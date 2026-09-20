# AgentPod — Plan de Validación (largo plazo, por cohortes)

**Corregido**: la versión anterior decía "Semanas 1-2", 30-50 contactos y pivotar si no había 3 en 2 semanas. Eso contradice la realidad de un solo founder con horario escolar — es receta para abandonar antes de tener datos útiles. Sistema nuevo: goteo constante y sostenible durante meses, no un sprint.

## Nicho
Desarrolladores freelance y estudios chicos (1-5 personas) que ya usan Claude Code / agentes de IA para programar, y que corren agentes en su compu local sufriendo consumo de CPU/RAM o incomodidad por dar acceso de ejecución a su sistema.

Canal: comunidades donde ya están (Reddit r/ClaudeAI, r/LocalLLaMA, X #BuildInPublic, Discord de Claude Code/Cursor, Hacker News).

## Las 4 etapas, con guión por etapa

**Etapa 1 — Interés (mensaje inicial, DM/WhatsApp):**
> Hola [nombre], vi que usás Claude Code / agentes de IA para programar.
> ¿Corrés los agentes en tu compu principal? Eso come recursos y da acceso a tu sistema.
> Armé AgentPod: un entorno aislado en la nube, listo en menos de un minuto, con terminal web y acceso remoto.
> ¿Te sirve una charla de 10 min para mostrártelo?

Nada de "gratis" ni "5 segundos" — mide curiosidad, no negocio, si se lo deja ahí. El objetivo es agendar la charla, no regalar acceso.

**Etapa 2 — Problema confirmado** (no preguntar "¿te parece buena idea?", sacar un hecho real):
> Quiero entender si esto realmente te pasa hoy y no solo si la idea te parece interesante.
> Cuando usás Claude Code u otros agentes para programar, ¿qué problema te genera concretamente?
> Por ejemplo: que te consume CPU/RAM, que te incomoda darle acceso a archivos de tu PC, tener que dejar la máquina encendida, que se te vuelve lento trabajar mientras corre el agente — o que directamente no tenés ese problema.
> ¿Qué te pasó la última vez que usaste un agente y te hizo pensar "esto estaría mejor corriendo en otro lugar"?

La última pregunta es la que importa. "Sí, me consume RAM" = señal débil. "Ayer tuve que cerrar X porque me dejó la máquina inutilizable" = evidencia real.

**Etapa 3 — Intención monetaria** (con número concreto, no plan abstracto):
> Por lo que me contaste, el problema sí te está afectando hoy.
> Para tu caso, la configuración que tendría sentido es [SPEC] y el precio sería de USD [PRECIO]/mes (ver `MASTER_SPEC.md` §7 para specs/precios reales).
> Incluye el entorno dedicado en la nube, terminal, MCP y la posibilidad de dejar el agente funcionando ahí en lugar de depender de tu PC.
> No te pregunto si "te gusta la idea": ¿pagarías USD [PRECIO]/mes por tener esto disponible para tu trabajo habitual?

Palabra clave: "pagarías", no "¿cuánto pagarías?" (eso abre negociación prematura). No cuentan como señal: "sí, si algún día está barato", "podría servir", "mandame cuando salga" — eso sigue siendo interés, no intención.

**Etapa 4 — Señal fuerte** (reserva real, cuando haya llegado el momento — no antes de tener los legales listos, ver abajo):
> Perfecto. Quiero separar el interés de una decisión real.
> AgentPod todavía no está disponible para entregar; justamente estoy validando esto antes de construirlo.
> Para reservar uno de los primeros Pods, puedo tomar una reserva de USD [RESERVA] (20-30% del primer mes), que se descuenta del primer mes cuando el Pod esté listo. Si antes de la entrega cambiás de decisión, la reserva se devuelve.
> La idea es que no pagues meses de servicio antes de tenerlo: solo dejar reservada tu intención de ser uno de los primeros clientes.
> ¿Querés que avancemos con la reserva?

No pedir USD 1 (se vuelve "click simbólico") ni el mes completo por adelantado. 20-30% del primer mes, reembolsable antes de la entrega, aplicado después al primer período.

## Descuento founding customer (primer mes, no la reserva)
Además de la reserva, el **primer mes completo** se cobra con **25% de descuento** sobre el precio real de `MASTER_SPEC.md` §7 — calculado para que el margen siga siendo positivo incluso en el spec más chico (verificado, no al costo de Hetzner):

| Spec | Precio normal | Precio founding (25% off) | Margen resultante |
|---|---|---|---|
| CX23 | $21.08 | ~$15.81 | ~22% (positivo) |
| CPX22 | $66.92 | ~$50.19 | ~24% (positivo) |

**Por qué no cobrar al costo de Hetzner**: la comisión de Polar/Mercado Pago es un % del precio, no una resta fija — cobrar exactamente el costo de infra deja margen negativo apenas se descuenta la comisión de pago. 25% de descuento es el punto que atrae al founding customer sin perder plata en el primer mes de cada uno.

**Dónde se cobra la reserva/primer mes**: Mercado Pago "Link de pago" (clientes de Argentina, gratis, sin código) o Polar Checkout Link (clientes del exterior, ya es el proveedor elegido) — no hace falta construir una página de pago propia.

## Ritmo sostenible (meses, no sprint)

| Cadencia | Cantidad |
|---|---|
| Prospectos nuevos por semana | **5** (no 30-50) |
| Follow-ups por prospecto | máximo 2 (día 4-7, día 10-14, después se corta el contacto activo) |
| Tiempo del founder | ~2-4h/semana, en 2 bloques cortos |

Ejemplo de semana: martes 2 prospectos nuevos, sábado 3 nuevos. Los follow-ups salen de la misma planilla. Puede haber semanas con cero conversaciones útiles — no significa que el proceso falló. Esto también protege la reputación en las comunidades: aparecés como alguien que habla con gente que podría usar el producto, no como una cuenta de spam.

## Cómo cobrar la reserva (sin infraestructura de pagos)

Para clientes argentinos: transferencia bancaria directa alcanza, con factura correspondiente al régimen fiscal (monotributo: factura C para operación local, factura E si es exportación de servicios). Para clientes del exterior: recién ahí entra Polar (ya definido como Merchant of Record en `MASTER_SPEC.md` §7).

```
VALIDACIÓN:       transferencia / reserva manual
PRIMEROS CLIENTES: Polar + provisioning manual (1-3 clientes)
MVP:              Polar + provisioning automatizado
```

**No cobrar una reserva real hasta tener los documentos de `docs/legal/` con los placeholders reemplazados y la situación fiscal (CUIT/monotributo) resuelta.** Una transferencia no saca de las obligaciones comerciales — si se cobra a un consumidor de forma remota, aplican el derecho de revocación de 10 días hábiles y el Botón de Arrepentimiento/Baja de Servicio (Disposición 954/2025, ya en `legal/REFUND_POLICY.md`).

## Sostener el compromiso entre la reserva y la entrega (meses)

Sistema "Founding Customers": cada reservante recibe, desde el día de la reserva, un resumen claro (qué reservó, precio, monto de reserva, estado, próximo checkpoint, ventana de entrega estimada — nunca fecha exacta).

**Una actualización mensual**, con solo 4 cosas: qué se terminó, qué se está haciendo, qué cambió del plan, próximo checkpoint. Ejemplo:
> Este mes cerramos el provisioning del Pod y la conexión MCP. Todavía estamos terminando el lifecycle y las pruebas de seguridad antes de entregar el primer entorno. El próximo checkpoint es validar el primer Pod completo. No necesitás hacer nada por ahora.

Nunca prometer fecha exacta ("el 17 de noviembre") — usar ventana ("noviembre-diciembre") y avisar antes si peligra. Condición explícita de la reserva: si no se entrega dentro de la ventana acordada, el cliente puede mantenerla, moverla a nueva fecha, o pedir el reintegro. Eso cambia la percepción de "le di plata a un pibe que desapareció" a "tengo una reserva documentada y puedo recuperar mi plata".

## Cuándo pivotar (ventana de meses, no de 2 semanas)

**Ventana mínima por nicho: 12-16 semanas** (a 5 prospectos/semana, eso son 60-80 prospectos — recién ahí se puede ver un patrón real). Excepción: señal contundente y temprana (ej. 25 personas del perfil exacto dicen explícitamente que no tienen el problema) sí justifica pivotar antes.

Matriz de decisión a los 3-4 meses:

| Resultado acumulado | Lectura |
|---|---|
| Problema confirmado + piden solución + alguna señal monetaria | Seguir |
| Problema confirmado, precio rechazado repetidamente | Revisar oferta/precio, no el nicho |
| Mucho interés, poca confirmación del problema | Problema probablemente poco doloroso |
| Mucha conversación, cero intención monetaria | Revisar propuesta o nicho |
| Casi nadie responde tras muestra razonable | Revisar canal/targeting antes de matar el nicho |
| 0 reservas tras 60-80 prospectos bien seleccionados | Pivot serio |

"No pagan" no siempre significa nicho equivocado — puede ser nicho correcto + precio incorrecto, o nicho correcto + mensaje incorrecto. Antes de pivotar, identificar en qué etapa del embudo se rompe (interés→problema→precio→reserva), no solo el resultado final.

## Horizonte de referencia
Mes 1: aprender quién responde y por qué. Mes 2: confirmar patrones del problema. Mes 3: meter precio real y buscar primeras reservas. Mes 4: decisión de nicho (seguir / ajustar oferta / pivotar). Meses 5-6: solo si hubo señales positivas pero insuficientes.

## Registro de contactos
Planilla simple: `fecha | nombre | canal | etapa alcanzada | respuesta | notas`.

## Próximo paso después de validar
Con 3 reservas reales: no se construye "el MVP completo" — se construye lo mínimo de la **Fase 1** (`MASTER_SPEC.md` §3/§8): Polar + backend mínimo + Hetzner API + 1 pod MCP Terminal + auth + firewall + MCP + A2A básico + logs + kill switch + lifecycle + legales. Provisioning manual para los primeros 1-3 clientes.

**El objetivo real no es "conseguir 3 personas rápido"**, es mantener durante meses un flujo pequeño pero constante de conversaciones suficientemente buenas hasta conseguir 3 señales monetarias reales, sin gastar capital ni quemar al founder.
