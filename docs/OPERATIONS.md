# AgentPod — Operaciones del Producto

Decisiones sobre cómo funciona el producto día a día, más allá del pitch. Investigado contra prácticas reales de la industria (Fly.io/Railway/Render en resize y persistencia de datos; estándares de dunning/grace period en SaaS).

## 1. Qué viene instalado por defecto
Imagen base Ubuntu 22.04 LTS con: git, Docker (para que el cliente corra sus propios contenedores, y para aislar agentes visuales — ver `PRODUCT_TYPES.md`), Python3, Node/npm, y **Claude Code preinstalado y pre-configurado** apuntando al MCP server local. Esto es lo que sostiene la promesa de "5 segundos" — no "instalá esto primero".

En MCP Visual, además: Xvfb, noVNC, Chromium (uno por contenedor de agente).

El MCP server, el A2A server y el agente de OpenTelemetry corren como servicios de sistema (systemd) desde que el pod arranca — el cliente no los levanta a mano.

## 2. Seguridad y acceso

**Tokens MCP:**
- Se generan al crear el pod, con prefijo reconocible (`sk_live_...`, como Stripe/Anthropic) — si se filtra por accidente (ej: subido a un repo público), las herramientas de escaneo de secretos de GitHub lo detectan solas.
- Se muestran **una sola vez**; nosotros guardamos solo un hash, nunca el token en texto plano.
- Rotación instantánea desde el dashboard: el viejo se invalida al momento.

**Transporte y firewall:**
- Todo por HTTPS, nunca texto plano.
- Firewall por defecto: todo cerrado excepto el endpoint MCP/A2A (443, con token).
- SSH apagado por defecto; si se activa, solo por clave, nunca por contraseña.

**Contra abuso (esto protege al negocio entero, no solo al cliente):**
El pod expone `shell.exec` — es ejecución remota de código real. Si un agente (propio o comprometido) lo usa para spam, ataques a terceros, o minado de cripto, **Hetzner puede suspender no solo ese pod, sino toda nuestra cuenta** — el riesgo no es del cliente, es nuestro.

Hetzner no publica números concretos de límite (no hay "tantos paquetes por segundo" oficial) — su política es cualitativa (spam y ataques prohibidos) y lo que sí publican son **tiempos de respuesta ante un aviso de abuso**: 6hs si es escaneo de puertos, 24hs si es ataque de red, 48hs si es abuso de email. Pasado ese plazo sin que respondamos, ellos bloquean la IP directamente.

**Nuestro margen de seguridad**: en vez de esperar a que Hetzner nos avise y tengamos esas horas para reaccionar, nosotros mismos pausamos el pod sospechoso **dentro de los primeros 30 minutos** de detectar tráfico anómalo — bien por debajo de su ventana más chica (6hs). Nunca llegamos a que Hetzner tenga que intervenir.

- Límite de tasa por token (protege también contra un agente en loop infinito).
- Monitoreo de tráfico saliente: tráfico anómalo (spam, escaneo de puertos, patrones de ataque) pausa el pod automáticamente en <30 min.
- Log de auditoría de cada uso del token (vía OpenTelemetry, ya definido en `PRODUCT_TYPES.md`) — sirve tanto para que el cliente vea qué hizo su agente como para detectar mal uso.
- Términos de servicio que prohíben expresamente ese uso, con derecho a suspender sin previo aviso ante abuso detectado.

**Login del dashboard**: solo OAuth (Google/GitHub) — nunca password propia. Menos superficie de ataque, no guardamos credenciales de nadie.

## 3. Qué pasa con los datos (pausar/borrar)
- **Pausar**: Hetzner deja de facturar cómputo, el disco queda intacto — no se pierde nada.
- **Borrar**: antes de eliminar en serio, se guarda un snapshot automático por **7 días** (barato: $0.0199/GB/mes) como red de seguridad ante un borrado accidental. Pasado ese plazo, se purga en serio.
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
Nunca se borra de una — es la práctica estándar en SaaS (evita perder al cliente para siempre por un pago que falló por error, ej. tarjeta vencida).
- Día 1 de atraso: aviso por mail.
- Día 3: se **pausa** el pod (no se borra, el cliente no pierde nada pero no puede usarlo).
- Día 14: sigue pausado, más avisos (secuencia de dunning).
- Día 30 sin pago: se borra, con la misma ventana de 7 días de recuperación del punto 3.

## 6. Templates
Para el MVP, no. Un solo pod base bien armado (Claude Code + MCP + A2A listos) alcanza. Templates específicos ("agente scraper", "agente QA") se dejan para después de tener uso real — construirlos sin saber qué arma la gente es adivinar.
