# AgentPod — Tipos de Pod

Tres tipos de entorno, mismo motor de aprovisionamiento (Hetzner Cloud API), distinta capa de acceso.

## 1. Servidor Normal (Dev Pod)
- **Qué es**: pod genérico en la nube, acceso por SSH y terminal web (xterm.js). Sin nada especial de agentes.
- **Para qué sirve**: correr código, tests, builds, cualquier proceso pesado que el dev no quiere en su máquina local.
- **Acceso**: terminal web + SSH key. Usuario entra y labura como en cualquier VPS.
- **Target**: dev que solo quiere descargar carga de su compu, sin usar agentes todavía.

## 2. Servidor MCP Terminal
- **Qué es**: el mismo pod, pero corriendo un **MCP server** que expone herramientas de shell/filesystem/git al agente (Claude Code, LangChain, etc.) vía protocolo MCP, **más soporte A2A** para que ese agente pueda descubrir y hablar con otros agentes (propios o de otros pods).
- **Para qué sirve**: el agente ejecuta comandos, lee/escribe archivos, corre tests — todo por texto, sin interfaz visual. Es el caso de uso central del pitch original (agentes que corren código arbitrario, aislados). Con A2A, además puede coordinarse con otros agentes en vez de trabajar solo.
- **Acceso**: terminal web (para el humano) + endpoint MCP (para el agente/cliente MCP tipo Claude Desktop, Cursor, etc.) + endpoint A2A (Agent Card publicada, para que otros agentes lo descubran).
- **Target**: dev que ya usa Claude Code/agentes y quiere que corran aislados, no en su máquina.

## 3. MCP Visual (Computer Use)
- **Qué es**: pod con MCP server + entorno gráfico (Xorg/VNC) + streaming WebRTC/noVNC a 60 FPS, **más soporte A2A** igual que el Terminal. El agente controla un navegador o GUI real (mouse, teclado, screenshots) vía MCP tools tipo `computer_use`, y puede coordinarse con otros agentes vía A2A.
- **Para qué sirve**: agentes que necesitan "ver" y operar una interfaz visual (navegar sitios sin API, llenar formularios, testear UI, usar apps de escritorio).
- **Acceso**: terminal web + endpoint MCP + endpoint A2A + stream de escritorio embebido (el humano puede mirar/tomar control en cualquier momento).
- **Target**: casos más avanzados — automatización de tareas visuales, QA de UI, agentes tipo "operador".

## Soporte A2A (Agent2Agent)
MCP Terminal y MCP Visual vienen con **A2A activado por defecto** (protocolo abierto, Apache-2.0, sin costo de licencia — gobernado por la Linux Foundation junto con Anthropic, OpenAI, Google, Microsoft, AWS y Block). Esto permite que los agentes de un cliente se descubran y se manden mensajes entre sí (multi-agente), y es la base de "AgentPod Mesh": un dashboard donde el cliente ve en vivo la conversación/coordinación entre sus agentes. El Servidor Normal no lo necesita porque no corre agentes.

## Pricing
No hay planes fijos — ver `MASTER_SPEC.md` §7. El usuario elige specs reales de Hetzner (CX/CPX/CAX para shared, CCX para dedicado) y el precio se calcula en vivo con markup. El MCP Visual necesita más CPU/RAM por el entorno gráfico + streaming, así que en la práctica conviene en specs desde CPX22 en adelante; Servidor Normal y MCP Terminal corren bien incluso en CX23 (la spec más chica).

## Prioridad de construcción (alineado a validación pre-venta)
No se construye ninguno de los 3 hasta cerrar el umbral de ≥3 pre-ventas (ver `VALIDATION_PLAN.md`). Cuando se valide, el orden de MVP es:
1. Servidor Normal (el más simple, prueba el aprovisionamiento Hetzner + terminal web).
2. Servidor MCP Terminal (agrega la capa MCP — es el diferencial real del producto).
3. MCP Visual (el más caro y complejo, se deja para después de tener clientes pagando en los primeros dos).
