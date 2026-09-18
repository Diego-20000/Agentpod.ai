# AgentPod — Tipos de Pod

Tres tipos de entorno, mismo motor de aprovisionamiento (Hetzner Cloud API), distinta capa de acceso.

## 1. Servidor Normal (Dev Pod)
- **Qué es**: pod genérico en la nube, acceso por SSH y terminal web (xterm.js). Sin nada especial de agentes.
- **Para qué sirve**: correr código, tests, builds, cualquier proceso pesado que el dev no quiere en su máquina local.
- **Acceso**: terminal web + SSH key. Usuario entra y labura como en cualquier VPS.
- **Target**: dev que solo quiere descargar carga de su compu, sin usar agentes todavía.

## 2. Servidor MCP Terminal
- **Qué es**: el mismo pod, pero corriendo un **MCP server** que expone herramientas de shell/filesystem/git al agente (Claude Code, LangChain, etc.) vía protocolo MCP.
- **Para qué sirve**: el agente ejecuta comandos, lee/escribe archivos, corre tests — todo por texto, sin interfaz visual. Es el caso de uso central del pitch original (agentes que corren código arbitrario, aislados).
- **Acceso**: terminal web (para el humano) + endpoint MCP (para el agente/cliente MCP tipo Claude Desktop, Cursor, etc.).
- **Target**: dev que ya usa Claude Code/agentes y quiere que corran aislados, no en su máquina.

## 3. MCP Visual (Computer Use)
- **Qué es**: pod con MCP server + entorno gráfico (Xorg/VNC) + streaming WebRTC/noVNC a 60 FPS. El agente controla un navegador o GUI real (mouse, teclado, screenshots) vía MCP tools tipo `computer_use`.
- **Para qué sirve**: agentes que necesitan "ver" y operar una interfaz visual (navegar sitios sin API, llenar formularios, testear UI, usar apps de escritorio).
- **Acceso**: terminal web + endpoint MCP + stream de escritorio embebido (el humano puede mirar/tomar control en cualquier momento).
- **Target**: casos más avanzados — automatización de tareas visuales, QA de UI, agentes tipo "operador".

## Mapeo con planes (docs/MASTER_SPEC.md §7)

| Plan | Tipo de pod incluido |
|---|---|
| Starter ($15/mes) | Servidor Normal o MCP Terminal (1 pod CX22) — el usuario elige el modo al crear el pod |
| Pro ($39/mes) | MCP Terminal o MCP Visual (1 pod CPX22, necesita más recursos para GUI) |
| Team ($99/mes) | Mezcla de los 3 tipos, hasta 3 pods concurrentes |

**Nota de costos**: el MCP Visual consume más CPU/RAM (por el entorno gráfico + streaming), por eso solo está disponible desde CPX22 en adelante (Pro/Team). Servidor Normal y MCP Terminal corren bien en CX22 (Starter).

## Prioridad de construcción (alineado a validación pre-venta)
No se construye ninguno de los 3 hasta cerrar el umbral de ≥3 pre-ventas (ver `VALIDATION_PLAN.md`). Cuando se valide, el orden de MVP es:
1. Servidor Normal (el más simple, prueba el aprovisionamiento Hetzner + terminal web).
2. Servidor MCP Terminal (agrega la capa MCP — es el diferencial real del producto).
3. MCP Visual (el más caro y complejo, se deja para después de tener clientes pagando en los primeros dos).
