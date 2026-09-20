# AgentPod — Plan de Validación (Semanas 1-2)

## Nicho
Desarrolladores freelance y estudios chicos (1-5 personas) que ya usan Claude Code / agentes de IA (LangChain, AutoGPT, MCP) para programar, y que:
- Corren agentes en su compu local (Mac/PC de trabajo) y sufren consumo de CPU/RAM.
- Les preocupa dar acceso de ejecución de código arbitrario a un agente en su máquina principal.

Canal de acceso: comunidades donde ya están (Reddit r/ClaudeAI, r/LocalLLaMA, X #BuildInPublic, Discord de Claude Code/Cursor, Hacker News).

## Mensaje de contacto inicial (WhatsApp/DM, 4 líneas)
> Hola [nombre], vi que usás Claude Code / agentes de IA para programar.
> ¿Corrés los agentes en tu compu principal? Eso come recursos y da acceso a tu sistema.
> Armé AgentPod: un entorno aislado en la nube, listo en 5 segundos, con terminal web y acceso remoto.
> ¿Te sirve probarlo gratis esta semana?

## Oferta concreta de prueba
- Acceso gratis por 7 días a un pod dedicado (1 servidor Hetzner chico).
- Sin tarjeta, sin compromiso.
- Objetivo: que lo usen al menos una vez para correr un agente real.

## Plan de 2 semanas
1. **Días 1-3**: Armar landing "puerta falsa" (1 sola página, sin backend real todavía) con la propuesta y botón "Quiero probarlo".
2. **Días 1-10**: Contactar 30-50 prospectos vía DM/WhatsApp en las comunidades listadas arriba. Registro manual en planilla (nombre, canal, respuesta, fecha).
3. **Días 10-14**: Cerrar semana. Contar cuántos dijeron "sí, quiero probarlo".

## Umbral de decisión (semana 2)
- **≥3 prospectos** dicen que quieren probarlo gratis → seguir a construir MVP (semanas 3-6).
- **<3 en 2 semanas** → pivotar a otro nicho (ej: estudiantes de IA, agencias que ofrecen automatización con IA a clientes).

## Registro de contactos
Planilla simple (Google Sheets o CSV local) con columnas:
`fecha | nombre | canal | mensaje enviado | respuesta | interesado (sí/no) | notas`

## Próximo paso después de validar
Si se cumple el umbral: MVP minimalista (2-3 días) con integración **Polar** (única fuente de verdad de billing, ver `MASTER_SPEC.md` §7 — se sacó Mercado Pago de este plan para no tener dos sistemas de cobro en paralelo) y notificaciones automáticas (semanas 3-6 del plan general).

**Corrección al criterio de validación**: "¿querés probarlo gratis?" mide curiosidad, no negocio. Se reemplaza por 4 etapas: (1) interés — 30-50 prospectos; (2) problema confirmado — al menos 10 que digan concretamente que hoy tienen este problema; (3) intención monetaria — presentar el producto con precio real, no gratis; (4) señal fuerte — objetivo: **3 personas dispuestas a pagar/reservar un pod**, antes de construir el sistema completo. "Uh sí, estaría bueno" no cuenta como validación; "sí, cobrámelo a $20/mes cuando esté" sí.
