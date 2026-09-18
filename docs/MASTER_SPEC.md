# AgentPod — Project Master Specification

## 1. Perfil del Fundador y Proyecto
- Fundador: Technical Solo-Founder (18 años, Argentina).
- Nivel de Inversión Inicial Propia: $0 USD (validación y desarrollo con herramientas 100% gratuitas).
- Visión: Plataforma de infraestructura en la nube de referencia para ejecutar, monitorear y gestionar agentes de IA (Claude Code, MCP, LangChain, AutoGPT) en entornos aislados y dedicados.

## 2. Definición del Producto (SaaS)
- **Problema**: Los agentes de IA consumen recursos masivos de CPU/RAM en las computadoras locales de los desarrolladores y representan riesgos de seguridad al ejecutar código arbitrario en el sistema operativo local.
- **Solución**: Entornos dedicados en la nube sobre servidores de Hetzner, desplegados con 1 clic.
- **Características clave**:
  - Despliegue en 5 segundos vía Hetzner Cloud API.
  - Terminal web integrada en tiempo real (xterm.js).
  - Streaming de escritorio remoto en la nube a 60 FPS (WebRTC / noVNC).
  - Aislamiento total en contenedores seguros (LXC/Docker Sandbox).

## 3. Esquema de Inversión y Formato Legal
- Monto de la ronda pre-seed: **$265,000 USD** (recalculado para 24 meses de runway real, ver §4).
- Instrumento legal: SAFE (formato estándar Y Combinator).
- Cobro: Crypto (USDC/USDT) a billetera corporativa/Lemon Cash con justificación de SAFE, o transferencia internacional (Wire/SWIFT) vía C-Corp en Delaware (Mercury/Wise).
- Canales de búsqueda 100% asincrónicos (sin videollamadas): Wefunder, Republic (crowdfunding de equity), Platanus Ventures (entrevista por chat, no video), TinySeed / Earnest Capital / Calm Company Fund (fondos para SaaS solo-founder, proceso por escrito), AngelList/Wellfound.
- **Nota importante**: la compra de un departamento propio (vivienda) NO forma parte del uso de fondos de la ronda — es gasto personal, separado, a pagar con ingresos propios cuando el negocio facture. Incluir gastos personales no declarados en el uso de fondos de un SAFE es mal uso de capital de riesgo y un riesgo legal/reputacional real. El runway sí incluye **alquiler**, que es gasto de vida legítimo.

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
- Pagos/billing: **Polar** (Merchant of Record, fee real: 4% + $0.40 USD por transacción).
- Infraestructura: **Hetzner Cloud API** (aprovisionamiento de pods on-demand, cada pod = 1 VM propia por cliente, sin compartir servidor entre clientes).

**No hay planes fijos (Starter/Pro/Team).** El usuario elige specs (vCPU/RAM/disco) del catálogo real de Hetzner vía API, con precio calculado en vivo. Datos sacados en vivo de `GET /v1/pricing` y `GET /v1/server_types` (región fsn1, USD, sep. 2026).

**Reservas sobre el precio final:**
- Comisión Polar: 4% + $0.40 fijo por transacción.
- Marketing: 10% (5% en línea dedicada).
- Soporte/operación: 5% (3% en línea dedicada).
- Contingencia: 5% (3% en línea dedicada).

**Costo real de un pod = servidor Hetzner + IP pública (IPv4, $0.60/mes, ya no viene gratis).** Otros costos de Hetzner que existen pero son opcionales (no van en el costo base): volumen extra $0.0767/GB/mes, backup automático +20% del precio del servidor, snapshot/imagen $0.0199/GB/mes.

### Shared (CX/CPX/CAX) — fórmula `precio = (costo_hetzner + IP + $0.40) / 0.385`, margen neto 37.5%

| Tipo | Specs | Costo Hetzner | + IP | Costo total | Precio cliente | Margen neto |
|---|---|---|---|---|---|---|
| CX23 | 2vCPU, 4GB, 40GB | $6.49 | $0.60 | $7.09 | $19.45 | $7.29 (37.5%) |
| CX33 | 4vCPU, 8GB, 80GB | $9.99 | $0.60 | $10.59 | $28.55 | $10.71 |
| CX43 | 8vCPU, 16GB, 160GB | $18.49 | $0.60 | $19.09 | $50.62 | $18.98 |
| CX53 | 16vCPU, 32GB, 320GB | $34.99 | $0.60 | $35.59 | $93.48 | $35.06 |
| CAX11 | 2vCPU ARM, 4GB, 40GB | $6.99 | $0.60 | $7.59 | $20.75 | $7.78 |
| CAX21 | 4vCPU ARM, 8GB, 80GB | $12.49 | $0.60 | $13.09 | $35.04 | $13.14 |
| CAX31 | 8vCPU ARM, 16GB, 160GB | $24.99 | $0.60 | $25.59 | $67.51 | $25.32 |
| CAX41 | 16vCPU ARM, 32GB, 320GB | $48.49 | $0.60 | $49.09 | $128.55 | $48.21 |
| CPX12 | 1vCPU, 2GB, 40GB | $13.49 | $0.60 | $14.09 | $37.64 | $14.12 |
| CPX22 | 2vCPU, 4GB, 80GB | $22.99 | $0.60 | $23.59 | $62.31 | $23.37 |
| CPX32 | 4vCPU, 8GB, 160GB | $41.99 | $0.60 | $42.59 | $111.66 | $41.87 |
| CPX42 | 8vCPU, 16GB, 320GB | $81.99 | $0.60 | $82.59 | $215.82 | $80.93 |
| CPX52 | 12vCPU, 24GB, 480GB | $118.99 | $0.60 | $119.59 | $311.66 | $116.87 |
| CPX62 | 16vCPU, 32GB, 640GB | $152.99 | $0.60 | $153.59 | $400.00 | $150.00 |

### Dedicado (CCX) — un poco más barato que AWS (precio = AWS equivalente × 0.92), reserva reducida (11%)

| Tipo | Specs | Costo total (servidor+IP) | AWS equivalente | Precio cliente | Margen neto |
|---|---|---|---|---|---|
| CCX13 | 2vCPU, 8GB, 80GB | $51.09 | $70.08 (m6i.large) | $64.47 | $3.31 (5.1%) |
| CCX23 | 4vCPU, 16GB, 160GB | $102.09 | $140.16 (m6i.xlarge) | $128.95 | $7.12 (5.5%) |
| CCX33 | 8vCPU, 32GB, 240GB | $163.59 | $280.32 (m6i.2xlarge) | $257.89 | $55.21 (21.4%) |
| CCX43 | 16vCPU, 64GB, 360GB | $326.09 | $560.64 | $515.79 | $111.93 (21.7%) |
| CCX53 | 32vCPU, 128GB, 600GB | $630.09 | $1,121.28 | $1,031.58 | $246.36 (23.9%) |
| CCX63 | 48vCPU, 192GB, 960GB | $1,007.59 | $1,681.92 | $1,547.37 | $307.28 (19.9%) |

**Notas:**
- El margen 95-97% mencionado originalmente para "SaaS" no aplica: cada pod consume infra real de Hetzner (costo variable).
- Shared (CX/CPX/CAX) es el volumen principal: margen neto sano y consistente (~37.5%).
- Dedicado (CCX) es upsell de nicho: CCX13/23 (los tamaños más pedidos) tienen margen muy ajustado (~5%) porque el costo de Hetzner ya casi empata con AWS ahí; CCX33 en adelante mejora bastante (20-24%) porque Hetzner escala mejor que AWS en instancias grandes.
- Se eligió no ser "mucho más barato" que AWS en CCX porque el margen no lo permite con los precios actuales de Hetzner (subieron 2-2.7x en junio 2026); apenas 8% debajo alcanza para competir sin regalar margen.
- Backup automático (opcional, +20% del precio del servidor) y volúmenes extra ($0.0767/GB/mes) son upsells aparte, no forman parte del costo base del pod.

## 8. Naming
Recomendación principal: **AgentPod** (agentpod.ai / .dev / .cloud).

Alternativas:
- Developer tool / executable: RunAgent, AgentBox, AgentGrid.
- Infraestructura & kernel: KernelAgent, HostAgent, HyperAgent.

Frase de pitch: "AgentPod es la plataforma de infraestructura en la nube que permite desplegar pods dedicados y aislados para agentes de IA en 5 segundos."
