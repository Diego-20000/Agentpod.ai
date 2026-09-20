# AgentPod — Contenido y funcionalidad por página (Fase 1)

No es diseño — es qué tiene que mostrar y qué acciones permite cada superficie del producto. Grounded en `MASTER_SPEC.md`, `PRODUCT_TYPES.md`, `OPERATIONS.md`, `COMPETITION.md`, `legal/`.

**7 superficies funcionales, no 6.** La que faltaba: **Provisioning / Pod recién creado / Primer acceso** — sin esto, el flujo finge que el pod está listo antes de que MCP, A2A y `healthz` pasen de verdad (`OPERATIONS.md` §1.5). No hace falta página aparte para "elegir agente" (va en el configurador), "gestión de tokens MCP" (va en el detalle del pod), ni "error genérico" (los errores viven en contexto: provisioning failed, health degraded, payment issue, resize failed).

**Flujo real**: Landing → Login/Signup → Configurador → Polar Checkout → **Provisioning/Primer acceso** → Dashboard → Detalle del Pod. Facturación accesible desde el dashboard/app shell. Legales (ToS/AUP/Privacy/Refund) accesibles desde landing, signup, billing y footer.

## 1. Landing
- Qué es AgentPod: computadora cloud persistente, VM dedicada por cliente, MCP+A2A incluidos, BYOK (no vendemos tokens de IA).
- Tipos: MCP Terminal (única opción comprable en Fase 1); MCP Visual mostrado como "próximamente" (se construye después de validar Terminal, ver `MASTER_SPEC.md` §8).
- Agentes compatibles: Claude Code, Cursor CLI, Gemini CLI, otros MCP.
- Pricing: sin planes fijos, precio según spec real de Hetzner.
- Persistencia, seguridad (VM dedicada, firewall, MCP/A2A autenticados), snapshot de 7 días al archivar.
- Nada de: "5 segundos", SLA/uptime prometido, "aislamiento absoluto", Mesh avanzado — contradice `MASTER_SPEC.md`/`COMPETITION.md` ya corregidos.
- Acciones: Crear mi Pod, Ver specs y precios, Iniciar sesión, Ver legal.

## 2. Login / Signup
- Solo OAuth (Google/GitHub) — sin password propia, sin reset/2FA en Fase 1.
- Aceptación de ToS/AUP/Privacy/Refund en el alta.
- Aviso de que contratar crea recursos reales con facturación recurrente.

## 3. Configurador de Pod
- Tipo de pod: MCP Terminal (comprable); MCP Visual bloqueado/"próximamente" en Fase 1.
- Selector de agente: Claude Code (default), Cursor CLI, Gemini CLI, otro MCP — con aviso BYOK.
- Catálogo real de Hetzner (vCPU/RAM/disco/tipo), no "planes".
- Precio en vivo, opcionales (backup, volumen extra).
- Aviso operacional antes de pagar: resize implica downtime ~2min, disco no se achica, apagar no detiene el costo de Hetzner, secuencia `restricted→archiving→archived` si no paga, snapshot 7 días.
- Resumen final → **Continuar con Polar** (checkout de Polar, no un sistema de cobro propio).

## 4. Provisioning / Pod recién creado / Primer acceso (nueva)
**Estado provisioning**: pod_id, tipo, agente, spec, precio, estado de pago, `lifecycle=provisioning`, `access=restricted`. Progreso en pasos simples (pago confirmado → servidor creado → firewall → servicios → MCP → A2A → health check → acceso habilitado). **Nunca mostrar el token MCP hasta que el health check real termine.**

**Estado listo** (`lifecycle=running, access=active, health=healthy`): specs finales, terminal disponible, instrucciones de conexión MCP, primer paso recomendado. Token MCP entregado **una sola vez** vía mecanismo seguro (nunca visible permanentemente, ver `OPERATIONS.md` §2) — después solo "rotar token" desde el detalle del pod.

**Falla temporal**: "estamos intentando completar tu pod", sin botón de "crear otro" (evita duplicados/costos). **Falla permanente**: pod no disponible, suscripción cancelada, reembolso solicitado vía Polar, referencia de pedido (alineado a `OPERATIONS.md` §5.5 y `legal/REFUND_POLICY.md`).

## 5. Dashboard
Por cada pod, mostrar los **3 ejes separados** (`OPERATIONS.md` §3), nunca mezclados: identidad (nombre, tipo, agente), infra (specs, precio), **lifecycle** (`provisioning/running/resizing/archiving/archived/failed/deleted`), **access** (`active/restricted`), **health** (`healthy/degraded`). Ejemplo válido: "Running · Restricted · Healthy" = infra sana, cliente sin acceso (ej. por no pago).

Pods archivados: fecha de archivado, vencimiento del snapshot, "Solicitar recuperación" (manual en Fase 1, no automática). Banners de estado: pago vencido, restricted, health degraded, provisioning fallido, resize en curso, snapshot por vencer.

## 6. Detalle del Pod
Organizado en secciones (no necesariamente URLs separadas): **Overview** (specs, estados, precio) · **Terminal** (xterm.js real, nunca credenciales del control plane visibles) · **MCP** (endpoint, token enmascarado, rotar token con invalidación inmediata del anterior, "copiar configuración MCP" no solo el token) · **Agente/BYOK** (proveedor de IA configurado, estado de conexión, nunca la key completa visible) · **Activity/Audit log** (timestamp, acción, origen, resultado — base para detectar abuso, ligado a OpenTelemetry).

**Resize**: mostrar spec actual/nueva, precio actual/nuevo, aviso de downtime y de que el disco no se achica; upgrade prorratea ya, downgrade en la próxima renovación (Polar calcula, no a mano).

**Eliminar pod**: nunca `running→deleted` directo. Explicar que pasa a archiving con snapshot de 7 días, y que borrar el servidor es lo que de verdad detiene el costo de Hetzner.

## 7. Facturación
Polar como única fuente de verdad — no un sistema de facturación propio en paralelo. Suscripción actual (producto/spec/precio/renovación), método de pago (administrado en Polar, nunca guardamos tarjeta), historial, y el vínculo explícito `suscripción → pod → spec de Hetzner` (no vendemos "créditos"). Acciones: administrar en Polar, cambiar método, cancelar, ver recibo, ver política de reembolso.

## Legales (páginas estáticas, no parte del flujo de la app)
ToS, AUP, Privacy, Refund (ya en `docs/legal/`) — accesibles desde landing, signup, billing y footer. Incluir el Botón de Arrepentimiento y Botón de Baja de Servicio donde corresponda (`legal/REFUND_POLICY.md`).

## Qué NO se agrega como página independiente en Fase 1
"Elegir agente" (va en el configurador), "MCP Token Manager" (va en detalle del pod), página de error genérica (los errores viven en contexto), Support Center (alcanza con "contactar soporte" + email + docs).
