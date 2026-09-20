# AgentPod — Plan de Validación (Semanas 1-2)

## Nicho
Desarrolladores freelance y estudios chicos (1-5 personas) que ya usan Claude Code / agentes de IA (LangChain, AutoGPT, MCP) para programar, y que:
- Corren agentes en su compu local (Mac/PC de trabajo) y sufren consumo de CPU/RAM.
- Les preocupa dar acceso de ejecución de código arbitrario a un agente en su máquina principal.

Canal de acceso: comunidades donde ya están (Reddit r/ClaudeAI, r/LocalLLaMA, X #BuildInPublic, Discord de Claude Code/Cursor, Hacker News).

## Mensaje de contacto inicial (WhatsApp/DM, 4 líneas)
> Hola [nombre], vi que usás Claude Code / agentes de IA para programar.
> ¿Corrés los agentes en tu compu principal? Eso come recursos y da acceso a tu sistema.
> Armé AgentPod: un entorno aislado en la nube (Hetzner), listo en menos de un minuto, con terminal web y acceso remoto.
> ¿Te sirve una charla de 10 min para mostrártelo?

**Nota**: nada de "gratis" ni "5 segundos" en el mensaje — corregido para no medir curiosidad, ver más abajo. El objetivo del primer contacto es agendar la demo, no regalar acceso.

## Oferta real (con precio, no gratis)
- Se muestra el producto con **precio real** del catálogo (ver `MASTER_SPEC.md` §7).
- Provisioning manual al principio, no automático: `cliente acepta pagar → vos creás el pod a mano en Hetzner → le das acceso`. Es válido para los primeros clientes, no hace falta checkout automático todavía.
- Objetivo: que acepte pagar por un pod real, no que "lo pruebe gratis".

## Plan de 2 semanas, en 4 etapas (no solo "interés")
1. **Etapa 1 — Interés (días 1-10)**: contactar 30-50 prospectos vía DM/WhatsApp en las comunidades listadas arriba. Registro manual en planilla.
2. **Etapa 2 — Problema confirmado**: de esos, al menos **10** tienen que decir concretamente que hoy sufren este problema (no "estaría bueno", sino "sí, esto me pasa").
3. **Etapa 3 — Intención monetaria**: a esos 10, mostrarles el producto con el precio real, no gratis.
4. **Etapa 4 — Señal fuerte (días 10-14)**: contar cuántos aceptan pagar/reservar un pod.

## Umbral de decisión (semana 2)
- **≥3 personas dispuestas a pagar** (no "les interesa", sino que aceptan el precio real) → seguir a construir el MVP mínimo (Fase 1, ver `MASTER_SPEC.md` §3/§4).
- **<3 en 2 semanas** → pivotar a otro nicho (ej: estudiantes de IA, agencias que ofrecen automatización con IA a clientes).
- "Uh sí, estaría bueno" no cuenta como señal. "Sí, cobrámelo cuando esté" sí.

## Registro de contactos
Planilla simple (Google Sheets o CSV local) con columnas:
`fecha | nombre | canal | mensaje enviado | respuesta | interesado (sí/no) | notas`

## Próximo paso después de validar
Si se cumple el umbral: no se construye "el MVP completo" — se construye lo mínimo de la **Fase 1** definida en `MASTER_SPEC.md` §3 (Polar + backend mínimo + Hetzner API + 1 pod MCP Terminal + auth + firewall + MCP + A2A básico + logs + kill switch + lifecycle + ToS/AUP/Privacy/Refund). Provisioning puede seguir siendo manual para los primeros 1-3 clientes.
