# AgentPod — Flujo de conexión MCP: pantallas, de punta a punta

Dos caminos, **con mecanismos de credenciales distintos** (decisión de arquitectura verificada contra la spec real de MCP, ver nota al final). El mismo endpoint `/mcp` sirve a ambos — lo que cambia es la capa de autorización.

## Camino 1: Manual (token estático `ap_sk_live_`)

1. **Landing**: sin autenticación MCP todavía.
2. **Login/Signup** (OAuth Google/GitHub, ver `PAGE_CONTENT.md`): cuenta creada, **sin token MCP generado todavía** — tener cuenta no implica tener credencial MCP.
3. **Configurador**: elige tipo/agente/specs. Solo un aviso: "vas a recibir una configuración MCP cuando el pod esté listo". Sin endpoint ni token visibles.
4. **Checkout de Polar**: pantalla del proveedor, no nuestra.
5. **Provisioning** (`PAGE_CONTENT.md` #4): progreso visual (pago→servidor→firewall→servicios→MCP→A2A→health→acceso). **Acá se genera el token** (`ap_sk_live_` + hash en DB + inyección en `/etc/agentpod/mcp.token` durante el bootstrap), pero **el usuario todavía no lo ve** — se genera antes de que el MCP pueda aceptar conexiones, no es lo mismo que "entregarlo".
6. **"Tu Pod está listo"**: recién con `lifecycle=running, access=active, health=healthy`. Bloques: Terminal, y "Conectá tu Pod" con botones por cliente (Claude Code/Cursor/Gemini CLI) + "conexión avanzada".
7. **Pantalla "Conectar MCP"**: muestra el endpoint (`https://pod-x.agentpod.ai/mcp`, copiable) y el token enmascarado con botón **"Mostrar token una vez"** — ahí sí se revela completo, con advertencia de no compartirlo. Este es el momento real de entrega al usuario (generación ≠ entrega, ya definido en `OPERATIONS.md` §2).
8. **Entrega segura**: en vez de dejarlo visible permanentemente, un link de un solo acceso con expiración (Bitwarden Send u equivalente). Si el usuario vuelve después: "este token ya fue entregado" + botón "Rotar token", nunca "ver token" de nuevo.
9. **Asistente por cliente**: pasos filtrados solo para el cliente elegido (ej. solo los comandos de Claude Code), con botón "Probar conexión" que verifica que el pod responde, y "probalo desde Claude Code" con el prompt de prueba ("Listá las herramientas MCP disponibles").
10. **Conexión confirmada**: cliente, endpoint, estado, última conexión.

Después de la primera conexión, el lugar permanente de gestión es **Dashboard → Pod → MCP** (`PAGE_CONTENT.md` #6), no se vuelve a la pantalla de provisioning.

## Camino 2: Directorio de conectores (Claude/ChatGPT — OAuth, no token estático)

El proveedor (Anthropic/OpenAI) controla el directorio y la ficha del conector — no son pantallas nuestras. Nuestras pantallas empiezan cuando el proveedor redirige al flujo OAuth de AgentPod:

1. **Directory + ficha del conector** (100% del proveedor): usuario busca "AgentPod", ve descripción/tools, aprieta Connect/Install.
2. **AgentPod OAuth — login** (`auth.agentpod.ai/authorize`, nuestra): "Conectar AgentPod con Claude/ChatGPT" → login Google/GitHub o sesión ya activa.
3. **AgentPod OAuth — selección de pod** (nuestra, necesaria porque una cuenta puede tener varios pods): lista de pods con specs, elegir cuál conectar. Evita conectar el proveedor al pod equivocado.
4. **AgentPod OAuth — permisos** (nuestra, la pantalla más importante): explícito qué SÍ (ejecutar comandos, leer/escribir archivos, git, procesos) y qué NO (administrar cuenta, cambiar suscripción, ver tarjeta, crear/acceder otros pods). Botones Cancelar/Permitir acceso.
5. **Emisión del token OAuth** (backend, no pantalla): AgentPod emite un access token OAuth vinculado a usuario+cliente+pod+scopes — **nunca el `ap_sk_live_`**. El proveedor manda ese token OAuth como `Authorization: Bearer <oauth-access-token>` al mismo endpoint `/mcp`.
6. **Confirmación** (del proveedor): "AgentPod connected", vuelta a Claude/ChatGPT, habilitar en la conversación.

Nota ChatGPT: la autorización OAuth del conector es distinta de la aprobación de una acción puntual — ChatGPT puede seguir pidiendo confirmación antes de ejecutar algo específico, incluso con el conector ya conectado.

## Comparación

| Momento | Manual | Directorio |
|---|---|---|
| Descubre AgentPod | AgentPod | Claude/OpenAI |
| Login/selección de pod | AgentPod | AgentPod |
| Endpoint visible al usuario | Sí | No |
| `ap_sk_live_` visible | Sí, una vez | Nunca |
| Mecanismo | Token estático | OAuth 2.1 + PKCE |
| Revocación | Rotar token | Revocar OAuth |
| UI del proveedor de IA | No | Sí (directory + ficha) |

## Bloqueante real antes de someter a cualquier directorio

**Verificado**: la especificación de autorización de MCP exige OAuth 2.1 + PKCE + metadata RFC9728 (`/.well-known/oauth-protected-resource`) con separación estricta entre authorization server y resource server (el MCP server). No es una preferencia de diseño — es requisito del protocolo para clientes que hacen discovery de auth (como exige OpenAI para sus apps).

**No enviar AgentPod a revisión de Claude/ChatGPT hasta implementar y probar OAuth de punta a punta** (authorization server propio + endpoint de discovery + validación de issuer/audience/scopes). El camino manual (token estático) sigue funcionando igual mientras tanto — esto es exclusivamente un requisito para la Fase 2/3 de distribución por directorio, no bloquea nada del MVP.
