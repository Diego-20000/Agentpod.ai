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
- Monto de la ronda pre-seed: $100,000 USD.
- Instrumento legal: SAFE (formato estándar Y Combinator).
- Cobro: Crypto (USDC/USDT) a billetera corporativa/Lemon Cash con justificación de SAFE, o transferencia internacional (Wire/SWIFT) vía C-Corp en Delaware (Mercury/Wise).

## 4. Uso de Fondos ($100,000 USD)

| Categoría | Detalle | Monto (USD) | % |
|---|---|---|---|
| Runway del fundador | 12–15 meses, ~$3,000 USD/mes limpios, dedicación 100% | $36,000 | 36.0% |
| Equipamiento hardware | ThinkPad P16 Gen 3, MacBook Pro 16" M4 Max, Galaxy S26 Ultra, iPhone 17 Pro Max | $26,900 | 26.9% |
| Marketing & influencers | Puerta falsa, Reddit/Google ads, prensa tech, creadores | $15,000 | 15.0% |
| Infraestructura & cloud | Servidor central, dominios, SSL, pool de prueba en Hetzner | $11,816 | 11.8% |
| Software & IAs | ChatGPT Pro, API Claude, Figma, v0/Bolt, cuentas dev | $5,284 | 5.3% |
| **TOTAL** | | **$100,000** | **100%** |

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

### Shared (CX/CPX/CAX) — fórmula `precio = (costo_hetzner + $0.40) / 0.385`, margen neto 37.5%

| Tipo | Specs | Costo Hetzner | Precio cliente | Margen neto |
|---|---|---|---|---|
| CX23 | 2vCPU, 4GB, 40GB | $6.49 | $17.90 | $6.71 (37.5%) |
| CX33 | 4vCPU, 8GB, 80GB | $9.99 | $26.99 | $10.12 |
| CX43 | 8vCPU, 16GB, 160GB | $18.49 | $49.06 | $18.40 |
| CX53 | 16vCPU, 32GB, 320GB | $34.99 | $91.92 | $34.47 |
| CAX11 | 2vCPU ARM, 4GB, 40GB | $6.99 | $19.19 | $7.20 |
| CAX21 | 4vCPU ARM, 8GB, 80GB | $12.49 | $33.48 | $12.56 |
| CAX31 | 8vCPU ARM, 16GB, 160GB | $24.99 | $65.95 | $24.73 |
| CAX41 | 16vCPU ARM, 32GB, 320GB | $48.49 | $127.01 | $47.63 |
| CPX12 | 1vCPU, 2GB, 40GB | $13.49 | $36.08 | $13.53 |
| CPX22 | 2vCPU, 4GB, 80GB | $22.99 | $60.75 | $22.78 |
| CPX32 | 4vCPU, 8GB, 160GB | $41.99 | $110.10 | $41.29 |
| CPX42 | 8vCPU, 16GB, 320GB | $81.99 | $214.13 | $80.30 |
| CPX52 | 12vCPU, 24GB, 480GB | $118.99 | $310.10 | $116.29 |
| CPX62 | 16vCPU, 32GB, 640GB | $152.99 | $398.42 | $149.41 |

### Dedicado (CCX) — un poco más barato que AWS (precio = AWS equivalente × 0.92), reserva reducida (11%)

| Tipo | Specs | Costo Hetzner | AWS equivalente | Precio cliente | Margen neto |
|---|---|---|---|---|---|
| CCX13 | 2vCPU, 8GB, 80GB | $50.49 | $70.08 (m6i.large) | $64.47 | $3.91 (6.1%) |
| CCX23 | 4vCPU, 16GB, 160GB | $101.49 | $140.16 (m6i.xlarge) | $128.95 | $7.72 (6.0%) |
| CCX33 | 8vCPU, 32GB, 240GB | $162.99 | $280.32 (m6i.2xlarge) | $257.89 | $55.81 (21.6%) |
| CCX43 | 16vCPU, 64GB, 360GB | $325.49 | $560.64 | $515.79 | $112.53 (21.8%) |
| CCX53 | 32vCPU, 128GB, 600GB | $629.49 | $1,121.28 | $1,031.58 | $246.96 (23.9%) |
| CCX63 | 48vCPU, 192GB, 960GB | $1,006.99 | $1,681.92 | $1,547.37 | $307.88 (19.9%) |

**Notas:**
- El margen 95-97% mencionado originalmente para "SaaS" no aplica: cada pod consume infra real de Hetzner (costo variable).
- Shared (CX/CPX/CAX) es el volumen principal: margen neto sano y consistente (~37.5%).
- Dedicado (CCX) es upsell de nicho: CCX13/23 (los tamaños más pedidos) tienen margen muy ajustado (~6%) porque el costo de Hetzner ya casi empata con AWS ahí; CCX33 en adelante mejora bastante (20-24%) porque Hetzner escala mejor que AWS en instancias grandes.
- Se eligió no ser "mucho más barato" que AWS en CCX porque el margen no lo permite con los precios actuales de Hetzner (subieron 2-2.7x en junio 2026); apenas 8% debajo alcanza para competir sin regalar margen.

## 8. Naming
Recomendación principal: **AgentPod** (agentpod.ai / .dev / .cloud).

Alternativas:
- Developer tool / executable: RunAgent, AgentBox, AgentGrid.
- Infraestructura & kernel: KernelAgent, HostAgent, HyperAgent.

Frase de pitch: "AgentPod es la plataforma de infraestructura en la nube que permite desplegar pods dedicados y aislados para agentes de IA en 5 segundos."
