# AgentPod — Operaciones del Producto

Decisiones sobre cómo funciona el producto día a día, más allá del pitch. Investigado contra prácticas reales de la industria (Fly.io/Railway/Render en resize y persistencia de datos; estándares de dunning/grace period en SaaS).

## 1. Qué viene instalado por defecto
Imagen base **Ubuntu 24.04 LTS** (corregido de 22.04: soporte hasta 2029 vs. 2027, no vale la pena empezar con la versión que vence antes) con: git, Docker (para que el cliente corra sus propios contenedores, y para aislar agentes visuales — ver `PRODUCT_TYPES.md`), Python3, Node/npm, y **Claude Code preinstalado y pre-configurado** apuntando al MCP server local. Esto es lo que sostiene la promesa de "menos de 60 segundos" — no "instalá esto primero" (corregido, ver `MASTER_SPEC.md` §2).

**Cómo se construye la imagen (Fase 1, sin equipo de infra)**: no usamos Packer ni una imagen Docker como base del pod — es una capa que no hace falta con 1-3 clientes. En cambio: Ubuntu 24.04 oficial + **cloud-init** (soportado nativo por Hetzner vía `user_data`, límite 32 KiB) + un `bootstrap.sh` versionado en Git que instala Docker/Python/Node/Claude Code/MCP/A2A/firewall interno/healthcheck. El cloud-init solo crea el usuario, configura SSH, descarga el bootstrap y lo corre — nada de YAML gigante.

En MCP Visual, además: Xvfb, noVNC, Chromium (uno por contenedor de agente).

El MCP server, el A2A server y el agente de OpenTelemetry corren como servicios de sistema (systemd) desde que el pod arranca — el cliente no los levanta a mano.

## 1.5 Flujo de provisioning (paso a paso, con manejo de fallos)
Arquitectura mínima para Fase 1: `Polar → webhook → DB → worker chico → Hetzner API → pod`. El worker puede ser un proceso simple que consulta una tabla `provision_jobs` — no hace falta Redis ni una cola administrada para 1-10 clientes.

1. **Webhook de Polar** (evento `order.paid`, no el redirect del checkout): valida la firma, chequea si ese `event_id` ya se procesó (evita duplicar el pod si Polar reintenta el mismo webhook), y si es nuevo crea un `provision_job` con estado `pending`. El webhook responde rápido y **nunca crea el servidor dentro de ese request**.
2. **Se crea el registro del pod en la DB antes de tocar Hetzner**: `pod_id` (UUID propio) con estado `provisioning`, `server_id = NULL`. Ese `pod_id` se usa como label en Hetzner (`agentpod_id=<uuid>`) — es la idempotency key real.
3. **Se crea el firewall del pod primero** (inbound: solo 443 permitido, todo lo demás cerrado; outbound: permitido pero monitoreado al inicio). Si esto falla, se marca el job `failed` y no se llega a crear ningún servidor.
4. **Se crea el servidor** con firewall + labels + `user_data` (cloud-init) en la misma llamada a Hetzner. **Se guarda el `server_id` inmediatamente** al recibir la respuesta — no esperar a que el pod esté listo para guardarlo, así se puede recuperar de casi cualquier problema posterior.
5. **Si la llamada da timeout** (el caso peligroso: no se sabe si Hetzner la recibió): nunca reintentar el POST de una. Primero consultar `GET /servers?label_selector=agentpod_id=<uuid>`. Si aparece un servidor, se usa ese. Si aparecen dos, se conserva uno y se borra el otro. Si no aparece ninguno después de un margen chico, ahí sí se reintenta una vez. Esto evita el clásico "timeout → retry → dos servidores → dos facturas".
6. **Se espera el resultado de la Action** de Hetzner (sin polling agresivo) hasta `success` o `error`.
7. **Health check real antes de entregar acceso**: que Hetzner diga "servidor creado" no significa que el pod funcione. Se espera a que cloud-init termine, MCP responda, A2A responda, y un endpoint interno `GET /healthz` devuelva OK en las 4 partes. Hasta que eso no pase, **el cliente no recibe el token ni el acceso**.
8. **Si cloud-init falla antes de entregar el pod** (todavía sin datos del cliente): se marca `failed`, se borra el servidor y el firewall, y se recrea limpio — no hay nada que preservar. **Si falla después de haber entregado el pod** es otra historia (ahí sí hay datos del cliente) — por eso el primer pod de cada cliente conviene tratarlo con cuidado, probando la misma imagen uno mismo antes de dársela a un cliente real.

## 2. Seguridad y acceso

**Tokens MCP (mecanismo concreto, Fase 1):**
- **Gate previo a emitir cualquier token: email verificado.** Login social (Google/GitHub) ya llega verificado por el proveedor, así que en la práctica el gate se cumple solo con el login actual (`PAGE_CONTENT.md`); si más adelante se agrega login con email/password propio, se bloquea la generación del token hasta confirmar el email (link a `/verify-email?token=...`). Mismo patrón que Stripe/GitHub: se puede tener cuenta sin verificar, no se puede sacar credenciales de API.
- **El token no es de un solo uso**: es una credencial de API persistente (igual que un API key de Stripe/GitHub), válida para todas las conexiones MCP del pod hasta que se rote — no se regenera en cada conexión ni "se gasta" al usarla. Lo único que ocurre una sola vez es la vista en texto plano en pantalla (ver punto de entrega abajo); si se pierde, se rota, no se "recupera".
- Formato: `ap_sk_live_` + 32 bytes aleatorios (`secrets.token_urlsafe`) — prefijo reconocible, si se filtra a un repo público las herramientas de escaneo de secretos de GitHub lo detectan solas.
- Guardamos solo `sha256(token)` en la DB; el token en texto plano **nunca** se persiste ahí.
- Durante el provisioning, el token entra al `user_data` de cloud-init solo para configurar el servicio MCP; el bootstrap lo deja en `/etc/agentpod/mcp.token` con `chmod 600`, dueño del usuario del servicio — nunca como variable de entorno visible en un `systemd` genérico.
- **Regla dura**: el pod puede tener el secreto MCP del cliente. **Nunca** un secreto del control plane (token de la API de Hetzner, secret de webhook de Polar, password de la DB) — esos viven solo en el backend, jamás dentro de un pod.
- **Entrega al cliente (Fase 1, sin dashboard automático todavía)**: por un link de un servicio de secretos con cifrado de punta a punta y expiración (ej. Bitwarden Send), configurado para expirar en 1 hora y máximo 1 acceso. Nunca por email plano, ticket, Discord, o pegado en un log.
- Rotación instantánea desde el dashboard (cuando exista): el viejo se invalida al momento.

**Transporte y firewall:**
- Todo por HTTPS, nunca texto plano.
- Firewall por defecto: todo cerrado excepto el endpoint MCP/A2A (443, con token).
- SSH apagado por defecto; si se activa, solo por clave, nunca por contraseña.

**Contra abuso (esto protege al negocio entero, no solo al cliente):**
El pod expone `shell.exec` — es ejecución remota de código real. Si un agente (propio o comprometido) lo usa para spam, ataques a terceros, o minado de cripto, **Hetzner puede suspender no solo ese pod, sino toda nuestra cuenta** — el riesgo no es del cliente, es nuestro.

Hetzner no publica números concretos de límite (no hay "tantos paquetes por segundo" oficial) — su política es cualitativa (spam y ataques prohibidos) y lo que sí publican son **tiempos de respuesta ante un aviso de abuso**: 6hs si es escaneo de puertos, 24hs si es ataque de red, 48hs si es abuso de email. Pasado ese plazo sin que respondamos, ellos bloquean la IP directamente.

**Nuestro margen de seguridad**: en vez de esperar a que Hetzner nos avise y tengamos esas horas para reaccionar, apuntamos a objetivos internos mucho más rápidos: señal crítica → alerta en <1 min, alerta → cuarentena en <2 min, cuarentena → servidor apagado en <3 min. Bien por debajo de la ventana más chica de Hetzner (6hs). Nunca llegamos a que Hetzner tenga que intervenir.

- Límite de tasa por token (protege también contra un agente en loop infinito).
- **Detección concreta (Fase 1, sin ML ni SIEM)**: **Netdata Agent** (open source, se corre local en cada pod, sin depender del tier gratis limitado de Netdata Cloud) + un script chico en systemd timer cada 30s que lee conexiones (`ss -Htan`) y contadores de `nftables`. Se alerta por **combinación** de señales, nunca una sola (CPU alta sola no es abuso — un agente compilando usa 100% CPU normal; tráfico alto solo tampoco — un `git clone` grande es legítimo):
  - Muchas IPs destino distintas en poco tiempo (posible port scanning).
  - Conexiones salientes a puertos 25/465/587 (SMTP saliente **bloqueado por defecto** — se habilita a mano si un cliente lo necesita de verdad, corta de entrada uno de los abusos más comunes).
  - Explosión de conexiones concurrentes muy por encima de lo normal del pod.
  - Umbrales iniciales (ajustables con datos reales, no son límites oficiales de Hetzner): >200 conexiones concurrentes + crecimiento rápido = alerta; >50 IPs destino distintas en 60s = alerta fuerte; >500 conexiones concurrentes = alerta crítica.

**Suspend Pod — qué hace en concreto (no es "pause", es cuarentena real):**
1. Firewall del pod a `INBOUND=DENY ALL / OUTBOUND=DENY ALL` (vía Hetzner API) — así, aunque el servidor reinicie por cualquier motivo, sigue aislado.
2. `shutdown` (apagado gracioso) vía Hetzner API.
3. Si sigue corriendo ~30s después, `poweroff` (forzado — puede perder datos, se usa solo cuando hay que cortar ya).
4. **Nunca se borra** como parte de Suspend — primero aislar, después investigar, después decidir si se borra.
- Log de auditoría de cada uso del token (vía OpenTelemetry, ya definido en `PRODUCT_TYPES.md`) — sirve tanto para que el cliente vea qué hizo su agente como para detectar mal uso.
- Términos de servicio que prohíben expresamente ese uso, con derecho a suspender sin previo aviso ante abuso detectado.

**Login del dashboard**: solo OAuth (Google/GitHub) — nunca password propia. Menos superficie de ataque, no guardamos credenciales de nadie.

## 3. Lifecycle, acceso y salud — 3 ejes separados, no un solo `status`
**Corrección de diseño**: mezclar lifecycle + salud + billing en un solo campo `status` termina en estados absurdos (`restricted_resizing_degraded`). Se separan en 3 campos independientes:

```
lifecycle_status: provisioning | running | resizing | archiving | archived | failed | deleted
access_status:    active | restricted
health_status:    healthy | degraded
```

Un pod puede estar perfectamente en `lifecycle=running, access=restricted, health=healthy` — significa "la infra existe y está sana, pero el cliente no puede usarla" (por falta de pago o abuso). Ejemplo real: cliente no paga → `access=restricted` sin tocar `lifecycle` ni `health`.

**Transiciones válidas de `lifecycle_status`:**
- `provisioning → running` (éxito) o `provisioning → failed` (error permanente). Nunca `provisioning → archived` directo — no hay nada que archivar todavía.
- `running → resizing → running` (éxito) o `resizing → running` con `resize_status=failed` si algo salió mal — no se bloquea el pod por un resize fallido, solo se re-consulta el spec real activo en Hetzner y se refleja (si no se puede determinar con certeza cuál spec quedó activo, `health=degraded` + alerta al fundador).
- Un healthcheck fallido **no es un lifecycle nuevo**, es `health=degraded` mientras `lifecycle` sigue en `running`. Si no se recupera, alerta a un humano — no se borra el pod automáticamente.
- `running → archiving → archived`: nunca `archiving → deleted` directo, el snapshot es la red de seguridad, siempre se pasa por `archived` primero.
- `archived → provisioning → running`: restauración manual en Fase 1 (no automática todavía).

**Corrección importante (confirmado en la FAQ de Hetzner)**: apagar un servidor **NO detiene la facturación** — Hetzner cobra mientras el servidor exista, prendido o apagado. Solo **borrar** el servidor (pasar a `archived`) detiene el costo real.

## 3.5 Reconciliación DB ↔ Hetzner (contra la hemorragia silenciosa de dinero)
Un worker corre **cada 5 minutos** (más una pasada diaria más lenta como control extra) y compara por `agentpod_id` (label en Hetzner) los servidores reales contra la tabla `pods` (excluyendo `deleted`).

- **Servidor en Hetzner sin pod en la DB (huérfano, nos está costando plata)**: nunca se borra en la primera detección — se crea un incidente, se alerta al fundador, y se espera una segunda detección consecutiva 5 min después (por si fue una inconsistencia transitoria, ej. una migración a mitad de camino). Si persiste: firewall deny-all + `shutdown` automático (`quarantined_orphan=true`), pero **el borrado final es manual**. Regla: automático para contener costo/riesgo, manual para destruir datos.
- **Pod en la DB en `provisioning` sin servidor en Hetzner**: se espera hasta 15 min (el worker puede reintentar el job). Pasado ese plazo: alerta al fundador, `health=degraded`, no se crean servidores de más.
- **Pod en `running` sin servidor en Hetzner** (alarma roja — pudo ser borrado, corrupción de estado, o error de API): re-consulta inmediata; si sigue sin aparecer, alerta a un humano, `lifecycle=failed`, `access=restricted`. **Nunca se crea un reemplazo automático** — el cliente puede tener datos importantes, la decisión es manual.

**Alertas de fuga de dinero, automáticas:**
- Cada 5 min: si existe servidor en Hetzner **y** no hay suscripción/entitlement activo en Polar **y** `lifecycle != archived/deleted` → alerta crítica con el costo estimado por día.
- `access=restricted` por más de 2 horas → warning (seguimos pagando Hetzner por un pod que no genera ingreso — por esto el dunning de "Si no paga" abajo es mucho más corto que lo que se había puesto antes).
- `provisioning`/`resizing` por más de 15 min, o `archiving` por más de 30 min → warning/crítico (son estados que deberían durar minutos).
- **Si el cliente paga pero no usa el pod**: no se apaga automáticamente — pagar por una máquina persistente aunque se use poco es legítimo. Solo se manda un resumen informativo semanal ("tu pod lleva 7 días sin actividad de agente"), nunca una acción automática.

## 4. Resize
Mecanismo completo, no solo "se apaga y prende":
1. Cliente pide resize desde el dashboard → avisamos "esto apaga tu pod ~2 min".
2. Antes de apagar, se manda una señal a los servicios (MCP/A2A) para que el agente pueda frenar en un punto seguro, no cortarlo a mitad de tarea sin avisar.
3. Apagamos vía Hetzner API → cambiamos el `server_type` → prendemos.
4. Los servicios (MCP, A2A, OTel, los contenedores Docker de agentes visuales) arrancan solos al boot (systemd + `restart: always` en Docker) — el cliente no reconfigura nada.
5. **Caveat real de Hetzner**: el disco solo puede crecer, nunca achicarse. Si el cliente baja de spec, el disco se queda con el tamaño viejo (más grande) — hay que mostrarlo en el dashboard para que no se sorprenda con el costo de disco.
6. **Facturación (Fase 1, con Polar de verdad, no cálculo a mano)**: cada spec de Hetzner (CX23, CPX22, etc.) tiene su propio **producto de Polar** ya creado, con precio fijo igual al que calculamos en `MASTER_SPEC.md` §7 (metadata: `{"agentpod_type": "mcp_terminal", "hetzner_server_type": "cx23"}`). Un resize es un `PATCH /v1/subscriptions/{id}` cambiando `product_id` al del nuevo spec, con `proration_behavior=invoice` (upgrade: se cobra la diferencia ya) o `proration_behavior=next_period` (downgrade: por simplicidad, el precio menor arranca en la próxima renovación en vez de generar crédito inmediato). Polar calcula el prorrateo, no lo armamos a mano. **Nota sobre el pricing dinámico de `MASTER_SPEC.md` §7**: el precio se sigue calculando en vivo contra la API de Hetzner para armar/actualizar el catálogo de productos de Polar — pero en el momento del cobro, cada spec ya es un producto fijo de Polar, no un cálculo por transacción.

## 5. Si no paga
Nunca se borra de una — es la práctica estándar en SaaS (evita perder al cliente para siempre por un pago que falló por error, ej. tarjeta vencida). Pero el dunning tiene que ser **corto**, porque cada día en `restricted` seguimos pagándole a Hetzner sin cobrar:
- Día 1 de atraso: aviso por mail, `access=active` todavía.
- Día 3: `access=restricted` (el cliente no puede usarlo, pero el servidor de Hetzner sigue existiendo y sigue costando).
- Día 7 (no día 14): sigue `restricted`, último aviso.
- Día 10 sin pago (no día 30): `lifecycle=archiving` → snapshot 7 días → `archived` (borrado real del servidor — recién ahí se deja de pagarle a Hetzner por ese pod). Un micro-SaaS con infra variable no puede sostener 27+ días de un servidor sin cobrar.

## 5.5 Provisioning falla después de cobrar (con Polar como merchant of record)
Si Polar confirma el pago pero Hetzner rechaza la creación del servidor, se clasifica el error:
- **Retryable** (timeout, falla temporal de la API, capacidad transitoria): `provision_job=retrying`, la suscripción queda activa, se reintenta.
- **Non-retryable, verificado** (límite de cuenta en Hetzner, configuración inválida): `pod=failed`, `access=restricted`, y se dispara automáticamente: `POST /v1/refunds` (reason=`service_disruption`) + revocar/cancelar la suscripción en Polar (nunca solo el reembolso sin cortar el ciclo de cobro futuro). Se espera la confirmación real vía el webhook `order.refunded` de Polar antes de asumir que terminó.
- **Detalle económico real**: Polar no devuelve el fee de la transacción original al hacer un refund — un reembolso por provisioning fallido puede dejar una pequeña pérdida neta. Por eso el refund automático es solo para fallas **no-retryable y verificadas**, nunca ante cualquier timeout.
- **Email al cliente** (específico, no genérico): explica qué se intentó, que no se creó ningún servidor ni se dio acceso a nada, que ya se canceló la suscripción y se pidió el reembolso completo vía Polar, y que no tiene que hacer nada de su parte.

## 6.5 Automático vs. humano — la línea concreta
**Automático**: webhook duplicado, reconciliación, cuarentena de huérfano, retry de provisioning transitorio, reintentos de healthcheck, refund de provisioning permanentemente fallido, restricción por no pago.
**Humano**: borrado definitivo de un huérfano, recrear un pod con datos del cliente, resolver una inconsistencia DB/Hetzner, abuso confirmado, restauración complicada, cualquier error ambiguo.

## 6. Templates
Para el MVP, no. Un solo pod base bien armado (Claude Code + MCP + A2A listos) alcanza. Templates específicos ("agente scraper", "agente QA") se dejan para después de tener uso real — construirlos sin saber qué arma la gente es adivinar.
