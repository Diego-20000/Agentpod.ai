# AgentPod — Análisis competitivo

Investigado y verificado (spot-check de los hechos más importantes: Fly.io Sprites existe con esas características; pricing de Maritime confirmado por su página oficial). Segunda opinión externa, cruzada contra fuentes reales.

## Conclusión principal
**"Una VM persistente para correr Claude Code" ya no es un hueco vacío.** Fly.io Sprites, Maritime y Railway Cloud Agents ya resolvieron gran parte de la propuesta original. Esto no invalida el proyecto, pero cambia qué se vende:

**No**: "un sandbox aislado para agentes" (comoditizado).
**Sí**: "una computadora cloud persistente y administrada para tu agente, con infraestructura dedicada por cliente y precio mensual predecible".

## Los 3 competidores que más importan

**Fly.io Sprites** — el más fuerte técnicamente. Filesystem persistente, checkpoint/restore, sleep/wake, políticas de egress por dominio/CIDR, MCP oficial, "connectors" donde la credencial del cliente nunca entra a la VM. Le falta: no está empaquetado como producto terminado (sigue siendo infra-first), no tiene A2A como eje central. Costo aproximado de un Sprite 2vCPU/4GB always-on: ~$230-280/mes — mucho más caro que nuestro CX23 ($21.08/mes).

**Maritime** — el más parecido comercialmente. VM persistente por agente, BYOK real, dashboard, triggers (Telegram/email/cron), computer use ya integrado. Pricing confirmado: $20/mes base (20 agentes sleep/wake) + **$20/mes extra por agente si querés always-on**. Un primer agente always-on cuesta ~$40/mes ahí — nuestro CX23 da más specs (2vCPU/4GB vs. su base 1vCPU/2GB) a mitad de precio.

**Railway Cloud Agents** — la amenaza "mainstream" (marca ya conocida por developers). VM persistente, sleep/wake, Claude Desktop conectado directo, hard usage caps. Todavía en beta ("Priority Boarding"), specs fijas por plan (no catálogo libre como el nuestro), y atado al ecosistema Railway — no es neutral de proveedor.

**Otros a vigilar, sin competir de frente**: E2B y Daytona (infra de sandboxing muy superior, pero filosofía "sandbox efímero para builders", no producto final — no competir en microVMs/enterprise security con $0 de capital), Modal/RunPod (GPU, no es el campo inicial), Replit/GitHub Codespaces/Gitpod-Ona (dev environments, no runtime de agentes 24/7), Agent37/AQ (competidores directos chicos, seguir pero no priorizar). **Anthropic mismo** ya ofrece Claude Code en la web + routines + self-hosted environments — por eso "Claude Code remoto" solo no alcanza como diferenciador; hay que ser neutral de proveedor (Claude/Codex/Gemini/OpenCode), no "Claude Code hosting".

## Los 5 pilares de diferenciación que quedan en pie
1. **Persistente, no por sesión**: "tu agente tiene una computadora, no una sesión."
2. **Economía predecible**: precio mensual fijo por spec, no medido por segundo/sandbox/storage.
3. **Neutral de proveedor**: Claude Code, Cursor, Antigravity CLI — no atado a un solo modelo ni a un ecosistema (a diferencia de Railway). (Corregido sep. 2026: Google discontinuó Gemini CLI el 18 jun 2026, reemplazado por Antigravity CLI — ver `COMPATIBILITY.md`.)
4. **Administrado de fábrica**: el diferencial no es "tenés una VM", es "no tenés que administrarla" (MCP, A2A, firewall, lifecycle, provisioning, logs, suspensión, billing ya resueltos).
5. **Barato para dejar corriendo**: mientras la competencia optimiza para sesiones cortas o cobra extra por always-on, nuestro pricing de Hetzner ya es más barato en always-on real que Maritime, Fly Sprites o Railway.

## 3 ajustes concretos a la Fase 1 (no romper lo ya definido, afinar)
1. **Golden image / snapshot en vez de cloud-init desde cero cada vez**: la primera imagen (Ubuntu + agentes + MCP + A2A + Netdata + systemd + firewall) se configura manualmente una vez, se toma un snapshot de Hetzner, y el provisioning parte de esa imagen en vez de instalar todo de nuevo en cada pod. Reduce puntos de falla en la cadena de `docs/OPERATIONS.md` §1.5.
2. **BYOK secrets, definido explícitamente antes de cobrar (no implícito)**: para Fase 1, la API key de IA del cliente (Anthropic/OpenAI/Google) vive **dentro del pod**, configurada por el cliente mismo al entrar — nunca en nuestra DB. Un secret broker tipo Daytona (la key nunca aparece dentro de la VM, se inyecta solo en la capa de egress) queda para Fase 2, no es necesario para el cliente #1.
3. **Hard usage caps de infraestructura**, no solo detección heurística de abuso: un límite duro de gasto/consumo por pod (inspirado en los hard caps de Railway) que corta o alerta automáticamente antes de que un pod barato se convierta en una máquina de minado corriendo semanas — complementa, no reemplaza, la detección de `docs/OPERATIONS.md` §2.

## Qué se degrada de prioridad (no se sacó, se re-ordena)
**A2A baja del pedestal comercial.** Sigue siendo parte de la Fase 1 técnica (`PRODUCT_TYPES.md`, Nivel 1), pero no es el motivo de venta: MCP ya está en varios competidores (Fly.io incluso tiene MCP oficial para Sprites), y "que un agente hable con otro" recién se vuelve un diferencial fuerte cuando AgentPod pueda coordinar múltiples pods de un mismo cliente de forma visible (Mesh, Fase 3) — no antes.

## Qué NO se construye ni se compite de frente (con $0 y un solo founder)
Sandboxing a nivel microVM/kernel (E2B), infraestructura serverless/GPU a escala (Modal), "AI app building" con editor+deploy+DB (Replit), GPU cloud (RunPod), ni pelear contra Anthropic en "quién tiene el mejor Claude Code". La pelea es "dónde corre el agente", no "quién es el mejor agente".
