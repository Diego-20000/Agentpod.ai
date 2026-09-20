# AgentPod — Operaciones del Producto

Decisiones sobre cómo funciona el producto día a día, más allá del pitch. Investigado contra prácticas reales de la industria (Fly.io/Railway/Render en resize y persistencia de datos; estándares de dunning/grace period en SaaS).

## 1. Qué viene instalado por defecto
Imagen base **Ubuntu 24.04 LTS** (corregido de 22.04: soporte hasta 2029 vs. 2027, no vale la pena empezar con la versión que vence antes) con: git, Docker (para que el cliente corra sus propios contenedores, y para aislar agentes visuales — ver `PRODUCT_TYPES.md`), Python3, Node/npm, y **Claude Code preinstalado y pre-configurado** apuntando al MCP server local. Esto es lo que sostiene la promesa de "menos de 60 segundos" — no "instalá esto primero" (corregido, ver `MASTER_SPEC.md` §2).

**Cómo se construye la imagen (Fase 1, sin equipo de infra)**: no usamos Packer ni una imagen Docker como base del pod — es una capa que no hace falta con 1-3 clientes. En cambio: Ubuntu 24.04 oficial + **cloud-init** (soportado nativo por Hetzner vía `user_data`, límite 32 KiB) + un `bootstrap.sh` versionado en Git que instala Docker/Python/Node/Claude Code/MCP/A2A/firewall interno/healthcheck. El cloud-init solo crea el usuario, configura SSH, descarga el bootstrap y lo corre — nada de YAML gigante.

En MCP Visual, además: Xvfb, noVNC, Chromium (uno por contenedor de agente).

El MCP server, el A2A server y el agente de OpenTelemetry corren como servicios de sistema (systemd) desde que el pod arranca — el cliente no los levanta a mano.

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

## 3. Qué pasa con los datos (pausar/borrar)
**Corrección importante (confirmado en la FAQ de Hetzner)**: apagar/pausar un servidor **NO detiene la facturación** — Hetzner cobra mientras el servidor exista, esté prendido o apagado. Solo **borrar** el servidor detiene el costo. Esto rompía el modelo de dunning que habíamos armado; se corrige con 3 estados:

- **`running`**: el pod funciona normal, se factura completo.
- **`restricted`** (ej: no pagó, o abuso detectado): el pod se apaga a nivel de acceso del cliente (no puede usarlo), pero **el servidor de Hetzner sigue existiendo y sigue costando** — por eso el plazo de gracia antes de pasar a `restricted` tiene que ser corto, y de `restricted` a `archived` no puede ser muy largo, porque cada día ahí seguimos pagando por un pod que no genera ingreso.
- **`archived`**: se genera el snapshot (retención 7 días, $0.0199/GB/mes) y **recién ahí se borra el servidor real** — ahí sí se deja de pagar a Hetzner.
- El backup completo automático (el +20% de Hetzner) sigue siendo upsell opcional, no viene incluido por defecto.

## 4. Resize
Mecanismo completo, no solo "se apaga y prende":
1. Cliente pide resize desde el dashboard → avisamos "esto apaga tu pod ~2 min".
2. Antes de apagar, se manda una señal a los servicios (MCP/A2A) para que el agente pueda frenar en un punto seguro, no cortarlo a mitad de tarea sin avisar.
3. Apagamos vía Hetzner API → cambiamos el `server_type` → prendemos.
4. Los servicios (MCP, A2A, OTel, los contenedores Docker de agentes visuales) arrancan solos al boot (systemd + `restart: always` en Docker) — el cliente no reconfigura nada.
5. **Caveat real de Hetzner**: el disco solo puede crecer, nunca achicarse. Si el cliente baja de spec, el disco se queda con el tamaño viejo (más grande) — hay que mostrarlo en el dashboard para que no se sorprenda con el costo de disco.
6. **Facturación**: como cambia de precio a mitad de mes, guardamos el historial de specs por pod y prorrateamos en la factura de Polar (días en spec A + días en spec B) — no se cobra de más ni de menos.

## 5. Si no paga
Nunca se borra de una — es la práctica estándar en SaaS (evita perder al cliente para siempre por un pago que falló por error, ej. tarjeta vencida). Usa los 3 estados del punto 3:
- Día 1 de atraso: aviso por mail, pod sigue `running`.
- Día 3: pasa a **`restricted`** (el cliente no puede usarlo, pero el servidor de Hetzner sigue existiendo y sigue costando — por eso este plazo es corto, no conviene mantenerlo así mucho tiempo).
- Día 14: sigue `restricted`, más avisos (secuencia de dunning).
- Día 30 sin pago: pasa a **`archived`** (snapshot 7 días + borrado real del servidor — recién ahí dejamos de pagarle a Hetzner por ese pod).

## 6. Templates
Para el MVP, no. Un solo pod base bien armado (Claude Code + MCP + A2A listos) alcanza. Templates específicos ("agente scraper", "agente QA") se dejan para después de tener uso real — construirlos sin saber qué arma la gente es adivinar.
