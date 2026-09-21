# AgentPod — Contrato de compatibilidad (MCP / A2A / clientes)

Fijado explícitamente para el MVP — no "soportamos MCP/A2A" en abstracto. Verificado contra fuentes oficiales (specs y changelogs reales, no supuestos).

## Contrato técnico

```
MCP:
  Protocol revision: 2025-11-25
  Transport: Streamable HTTP
  Endpoint: /mcp  (único, sin /sse, sin /mcp/v1, sin versión en el path)
  Auth: Authorization: Bearer ap_sk_live_...
  Legacy SSE: no soportado

A2A:
  Protocol version: 1.0
  Agent Card: /.well-known/agent-card.json
  A2A-Version header: 1.0
```

**Por qué `2025-11-25` y no la más nueva (`2026-07-28`)**: la revisión `2026-07-28` cambió el modelo de Streamable HTTP de fondo — sacó el endpoint GET de streaming y las sesiones a nivel de protocolo, y ahora exige headers `Mcp-Method`/`Mcp-Name`. Es un cambio de arquitectura reciente y todavía no vale la pena convertir "la última" en contrato de producto sin una batería de compatibilidad real contra los 3 clientes que soportamos. Se revisita cuando haya evidencia de que los clientes la adoptaron ampliamente.

**Por qué versión de protocolo A2A `1.0` y no `1.0.1`**: A2A versiona Major.Minor para compatibilidad de protocolo; `1.0.1` es un release/patch de implementación (con fixes), no el identificador que debe viajar en el wire. Se fija `1.0` como contrato, y la versión exacta del SDK/implementación (ej. `@a2a-js/sdk`) se pinnea aparte, con número exacto, no con `^1.0.0`.

## Matriz de clientes MCP soportados (Fase 1)

| Cliente | Versión de referencia | Transporte | Auth | Estado |
|---|---|---|---|---|
| Claude Code | 2.1.278 (19 sep 2026) | Streamable HTTP | `Authorization: Bearer` | **Soportado directamente** |
| Cursor | 3.21 | Streamable HTTP | Header HTTP personalizado | **Soportado directamente** |
| Antigravity CLI | — (ver nota) | Streamable HTTP | Header HTTP personalizado (`serverUrl` + `headers`, sin OAuth) | **Soportado directamente** |
| Claude Desktop | — | Remote MCP vía Custom Connectors | El flujo documentado hoy es OAuth, no header custom | **No soportado directamente en el MVP** — requeriría OAuth o un bridge, queda fuera de alcance por ahora |
| Otros hosts MCP | Variable | Streamable HTTP | Deben permitir headers HTTP personalizados | Best effort, no certificado |

**Contrato comercial**: "Clientes MCP soportados directamente: Claude Code, Cursor y Antigravity CLI. Otros pueden funcionar si soportan Streamable HTTP y headers personalizados, pero no están certificados. Claude Desktop queda fuera del soporte directo del MVP." No prometer Claude Desktop hasta resolver el mecanismo de auth.

**Corrección importante (verificado sep. 2026)**: Google discontinuó **Gemini CLI** el 18 de junio de 2026 para usuarios Pro/Ultra/free y lo reemplazó por **Antigravity CLI** (reescrito en Go, sigue soportando MCP remoto vía `serverUrl` + `headers`, sin OAuth — mismo mecanismo de auth que ya usábamos). Gemini CLI solo sigue vivo para licencias enterprise Gemini Code Assist, que no es nuestro público. Se reemplaza en todo el contrato y el demo.

## Directorios de conectores (Fase 2/3 — distribución, no requisito técnico)

Un "conector oficial" en estas plataformas **es el mismo MCP server que ya definimos**, solo que revisado y listado para instalación de un clic — no es tecnología distinta. En todas, el usuario ya puede conectar nuestro MCP pegando la URL a mano, esté o no en el directorio; estar listado es marketing/descubrimiento, no un bloqueante técnico.

| Plataforma | ¿Proceso de submission abierto? | Estado (investigado) |
|---|---|---|
| Claude (Anthropic) | Sí, portal siempre abierto | Más maduro — revisan auth/datos/tools, contacto `mcp-review@anthropic.com` |
| ChatGPT (OpenAI) | Sí, vía OpenAI Developer Platform | Submission formal, metadata, política de privacidad (ya en `legal/`), testing |
| Google Gemini | No hay proceso público (confirmado may. 2026) | Solo self-serve: usuario pega la URL en "Connected Apps", no hay forma de aplicar a listado |
| Microsoft Copilot | Existe pero orientado a partners/ISV enterprise (M365) | Más pesado, para más adelante |
| Perplexity | No hay proceso público confirmado | +400 conectores pero sin submission documentado; self-serve MCP remoto igual funciona |
| Mistral Le Chat | Directorio curado, agregado por Mistral | Self-serve MCP remoto funciona igual sin estar listado |

**Prioridad**: Claude primero (coincide con el público objetivo, Claude Code), ChatGPT segundo — son los únicos con proceso de submission abierto hoy. El resto, cuando haya tracción.

## Testing de restauración (recovery real, no solo "el snapshot existe")

Ver `RESTORE_TESTING.md` para el procedimiento completo. El resumen: un snapshot demuestra almacenamiento, no recuperación — hay que probar mensualmente que un snapshot se puede convertir en un pod funcional de punta a punta (datos, servicios, MCP, A2A, rotación de token), usando un pod canario sin datos de clientes reales.
