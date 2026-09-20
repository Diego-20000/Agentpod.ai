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

### Varios agentes visuales en un mismo pod: aislamiento real, no solo "no pisarse"
Separar pantallas virtuales (displays distintos) evita que dos agentes se peleen por el mismo mouse, pero **no alcanza**: si corren sueltos en el mismo sistema operativo, uno podría llegar a los archivos o procesos del otro — y eso contradice la promesa central del producto ("aislamiento total").

**Corrección: cada agente visual va en su propio contenedor (Docker), no solo en su propio display.**
- Cada contenedor tiene su pantalla virtual (Xvfb), su navegador, su mouse/teclado virtual (son eventos de software, nunca hardware real) y su propio filesystem — no puede tocar nada del contenedor vecino, aunque estén en el mismo pod físico.
- Un proxy chico adentro del pod expone el stream de cada contenedor en su propia URL (`/agente-1/`, `/agente-2/`); el dashboard deja elegir cuál mirar, nunca todos mezclados en una pantalla.

**Costo real de esto (no aproximado)**: cada sesión completa (Xvfb + navegador + streaming) necesita ~1.5-2GB de RAM para andar bien.
- CPX22 (4GB) → 1 agente visual cómodo.
- CPX32 (8GB) → 3-4 agentes visuales concurrentes.
- Más que eso: specs más grandes, o repartir en varios pods (ver Nivel 2 abajo).

## Herramientas de coordinación (para que los agentes rindan mejor, no solo "se hablen")
Investigado — esto es lo que la evidencia real de sistemas multi-agente dice que hace falta, más allá de darles un canal de chat.

**Nivel 1 — Mismo pod (default, sin costo extra, sin nada nuestro en el medio):**
Si el cliente mete varios agentes en un mismo pod, se comunican solos: mismo disco, misma red local. Con pocos agentes (2-4), hablan directo entre sí (A2A local + carpeta compartida `/shared/`, con subcarpeta por agente para no pisarse archivos — lo pesado como imágenes va por disco, referencia por mensaje). Con muchos agentes (10+), conviene un patrón de **supervisor**: uno coordina, el resto le reporta a él en vez de hablarse todos entre todos (evita que se enrede exponencialmente).

**Nivel 2 — Varios pods de un mismo cliente, conectados (opcional, pod por pod, nunca automático):**
Usamos **Hetzner Private Networks** — gratis, viene incluido, no está en el pricing de la API porque no tiene costo. Es una red privada tipo "cable" entre los pods de un cliente: se ven por IP interna, sin pasar por internet.

**Importante — no es automático ni todo-o-nada.** Al crear o editar un pod, el cliente elige, pod por pod: "¿conectar este pod con mis otros pods?" (on/off). Un pod que maneja algo sensible puede quedar aislado aunque el cliente tenga otros 5 pods conectados entre sí. Default: **desconectado**. El cliente decide activar la conexión, nunca al revés.

Sobre esa red, agregamos:
1. **A2A (Agent2Agent)** — protocolo abierto, Apache-2.0, sin costo de licencia, gobernado por la Linux Foundation junto con Anthropic, OpenAI, Google, Microsoft, AWS y Block. Permite que los agentes de pods distintos (ya conectados por la red privada) se descubran y se manden mensajes.
2. **Memoria compartida (Redis multi-tenant nuestro)** — evita que cada agente repita trabajo o se contradiga con otro. Solo hace falta cruzar pods porque dentro de un mismo pod ya comparten disco (Nivel 1).
3. **Observabilidad/trazabilidad (OpenTelemetry)** — sin esto, varios agentes coordinándose entre pods es una caja negra. Además es **requisito legal en la UE desde agosto 2026** (EU AI Act, Article 14). Es la base real de "AgentPod Mesh": el dashboard donde el cliente ve qué se dijeron y qué hicieron sus agentes, solo entre los pods que él eligió conectar.

## Pricing
No hay planes fijos — ver `MASTER_SPEC.md` §7. El usuario elige specs reales de Hetzner (CX/CPX/CAX para shared, CCX para dedicado) y el precio se calcula en vivo con markup. El MCP Visual necesita más CPU/RAM por el entorno gráfico + streaming, así que en la práctica conviene en specs desde CPX22 en adelante; MCP Terminal corre bien incluso en CX23 (la spec más chica).

## Prioridad de construcción (alineado a validación pre-venta)
No se construye ninguno hasta cerrar el umbral de ≥3 pre-ventas (ver `VALIDATION_PLAN.md`). Cuando se valide, el orden de MVP es:
1. MCP Terminal (el diferencial real del producto, y el más simple de los dos).
2. MCP Visual (el más caro y complejo, se deja para después de tener clientes pagando en el primero).
