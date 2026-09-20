# AgentPod — Refund Policy

**Borrador operativo — reemplazar placeholders antes de publicar. No sustituye revisión jurídica argentina.**

Última actualización: [FECHA]

Esta Política regula reembolsos de los servicios de AgentPod cobrados mediante Polar.
Polar actúa como Merchant of Record y reseller del producto. El comprador adquiere el producto mediante Polar y los derechos de uso del servicio se encuentran sujetos a los términos del proveedor.

## 1. Falla permanente de provisioning
Si un cliente realizó un pago y AgentPod no puede provisionar permanentemente el pod contratado por una causa atribuible a nuestra infraestructura o a una limitación de provisioning que no pueda resolverse razonablemente (ver `docs/OPERATIONS.md` §5.5):
- no se entregará acceso a un pod incompleto;
- se cancelará el entitlement o suscripción correspondiente;
- se solicitará un reembolso completo del importe aplicable mediante Polar;
- el cliente recibirá una comunicación explicando el motivo de la falla.

No consideramos un timeout temporal o un incidente recuperable como una falla permanente. Primero intentaremos recuperar el provisioning.
Polar dispone de una operación específica para crear reembolsos (`POST /v1/refunds`) y admite, entre otros motivos, `service_disruption`. El estado del reembolso se confirma mediante su evento de webhook `order.refunded`.

## 2. Derecho de arrepentimiento
Nada de esta Política limita los derechos irrenunciables reconocidos por la legislación aplicable.
En particular, en contratos celebrados a distancia, el consumidor puede ejercer el derecho de revocación dentro de los **10 días hábiles** en los términos previstos por el Código Civil y Comercial de la Nación.
AgentPod proporcionará el mecanismo correspondiente para ejercer ese derecho cuando resulte aplicable, incluido el **"Botón de Arrepentimiento"** y el **"Botón de Baja de Servicio"** exigidos por la Disposición 954/2025 de la Subsecretaría de Defensa del Consumidor y Lealtad Comercial para la comercialización online de bienes y servicios a distancia — deben estar visibles desde el primer acceso, sin exigir registro previo ni trámites adicionales.

## 3. Cancelación de una suscripción
El cliente puede cancelar su suscripción mediante el mecanismo indicado en el checkout o portal de suscripción.
La cancelación evita renovaciones futuras. Los reembolsos de importes ya pagados se tramitarán únicamente cuando correspondan por esta Política, por la legislación aplicable o por una decisión expresa de AgentPod.
La baja no genera cargos de cancelación ni preavisos cuando la legislación aplicable prohíba dichos cargos.

## 4. Interrupciones del servicio
Si AgentPod experimenta una interrupción o incumplimiento relevante, evaluaremos el caso individualmente y podremos ofrecer un reembolso total, parcial o crédito cuando corresponda.
Nada de esta sección limita los derechos legales del cliente.

## 5. Abuso o violación de los Términos
La suspensión o cancelación por violación de la Acceptable Use Policy no genera automáticamente un derecho a reembolso.
Esta regla no afecta derechos de consumidores u otros derechos irrenunciables establecidos por la legislación aplicable.

## 6. Cómo se procesa el reembolso
Cuando corresponda un reembolso, se solicitará a través de Polar y se devolverá mediante el mecanismo de pago correspondiente, sujeto a los procesos del Merchant of Record y del medio de pago.
**Nota interna**: Polar no devuelve el fee de la transacción original al procesar un reembolso — puede generar una pequeña pérdida neta (ver `docs/OPERATIONS.md` §5.5). Por eso el reembolso automático de provisioning fallido se reserva a fallas no-retryable y verificadas, no a cualquier timeout.

## 7. Contacto
Para solicitudes relacionadas con reembolsos: [EMAIL DE SOPORTE]

Indicar:
- correo utilizado en la compra;
- número de pedido de Polar;
- pod afectado, si corresponde;
- motivo de la solicitud.
