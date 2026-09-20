# Conectá tu AgentPod a tu cliente MCP

Guía de cliente (texto listo para usar en la pantalla "Provisioning/Primer acceso" de `PAGE_CONTENT.md`). Contrato técnico fijado en `COMPATIBILITY.md`: endpoint `/mcp`, token con prefijo `ap_sk_live_`.

## Antes de empezar
MCP conecta tu cliente de IA con las herramientas de tu pod: **Claude Code / Cursor / Gemini CLI → AgentPod → herramientas del pod**. El token MCP autoriza ese acceso — no es tu API key de Anthropic/OpenAI/Google (BYOK, esa la configurás vos dentro del pod).

**1. Copiá tu endpoint MCP** desde el detalle del pod → sección MCP. Ejemplo: `https://pod-abc123.agentpod.ai/mcp`. No agregues `/sse` ni `/agent-card.json` (ese es de A2A, no de MCP).

**2. Abrí el enlace seguro de tu token** (expira, uso único). No lo pegues en URLs, no lo subas a Git, no lo compartas por chat/capturas. Si lo perdés: **Detalle del Pod → MCP → Rotar token** (invalida el anterior al instante).

## Claude Code
```bash
read -s AGENTPOD_MCP_TOKEN
echo
claude mcp add --transport http agentpod "https://TU-ENDPOINT-MCP/mcp" --header "Authorization: Bearer $AGENTPOD_MCP_TOKEN"
unset AGENTPOD_MCP_TOKEN
claude mcp list
```
Probar: abrí una sesión nueva y pedile "Listá las herramientas MCP disponibles en AgentPod".

## Cursor
Configurar como servidor MCP **global** (`~/.cursor/mcp.json`, no en un archivo de proyecto que vaya a Git):
```json
{
  "mcpServers": {
    "agentpod": {
      "url": "https://TU-ENDPOINT-MCP/mcp",
      "headers": { "Authorization": "Bearer ap_sk_live_TU_TOKEN" }
    }
  }
}
```
Reiniciar/recargar Cursor y verificar en la sección MCP que `agentpod` aparezca conectado.

## Gemini CLI
```bash
read -s AGENTPOD_MCP_TOKEN
echo
gemini mcp add --transport http agentpod "https://TU-ENDPOINT-MCP/mcp" --header "Authorization: Bearer $AGENTPOD_MCP_TOKEN"
unset AGENTPOD_MCP_TOKEN
gemini mcp list
```

## Errores comunes
- **401 Unauthorized**: chequear formato exacto `Authorization: Bearer ap_sk_live_...`, sin comillas, nunca como parámetro de URL. Si el token fue rotado, generar uno nuevo.
- **404 Not Found**: el endpoint del MVP termina en `/mcp`, no `/sse` ni `/.well-known/agent-card.json` (ese es A2A).
- **Conecta pero no muestra herramientas**: verificar que el pod esté `Running · Active · Healthy`. Si persiste, contactar soporte con nombre del pod, cliente usado, SO y error exacto — **nunca enviar el token**.

**Nota**: el pod y el proveedor de IA son cosas separadas — AgentPod da la infraestructura, tu cuenta de Anthropic/Google/etc. da el modelo y sus tokens (BYOK).
