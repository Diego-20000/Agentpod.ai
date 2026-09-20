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
- Límite de tasa por token (protege también contra un agente en loop infinito).
- Monitoreo de tráfico saliente: tráfico anómalo (spam, escaneo de puertos, patrones de ataque) pausa el pod automáticamente antes de que Hetzner actúe contra nuestra cuenta.
- Log de auditoría de cada uso del token (vía OpenTelemetry, ya definido en `PRODUCT_TYPES.md`) — sirve tanto para que el cliente vea qué hizo su agente como para detectar mal uso.
- Términos de servicio que prohíben expresamente ese uso, con derecho a suspender sin previo aviso ante abuso detectado.

**Login del dashboard**: solo OAuth (Google/GitHub) — nunca password propia. Menos superficie de ataque, no guardamos credenciales de nadie.

## 3. Qué pasa con los datos (pausar/borrar)
- **Pausar**: Hetzner deja de facturar cómputo, el disco queda intacto — no se pierde nada.
- **Borrar**: antes de eliminar en serio, se guarda un snapshot automático por **7 días** (barato: $0.0199/GB/mes) como red de seguridad ante un borrado accidental. Pasado ese plazo, se purga en serio.
- El backup completo automático (el +20% de Hetzner) sigue siendo upsell opcional, no viene incluido por defecto.

## 4. Resize
Hetzner permite cambiar de spec, pero el pod tiene que estar apagado durante el cambio (uno o dos minutos), y el disco solo puede crecer, nunca achicarse. Se comunica claro en el dashboard: "esto va a apagar tu pod ~2 min, tus datos no se tocan".

## 5. Si no paga
Nunca se borra de una — es la práctica estándar en SaaS (evita perder al cliente para siempre por un pago que falló por error, ej. tarjeta vencida).
- Día 1 de atraso: aviso por mail.
- Día 3: se **pausa** el pod (no se borra, el cliente no pierde nada pero no puede usarlo).
- Día 14: sigue pausado, más avisos (secuencia de dunning).
- Día 30 sin pago: se borra, con la misma ventana de 7 días de recuperación del punto 3.

## 6. Templates
Para el MVP, no. Un solo pod base bien armado (Claude Code + MCP + A2A listos) alcanza. Templates específicos ("agente scraper", "agente QA") se dejan para después de tener uso real — construirlos sin saber qué arma la gente es adivinar.
