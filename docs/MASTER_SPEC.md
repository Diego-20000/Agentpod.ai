# AgentPod — Project Master Specification

## 1. Perfil del Fundador y Proyecto
- Fundador: Technical Solo-Founder (18 años, Argentina).
- Nivel de Inversión Inicial Propia: $0 USD (validación y desarrollo con herramientas 100% gratuitas).
- Visión: Plataforma de infraestructura en la nube de referencia para ejecutar, monitorear y gestionar agentes de IA (Claude Code, MCP, LangChain, AutoGPT) en entornos aislados y dedicados.

## 2. Definición del Producto (SaaS)
- **Problema**: Los agentes de IA consumen recursos masivos de CPU/RAM en las computadoras locales de los desarrolladores y representan riesgos de seguridad al ejecutar código arbitrario en el sistema operativo local.
- **Solución**: Entornos dedicados en la nube sobre servidores de Hetzner, desplegados con 1 clic.
- **Características clave**:
  - Despliegue rápido vía Hetzner Cloud API (objetivo MVP: **menos de 60 segundos**; no prometemos "5 segundos" hasta medir p50/p95 reales con el sistema completo — server + boot + cloud-init + servicios + MCP/A2A + terminal + health checks).
  - Terminal web integrada en tiempo real (xterm.js).
  - Streaming de escritorio remoto en la nube a 60 FPS (WebRTC / noVNC).
  - **Aislamiento por VM entre clientes** (cada cliente en su propia VM de Hetzner, nunca comparte servidor con otro cliente) **+ aislamiento de workloads dentro de un mismo pod** (contenedores Docker separados por agente). No es "aislamiento total" en sentido absoluto de seguridad: varios workloads igual comparten kernel dentro de la misma VM, y un escape de contenedor es un riesgo real a mitigar, no algo ya resuelto.

## 3. Esquema de Inversión y Formato Legal
- Monto de la ronda pre-seed: **$265,000 USD** (recalculado para 24 meses de runway real, ver §4).
- Instrumento legal: SAFE (formato estándar Y Combinator).
- Cobro: Crypto (USDC/USDT) a billetera corporativa/Lemon Cash con justificación de SAFE, o transferencia internacional (Wire/SWIFT) vía C-Corp en Delaware (Mercury/Wise).
- Canales de búsqueda 100% asincrónicos (sin videollamadas): Wefunder, Republic (crowdfunding de equity), Platanus Ventures (entrevista por chat, no video), TinySeed / Earnest Capital / Calm Company Fund (fondos para SaaS solo-founder, proceso por escrito), AngelList/Wellfound.
- **Nota importante**: la compra de un departamento propio (vivienda) NO forma parte del uso de fondos de la ronda — es gasto personal, separado, a pagar con ingresos propios cuando el negocio facture. Incluir gastos personales no declarados en el uso de fondos de un SAFE es mal uso de capital de riesgo y un riesgo legal/reputacional real. El runway sí incluye **alquiler**, que es gasto de vida legítimo.

### Encuadre legal para cobrar el primer cliente (antes de cualquier SAFE/Delaware)
**No hace falta constituir una empresa (ni Delaware ni ninguna) para tener los primeros clientes pagando.** Se puede operar como **persona física** en Argentina: nombre legal completo + CUIT + domicilio + encuadre fiscal (monotributo u otro régimen, a determinar con un contador argentino) + facturación electrónica correspondiente (ARCA exige comprobante tipo C o E según el caso para monotributistas). "AgentPod" es el nombre comercial del servicio; el contratante real es la persona física — no hay separación de patrimonio automática sin una sociedad, algo a tener presente pero que no bloquea arrancar.

**No usar "AgentPod Inc." / "AgentPod LLC" en ningún documento hasta que esa entidad exista de verdad.**

**Bloqueante real a chequear antes de cobrar (no Delaware "por si acaso")**: los Master Services Terms de Polar (el proveedor elegido como Merchant of Record, ver §7) exigen que el proveedor mantenga a su costo seguro de responsabilidad civil general, profesional (errores y omisiones) y **cyber liability**, con certificado disponible a pedido. Hay que verificar directamente en la cuenta de Polar si esto aplica igual a una cuenta chica/nueva y qué cobertura exigirían en la práctica — puede ser un costo real no contemplado todavía en el presupuesto.

**Checklist legal mínimo antes de cobrar**: nombre legal real + CUIT/encuadre fiscal + domicilio + los 4 documentos de `docs/legal/` (Términos de Servicio, Acceptable Use Policy, Privacy Policy, Refund Policy, con placeholders reemplazados) + Botón de Arrepentimiento y Botón de Baja de Servicio visibles desde el primer acceso (obligatorio desde la Disposición 954/2025) + revisión de los Supplier Terms de Polar.

## 4. Uso de Fondos ($265,000 USD, 24 meses)

| Categoría | Detalle | Monto (USD) | % |
|---|---|---|---|
| Runway del fundador | Alquiler + gastos de vida, $4,200 USD/mes × 24 meses | $100,800 | 38.0% |
| Equipamiento hardware | ThinkPad P16 Gen 3 (Docker/backend), MacBook Pro 16" M4 Max (Xcode/iOS), Galaxy S26 Ultra (pruebas Android/PWA), iPhone 17 Pro Max (pruebas iOS/Safari) | $26,900 | 10.2% |
| Equipo (devs) | Ayuda para construir la app de administración | $30,000 | 11.3% |
| Contenido orgánico | 1 persona part-time, $800/mes × 24 | $19,200 | 7.2% |
| Marketing & publicidad | Ads, prensa tech, pago a creadores | $35,000 | 13.2% |
| Infraestructura & cloud | Servidor central, dominios, SSL, pool de prueba en Hetzner | $18,000 | 6.8% |
| Software & IAs | ChatGPT Pro ($200) + API Claude ($200) + Figma/otras (~$40), 24 meses | $10,560 | 4.0% |
| Contingencia (10%) | Imprevistos | $24,046 | 9.1% |
| **TOTAL** | | **~$264,506 ≈ $265,000** | **100%** |

## 5. Stack Tecnológico de Desarrollo
- IA & razonamiento: ChatGPT Pro ($200/mo) + API Claude ($200/mo).
- Prototipado/demo: HTML + Tailwind CSS + JavaScript en Vercel desde GitHub.
- Infraestructura cloud: Hetzner Cloud API + contenedores Docker/LXC + streaming WebRTC.

## 6. Estrategia Go-To-Market & Pitcheo
- Canales orgánicos ($0): Reddit (r/ClaudeAI, r/LocalLLaMA), X/Twitter (#BuildInPublic), Hacker News (Show HN).
- Pitcheo 100% asincrónico: demo en vivo (Vercel), video Loom de 90s, pitch deck PDF (10 slides), comunicación escrita/audio.
- Plataformas target: Platanus Ventures, Y Combinator, Thiel Fellowship, Signal by NFX.

## 7. Pricing y Márgenes (real, API oficial de Hetzner)

**Proveedores confirmados:**
- Pagos/billing: **Polar** (Merchant of Record). **Corrección (sep. 2026): Polar cambió de tarifa el 27 de mayo de 2026.** Las cuentas nuevas (como la nuestra) arrancan en plan **Starter: 5% + $0.50 por transacción**, no el 4%+$0.40 "Early Member" que se había asumido antes (esa tarifa vieja solo la conservan cuentas creadas antes de esa fecha). Además, Polar suma **+1.5% en pagos con tarjeta internacional** — aplica prácticamente siempre en nuestro caso (cobramos en USD a clientes de cualquier país). **Tarifa real a usar: 6.5% + $0.50.**
- Facturación única: **Polar es la única fuente de verdad de billing.** Se elimina la idea de integrar Mercado Pago más adelante (estaba mencionada en `VALIDATION_PLAN.md` y contradice esto) — si en el futuro el mercado argentino lo justifica, se evalúa una capa aparte, pero no forma parte del plan de construcción actual.
- Infraestructura: **Hetzner Cloud API** (aprovisionamiento de pods on-demand, cada pod = 1 VM propia por cliente, sin compartir servidor entre clientes).

**No hay planes fijos (Starter/Pro/Team).** El usuario elige specs (vCPU/RAM/disco) del catálogo real de Hetzner vía API, con precio calculado en vivo. Datos sacados en vivo de `GET /v1/pricing` y `GET /v1/server_types` (región fsn1, USD, sep. 2026).

**Reservas sobre el precio final:**
- Comisión Polar: **6.5% + $0.50** por transacción (corregido).
- Marketing: 10% (5% en línea dedicada).
- Soporte/operación: 5% (3% en línea dedicada).
- Contingencia: 5% (3% en línea dedicada).

**Costo real de un pod = servidor Hetzner + IP pública (IPv4, $0.60/mes, ya no viene gratis).** Otros costos de Hetzner que existen pero son opcionales (no van en el costo base): volumen extra $0.0767/GB/mes, backup automático +20% del precio del servidor, snapshot/imagen $0.0199/GB/mes.

### Shared (CX/CPX/CAX) — fórmula `precio = (costo_hetzner + IP + $0.50) / 0.36`, margen neto 37.5% **(recalculado con la tarifa real de Polar, 6.5%+$0.50)**

| Tipo | Specs | Costo Hetzner | + IP | Costo total | Precio cliente | Margen neto |
|---|---|---|---|---|---|---|
| CX23 | 2vCPU, 4GB, 40GB | $6.49 | $0.60 | $7.09 | $21.08 | $7.91 (37.5%) |
| CX33 | 4vCPU, 8GB, 80GB | $9.99 | $0.60 | $10.59 | $30.81 | $11.55 |
| CX43 | 8vCPU, 16GB, 160GB | $18.49 | $0.60 | $19.09 | $54.42 | $20.41 |
| CX53 | 16vCPU, 32GB, 320GB | $34.99 | $0.60 | $35.59 | $100.25 | $37.59 |
| CAX11 | 2vCPU ARM, 4GB, 40GB | $6.99 | $0.60 | $7.59 | $22.47 | $8.43 |
| CAX21 | 4vCPU ARM, 8GB, 80GB | $12.49 | $0.60 | $13.09 | $37.75 | $14.16 |
| CAX31 | 8vCPU ARM, 16GB, 160GB | $24.99 | $0.60 | $25.59 | $72.47 | $27.18 |
| CAX41 | 16vCPU ARM, 32GB, 320GB | $48.49 | $0.60 | $49.09 | $137.75 | $51.66 |
| CPX12 | 1vCPU, 2GB, 40GB | $13.49 | $0.60 | $14.09 | $40.53 | $15.20 |
| CPX22 | 2vCPU, 4GB, 80GB | $22.99 | $0.60 | $23.59 | $66.92 | $25.10 |
| CPX32 | 4vCPU, 8GB, 160GB | $41.99 | $0.60 | $42.59 | $119.69 | $44.88 |
| CPX42 | 8vCPU, 16GB, 320GB | $81.99 | $0.60 | $82.59 | $230.81 | $86.55 |
| CPX52 | 12vCPU, 24GB, 480GB | $118.99 | $0.60 | $119.59 | $333.58 | $125.09 |
| CPX62 | 16vCPU, 32GB, 640GB | $152.99 | $0.60 | $153.59 | $428.03 | $160.51 |

### Dedicado (CCX) — un poco más barato que AWS (precio = AWS equivalente × 0.92), reserva reducida (11%), **margen recalculado con Polar real**

| Tipo | Specs | Costo total (servidor+IP) | AWS equivalente | Precio cliente | Margen neto |
|---|---|---|---|---|---|
| CCX13 | 2vCPU, 8GB, 80GB | $51.09 | $70.08 (m6i.large) | $64.47 | $1.60 (**2.5%**) |
| CCX23 | 4vCPU, 16GB, 160GB | $102.09 | $140.16 (m6i.xlarge) | $128.95 | $3.80 (**2.9%**) |
| CCX33 | 8vCPU, 32GB, 240GB | $163.59 | $280.32 (m6i.2xlarge) | $257.89 | $48.67 (18.9%) |
| CCX43 | 16vCPU, 64GB, 360GB | $326.09 | $560.64 | $515.79 | $98.93 (19.2%) |
| CCX53 | 32vCPU, 128GB, 600GB | $630.09 | $1,121.28 | $1,031.58 | $220.47 (21.4%) |
| CCX63 | 48vCPU, 192GB, 960GB | $1,007.59 | $1,681.92 | $1,547.37 | $268.49 (17.4%) |

**Notas:**
- El margen 95-97% mencionado originalmente para "SaaS" no aplica: cada pod consume infra real de Hetzner (costo variable).
- Shared (CX/CPX/CAX) es el volumen principal: margen neto sano y consistente (~37.5%), ya con la tarifa real de Polar.
- **Alerta real**: con la tarifa corregida de Polar, **CCX13 y CCX23 (los tamaños dedicados más pedidos) quedan con margen casi nulo (2.5-2.9%)**, no ~5-6% como se había calculado antes. Prácticamente no vale la pena venderlos sueltos a ese precio — evaluar subir el precio en esos dos tamaños específicos, o directamente no ofrecerlos hasta CCX33 (que sí tiene margen sano, ~19%).
- Se eligió no ser "mucho más barato" que AWS en CCX porque el margen no lo permite con los precios actuales de Hetzner (subieron 2-2.7x en junio 2026); apenas 8% debajo alcanza para competir sin regalar margen.
- Backup automático (opcional, +20% del precio del servidor) y volúmenes extra ($0.0767/GB/mes) son upsells aparte, no forman parte del costo base del pod.

## 8. Roadmap por fases (regla del proyecto: no construir para una escala que todavía no existe)

Segunda opinión externa (revisión cruzada con otra IA, verificada contra fuentes reales) ayudó a separar "SaaS serio a escala" de "lo mínimo para cobrarle al primer cliente sin fundir la empresa". Regla fija: **construir solo el mecanismo necesario para que el próximo cliente pueda pagar, usar y seguir usando el producto — nada más.**

**Fase 0 — Antes de construir (donde estamos)**
Objetivo: demostrar que alguien paga. Landing + demo + outreach + precio real + **3 preventas o compromisos de pago reales** (ver `VALIDATION_PLAN.md`). No se construye nada del pod todavía.

**Fase 1 — Primer cliente (el MVP real, no antes)**
Objetivo: que una persona lo use sin que la empresa pierda plata ni dependa de control manual total.
- Polar (única fuente de verdad de billing) + backend mínimo + Hetzner API + 1 pod MCP Terminal.
- Provisioning seguro con aislamiento por VM: 1 VM Hetzner por cliente, imagen base reproducible, firewall (todo cerrado salvo MCP/A2A con token), SSH cerrado por defecto, MCP autenticado, A2A autenticado (básico, Nivel 1 de `PRODUCT_TYPES.md`), Docker sin acceso innecesario al host. Objetivo concreto: que un atacante medio no pueda convertir el primer pod en puerta de entrada al resto de la infra.
- Anti-abuso mínimo + kill switch manual: rate limits, **máximo 1 pod por cuenta** al arrancar (reduce el blast radius), firewall, logs básicos, métricas básicas de CPU/red, botón administrativo "Suspend Pod", capacidad de bloquear IP/token, procedimiento escrito para responder a un abuse report de Hetzner.
- Billing + lifecycle + control de costos: cadena de control única `Polar → nuestra DB → Hetzner API` (nunca frontend hablando directo con Hetzner, ni Polar creando servidores directo). Cada pod con owner + Hetzner server ID + fecha + spec + estado de billing (`creating → running → restricted → archived → deleted`, ver `OPERATIONS.md`). Revisión periódica (manual al principio) de servidores en Hetzner que la DB no reconozca.
- **Pérdida máxima por cliente, limitada de entrada**: solo specs chicas/medias habilitadas al lanzar (no vender CCX53/CCX63 a un usuario desconocido el día 1 — el riesgo de una cuenta fraudulenta minando cripto 48hs no se justifica por el margen).
- Mínimo legal (no enterprise): Terms of Service + Acceptable Use Policy + Privacy Policy + Refund/Cancellation Policy — quién puede usar el producto, usos prohibidos, quién responde por el código ejecutado, cuándo se suspende, qué pasa si no paga, retención de datos, qué pasa al borrar un pod, límite de responsabilidad, política de reembolso.

**Fase 2 — 3 a 10 clientes**
Recién aquí: provisioning idempotente (reintentar sin crear servidores huérfanos), cleanup automático de recursos huérfanos, backups/restores probados de verdad (no solo "se hace el snapshot"), monitoreo central básico, dunning automático (no manual), límites de cuenta más finos, mejor onboarding, recovery self-service, versionado/upgrade de imagen base.

**Fase 3 — Cuando haya tracción real**
Recién aquí: AgentPod Mesh (Nivel 2 de coordinación entre pods, Redis multi-tenant, observabilidad cross-pod), MCP Visual (más complejo que Terminal: browser + Xvfb + streaming + WebRTC + takeover — se vende después de validar Terminal, no en paralelo), roles/RBAC por organización, observabilidad central tipo Datadog, control plane robusto para cientos de pods, alta disponibilidad, SLA contractual, compliance empresarial (DPA/GDPR/SOC2) — esto último recién si aparece un cliente real que lo exija, no antes.

**Qué NO se construye antes de la primera venta**: control plane para flota grande, Mesh avanzado, Redis multi-tenant, MCP Visual, observabilidad tipo Datadog, alta disponibilidad/disaster recovery enterprise, roles complejos, SLA contractual, compliance enterprise. Todo eso es capital quemado si todavía no hay un cliente pagando.

## 9. Naming
Recomendación principal: **AgentPod** (agentpod.ai / .dev / .cloud).

Alternativas:
- Developer tool / executable: RunAgent, AgentBox, AgentGrid.
- Infraestructura & kernel: KernelAgent, HostAgent, HyperAgent.

Frase de pitch (corregida tras análisis competitivo, ver `docs/COMPETITION.md`): "AgentPod te da una computadora en la nube, persistente y administrada, para que tu agente de IA (Claude Code, Codex, Gemini CLI, el que sea) trabaje 24/7 — con precio mensual predecible, no medido por segundo." Ya no se vende como "sandbox aislado" (comoditizado por E2B/Daytona) ni como "5 segundos" sin medición real.
