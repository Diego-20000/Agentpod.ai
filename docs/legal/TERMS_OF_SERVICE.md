# AgentPod — Términos de Servicio

**Borrador operativo — reemplazar placeholders antes de publicar. No sustituye revisión jurídica argentina.**

Última actualización: [FECHA]

Estos Términos de Servicio ("Términos") regulan el acceso y uso de AgentPod, un servicio de infraestructura en la nube que proporciona entornos informáticos dedicados para ejecutar agentes de inteligencia artificial, herramientas de desarrollo, código y procesos del cliente.

Proveedor: [NOMBRE Y APELLIDO LEGAL COMPLETO], persona humana domiciliada en [DOMICILIO], Argentina, CUIT [CUIT], correo de contacto: [EMAIL].

## 1. Aceptación
Al crear una cuenta, contratar un pod o utilizar AgentPod, aceptás estos Términos, la Acceptable Use Policy, la Privacy Policy y la Refund Policy.
Si contratás en nombre de una empresa u otra organización, declarás que tenés autorización para obligarla contractualmente.
El servicio está destinado a personas mayores de 18 años y a entidades cuyos representantes tengan capacidad para contratar.

## 2. Qué proporciona AgentPod
AgentPod proporciona infraestructura administrada que puede incluir una máquina virtual dedicada, sistema operativo, herramientas de desarrollo, servidor MCP, soporte A2A, terminal web y otros componentes indicados para el pod contratado.
La infraestructura subyacente puede ser proporcionada por terceros, incluido Hetzner.
AgentPod no proporciona ni revende modelos de inteligencia artificial ni sus tokens. El cliente es responsable de sus propias cuentas y credenciales de proveedores de IA ("BYOK").

## 3. Responsabilidad del cliente
El cliente es responsable de todo código, archivo, dato, instrucción, credencial, agente, proceso y contenido que almacene, ejecute, transmita o permita ejecutar dentro de su pod.
Esto incluye la legalidad de las acciones realizadas por sus agentes y de los sistemas externos a los que dichos agentes accedan.
El cliente debe mantener seguras sus credenciales, claves API y tokens de AgentPod y notificarnos inmediatamente si cree que alguno fue comprometido.
AgentPod no autoriza al cliente a utilizar la infraestructura para actividades ilegales, abusivas o prohibidas por estos Términos.

## 4. Seguridad y suspensión
AgentPod puede restringir o suspender inmediatamente un pod, sin aviso previo, cuando exista una sospecha razonable de:
- violación de la Acceptable Use Policy;
- compromiso de una cuenta, token o servidor;
- malware, cryptomining, botnet u otra actividad maliciosa;
- ataques, escaneo abusivo, spam o tráfico que pueda perjudicar a terceros o a la infraestructura de AgentPod;
- riesgo para otros clientes, proveedores o la cuenta de infraestructura de AgentPod;
- fraude o intento de fraude;
- incumplimiento material de estos Términos.

Cuando sea razonablemente posible, AgentPod puede conservar información técnica necesaria para investigar el incidente.
Una suspensión por abuso no implica automáticamente la eliminación de los datos. La eliminación seguirá el procedimiento de lifecycle y retención aplicable (ver `docs/OPERATIONS.md`), salvo que sea necesario actuar inmediatamente por razones de seguridad o legales.

## 5. Disponibilidad
AgentPod intentará mantener el servicio operativo, pero no garantiza disponibilidad del 100%.
El servicio puede verse afectado por mantenimiento, fallas de proveedores de infraestructura, problemas de red, errores de software, abuso, incidentes de seguridad, fuerza mayor u otras circunstancias fuera de nuestro control razonable.
El objetivo de despliegue rápido comunicado comercialmente es un objetivo operativo y no constituye una garantía contractual de tiempo exacto.

## 6. Datos del cliente
El cliente conserva sus derechos sobre sus archivos, código, credenciales y demás contenido introducido en su pod.
AgentPod obtiene únicamente los permisos necesarios para operar, proteger, mantener y prestar el servicio, además de aquellos necesarios para cumplir obligaciones legales.
No utilizaremos el contenido del cliente para entrenar modelos de inteligencia artificial propios.

## 7. Cancelación y falta de pago
Las suscripciones son recurrentes según el producto contratado y son procesadas mediante Polar, que actúa como Merchant of Record y reseller del producto.
El cliente puede cancelar la suscripción conforme al mecanismo disponible en el proceso de compra o en el portal correspondiente.
Ante un pago vencido (ver `docs/OPERATIONS.md` §5):
- Día 1: aviso de pago pendiente.
- Día 3: el acceso al pod pasa a `restricted`.
- Día 10: el pod pasa a `archived`; se conserva un snapshot durante 7 días y posteriormente se elimina de forma definitiva, salvo obligación legal o acuerdo distinto.

La cancelación evita renovaciones futuras. Los derechos legales de consumidores y cualquier derecho de reembolso aplicable conforme a la legislación vigente prevalecen sobre esta sección.

## 8. Eliminación y recuperación
Cuando un pod se archiva, AgentPod puede generar un snapshot de recuperación con una retención de 7 días.
Finalizado ese período, el snapshot y los datos asociados podrán eliminarse de forma permanente.
El cliente es responsable de mantener copias adicionales de los datos que considere críticos.

## 9. Propiedad intelectual
AgentPod y sus componentes propios, incluyendo software, interfaz, marca, documentación y código desarrollado por AgentPod, pertenecen al Proveedor o a sus respectivos licenciantes.
Estos Términos no transfieren al cliente propiedad sobre dichos componentes.
El cliente conserva sus derechos sobre el contenido que introduce en su pod.

## 10. Limitación de responsabilidad
En la máxima medida permitida por la legislación aplicable, AgentPod no será responsable por pérdidas indirectas, incidentales, especiales, consecuenciales, pérdida de beneficios, pérdida de oportunidades comerciales o pérdida de datos derivadas del uso del servicio.
Para clientes que contraten exclusivamente con finalidad empresarial y en la medida permitida por la ley, la responsabilidad contractual total de AgentPod relacionada con el servicio estará limitada al mayor de:
(a) los importes efectivamente pagados por el cliente a AgentPod durante los tres meses anteriores al hecho que originó el reclamo; o
(b) USD 100.

**Esta limitación no se aplica en la medida en que una ley aplicable prohíba limitar o excluir determinada responsabilidad, incluyendo derechos irrenunciables de consumidores** (Ley 24.240, Art. 37 — cláusulas que limiten la responsabilidad por daños en una relación de consumo son ineficaces).

## 11. Cambios
Podemos modificar estos Términos cuando sea necesario para reflejar cambios en el servicio, seguridad, legislación o funcionamiento de AgentPod.
Los cambios materiales serán comunicados mediante el sitio, el servicio o correo electrónico cuando corresponda.
El uso continuado del servicio después de la entrada en vigor de los cambios constituye aceptación, salvo que la legislación aplicable exija otra forma.

## 12. Contacto
[NOMBRE Y APELLIDO LEGAL COMPLETO]
[DOMICILIO]
CUIT: [CUIT]
Email: [EMAIL]
