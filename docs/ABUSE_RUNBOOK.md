# AgentPod — Runbook de respuesta a abuso

Procedimiento a seguir ante una alerta interna de abuso o un abuse report externo (típicamente de Hetzner). Objetivo: contener primero, investigar después, decidir al final. Nunca al revés.

## Objetivos de tiempo (internos, no garantizados por Hetzner)
| Evento | Objetivo |
|---|---|
| Señal crítica detectada → alerta | < 1 min |
| Alerta → cuarentena (Suspend Pod) | < 2 min |
| Cuarentena → servidor apagado | < 3 min |
| Abuse report de Hetzner → primera respuesta | < 30-60 min |

## Minuto 0-5: contener
1. Identificar: `server_id`, IP, `pod_id`, cliente, motivo del reporte/alerta, timestamp del incidente.
2. Ejecutar **Suspend Pod** (ver `OPERATIONS.md` §2): firewall deny-all (inbound + outbound) → `shutdown` → si sigue corriendo, `poweroff`.
3. Marcar `pod.status = restricted`, `incident.status = contained`.

**No investigar primero y bloquear después. Primero se corta el incendio.**

## Minuto 5-20: preservar evidencia
Antes de borrar nada, guardar (solo lo relevante, no hace falta forense completo):
- Logs de sistema y auth.
- Eventos de Docker, procesos activos, conexiones de red.
- Métricas de Netdata del período.
- Logs de auditoría de MCP y A2A.

Preguntas que esa evidencia tiene que responder: ¿qué estaba haciendo el pod? ¿cuándo empezó? ¿fue el cliente, una app comprometida, o una credencial robada? ¿el riesgo sigue activo?

## Minuto 20-40: responder al reporte (si es de Hetzner u otro tercero)
Responder por el mismo canal/ticket que usó Hetzner, con un mensaje factual:

> We identified the affected server and isolated it.
> Server: [ID] · Public IP: [IP] · At: [TIMESTAMP UTC]
> Actions taken: blocked inbound and outbound traffic, powered off the server, disabled customer access, started an internal investigation.
> We are investigating the root cause and will not restore the server until the issue is resolved.

Nunca responder "fue culpa del cliente" (todavía no se sabe) ni "no hicimos nada" (deja peor parado).

## Minuto 40+: decidir qué hacer con el pod

**Si fue un compromiso** (credencial robada, app vulnerada): no confiar en "borrar el malware detectado" — reinstalar desde cero es la recomendación estándar (también de Hetzner). Secuencia: preservar evidencia → revocar token MCP → borrar/reconstruir desde imagen limpia → emitir token nuevo.

**Si fue abuso deliberado del cliente**: cuenta `restricted`, servidor borrado, token revocado, cuenta bloqueada. Documentar el incidente.

**Si fue falso positivo**: responder con evidencia concreta (qué proceso era, por qué era legítimo, qué tráfico era, qué se ajusta para evitar que se repita la falsa alarma) y restaurar el acceso.

## Notas
- Este runbook aplica tanto a alertas propias (Netdata + reglas de `OPERATIONS.md` §2) como a reportes externos.
- Los umbrales de detección son nuestros, no oficiales de Hetzner — se ajustan con datos reales a medida que haya más pods corriendo.
