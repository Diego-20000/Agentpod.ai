# AgentPod — Testing de restauración (Fase 2, no bloqueante para el primer cliente)

**Regla**: que exista un snapshot demuestra almacenamiento. No demuestra recuperación. Se prueba, no se asume.

## Procedimiento mínimo, mensual (sin infraestructura de testing compleja)

1. **Pod canario**, sin datos de clientes reales — nunca probar restore sobre un servidor productivo.
2. **Escribir datos conocidos** antes de archivar (un archivo sentinel con fecha, y un repo Git de prueba con contenido conocido).
3. **Ejecutar el flujo real de archivado** (`running → archiving → snapshot → delete server → archived`, ver `OPERATIONS.md` §3) — no un snapshot manual aparte, hay que testear el proceso real de producción.
4. **Registrar**: `pod_id`, `snapshot_id`, fecha/hora, server_type original, región.
5. **Restaurar en una máquina nueva y descartable** a partir del snapshot. El snapshot es solo el disco — hay que reconstruir aparte lo que no vive ahí: firewall, labels, IP, DNS, vínculo DB↔pod.
6. **Rotar el token MCP antes de dar acceso.** El snapshot puede contener el token viejo (`/etc/agentpod/mcp.token`) — nunca reutilizarlo. Orden correcto: `restore → firewall → boot → health checks → rotación de credenciales → nuevo token → acceso`.
7. **Comprobaciones concretas** (no alcanza con que un proceso responda 200):
   - **Datos**: leer el archivo sentinel y el repo Git de prueba.
   - **Sistema**: `systemctl --failed` sin servicios críticos caídos.
   - **Health**: `GET /healthz` en OK.
   - **A2A**: `GET /.well-known/agent-card.json` con `protocolVersion: 1.0`.
   - **MCP real**: conectar con un cliente realmente soportado (Claude Code o Antigravity CLI, ver `COMPATIBILITY.md`) con el token nuevo y pedirle una acción real (ej. leer el archivo sentinel vía la tool de filesystem) — prueba la cadena completa (HTTPS → auth → MCP → herramienta → filesystem restaurado), no solo que el servidor responda.

## Criterio de "restore exitoso"
```
[ ] Servidor nuevo creado desde snapshot
[ ] Datos previos al snapshot presentes
[ ] Git/filesystem íntegros
[ ] Servicios del pod arrancan
[ ] /healthz = OK
[ ] Agent Card accesible (A2A 1.0)
[ ] MCP Streamable HTTP operativo
[ ] Token anterior no funciona
[ ] Token nuevo funciona
[ ] Cliente MCP real ejecuta una operación de prueba

Restore RTO observado: __ min
Ventana de pérdida de datos del snapshot: __ min
```
No se promete ningún RTO al cliente todavía — solo se mide el propio, para tener un número real cuando llegue el momento de comunicarlo.

## Cadencia
Una vez al mes, y cada vez que cambie algo de: `bootstrap.sh`, imagen base, MCP server, A2A server, proceso de archiving/restore, o gestión de tokens.

## Registro
Log en texto plano, sin herramienta especial — un archivo `RESTORE_TEST_LOG.md` con una entrada por prueba (fecha, pod canario, snapshot ID, servidor nuevo, resultado por ítem del checklist, problemas encontrados, acción correctiva). Con esto se puede decir honestamente "probamos periódicamente que un snapshot se convierte en un pod funcional", no solo "tenemos snapshots".
