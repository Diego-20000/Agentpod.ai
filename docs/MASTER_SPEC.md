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

## 7. Naming
Recomendación principal: **AgentPod** (agentpod.ai / .dev / .cloud).

Alternativas:
- Developer tool / executable: RunAgent, AgentBox, AgentGrid.
- Infraestructura & kernel: KernelAgent, HostAgent, HyperAgent.

Frase de pitch: "AgentPod es la plataforma de infraestructura en la nube que permite desplegar pods dedicados y aislados para agentes de IA en 5 segundos."
