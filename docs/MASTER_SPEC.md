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

## 7. Pricing y Márgenes (real, no estimado)

**Proveedores confirmados:**
- Pagos/billing: **Polar** (Merchant of Record, fee real: 4% + $0.40 USD por transacción).
- Infraestructura: **Hetzner Cloud API** (aprovisionamiento de pods on-demand).

**Costos de infra por plan (Hetzner, precios post-suba jun 2026):**
- CX22 (2vCPU/4GB): ~$4.70/mes
- CPX22 (2vCPU/4GB AMD, mejor para streaming): ~$9.50/mes

**Planes:**

| Plan | Precio | Incluye |
|---|---|---|
| Starter | $15/mes | 1 pod CX22, terminal web, 24/7 |
| Pro | $39/mes | 1 pod CPX22 + streaming remoto 60fps |
| Team | $99/mes | 3 pods mixtos, soporte prioritario |

**Desglose de costos por plan (% del precio, con Polar):**

| Rubro | Starter ($15) | Pro ($39) | Team ($99) |
|---|---|---|---|
| Infra (Hetzner) | $5.00 (33.3%) | $10.00 (25.6%) | $25.00 (25.3%) |
| Comisión Polar (4% + $0.40) | $1.00 (6.7%) | $1.96 (5.0%) | $4.36 (4.4%) |
| Marketing (10%) | $1.50 | $3.90 | $9.90 |
| Soporte/operación (5%) | $0.75 | $1.95 | $4.95 |
| Contingencia (5%) | $0.75 | $1.95 | $4.95 |
| **Costos totales** | **$9.00 (60.0%)** | **$19.76 (50.7%)** | **$49.16 (49.7%)** |
| **Margen neto** | **$6.00 (40.0%)** | **$19.24 (49.3%)** | **$49.84 (50.3%)** |

**Nota**: el margen esperado de 95-97% mencionado originalmente para "SaaS" no aplica acá porque cada pod consume infra real de Hetzner (costo variable, no marginal-cero como el software puro). Margen neto real: 40-50% según plan, mejorando en los planes más grandes. Con mezcla de planes (meta 10-15 clientes en septiembre), el margen neto blend estimado es ~45-48%.

## 8. Naming
Recomendación principal: **AgentPod** (agentpod.ai / .dev / .cloud).

Alternativas:
- Developer tool / executable: RunAgent, AgentBox, AgentGrid.
- Infraestructura & kernel: KernelAgent, HostAgent, HyperAgent.

Frase de pitch: "AgentPod es la plataforma de infraestructura en la nube que permite desplegar pods dedicados y aislados para agentes de IA en 5 segundos."
