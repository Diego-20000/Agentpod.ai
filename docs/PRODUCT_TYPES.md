# AgentPod — Tipos de Pod

Dos tipos de entorno, mismo motor de aprovisionamiento (Hetzner Cloud API), distinta capa de acceso. Se sacó del catálogo el "Servidor Normal" (VPS pelado sin MCP): no tiene el diferencial real del producto — quien solo quiere cómputo sin agente va directo a Hetzner y no paga nuestro margen. Solo vendemos lo que integra MCP + A2A, no cómputo pelado.

## 1. Servidor MCP Terminal
- **Qué es**: el mismo pod, pero corriendo un **MCP server** que expone herramientas de shell/filesystem/git al agente (Claude Code, LangChain, etc.) vía protocolo MCP, **más soporte A2A** para que ese agente pueda descubrir y hablar con otros agentes (propios o de otros pods).
- **Para qué sirve**: el agente ejecuta comandos, lee/escribe archivos, corre tests — todo por texto, sin interfaz visual. Es el caso de uso central del pitch original (agentes que corren código arbitrario, aislados). Con A2A, además puede coordinarse con otros agentes en vez de trabajar solo.
- **Acceso**: terminal web (para el humano) + endpoint MCP (para el agente/cliente MCP tipo Claude Desktop, Cursor, etc.) + endpoint A2A (Agent Card publicada, para que otros agentes lo descubran).
- **Target**: dev que ya usa Claude Code/agentes y quiere que corran aislados, no en su máquina.

## 2. MCP Visual (Computer Use)
- **Qué es**: pod con MCP server + entorno gráfico (Xorg/VNC) + streaming WebRTC/noVNC a 60 FPS, **más soporte A2A** igual que el Terminal. El agente controla un navegador o GUI real (mouse, teclado, screenshots) vía MCP tools tipo `computer_use`, y puede coordinarse con otros agentes vía A2A.
- **Para qué sirve**: agentes que necesitan "ver" y operar una interfaz visual (navegar sitios sin API, llenar formularios, testear UI, usar apps de escritorio).
- **Acceso**: terminal web + endpoint MCP + endpoint A2A + stream de escritorio embebido (el humano puede mirar/tomar control en cualquier momento).
- **Target**: casos más avanzados — automatización de tareas visuales, QA de UI, agentes tipo "operador".

## Herramientas de coordinación (para que los agentes rindan mejor, no solo "se hablen")
Investigado — esto es lo que la evidencia real de sistemas multi-agente dice que hace falta, más allá de darles un canal de chat:

1. **A2A (Agent2Agent)** — activado por defecto en MCP Terminal y MCP Visual (protocolo abierto, Apache-2.0, sin costo de licencia, gobernado por la Linux Foundation junto con Anthropic, OpenAI, Google, Microsoft, AWS y Block). Permite que los agentes se descubran y se manden mensajes.
2. **Memoria compartida (Redis o similar)** — el problema #1 de los sistemas multi-agente reales: sin esto, cada agente repite trabajo, se contradice con otros, y gasta tokens re-explicando contexto que otro agente del mismo cliente ya tiene. Cada pod expone un store de contexto compartido entre los agentes de ese cliente (aislado de otros clientes).
3. **Observabilidad/trazabilidad (OpenTelemetry)** — sin esto, un grupo de agentes coordinándose es una caja negra que nadie puede auditar. Es además **requisito legal en la UE desde agosto 2026** (EU AI Act, Article 14 — trazabilidad obligatoria para sistemas de IA de riesgo), así que cualquier cliente europeo lo va a necesitar sí o sí. Esta es la base técnica real de "AgentPod Mesh": el dashboard donde el cliente ve en vivo qué se dijeron y qué hicieron sus agentes, no solo un chat bonito.

## Pricing
No hay planes fijos — ver `MASTER_SPEC.md` §7. El usuario elige specs reales de Hetzner (CX/CPX/CAX para shared, CCX para dedicado) y el precio se calcula en vivo con markup. El MCP Visual necesita más CPU/RAM por el entorno gráfico + streaming, así que en la práctica conviene en specs desde CPX22 en adelante; MCP Terminal corre bien incluso en CX23 (la spec más chica).

## Prioridad de construcción (alineado a validación pre-venta)
No se construye ninguno hasta cerrar el umbral de ≥3 pre-ventas (ver `VALIDATION_PLAN.md`). Cuando se valide, el orden de MVP es:
1. MCP Terminal (el diferencial real del producto, y el más simple de los dos).
2. MCP Visual (el más caro y complejo, se deja para después de tener clientes pagando en el primero).
