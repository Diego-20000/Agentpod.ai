# AgentPod — Acceptable Use Policy

**Borrador operativo — reemplazar placeholders antes de publicar. No sustituye revisión jurídica argentina.**

Última actualización: [FECHA]

AgentPod proporciona infraestructura capaz de ejecutar código real. Por eso esta política es parte esencial de las condiciones de uso.
El cliente es responsable de todas las actividades realizadas desde su pod, incluidos los actos realizados automáticamente por sus agentes.

## 1. Usos prohibidos
Está prohibido utilizar AgentPod para:

**Malware y compromiso de sistemas**
Crear, distribuir o ejecutar malware, ransomware, spyware, keyloggers, backdoors, botnets, payloads maliciosos o herramientas destinadas a obtener acceso no autorizado.

**Ataques contra terceros**
Realizar DDoS, DoS, explotación no autorizada de vulnerabilidades, credential stuffing, brute force, phishing, robo de credenciales, explotación de cuentas o cualquier ataque contra sistemas que el cliente no esté autorizado a probar.
Las pruebas de seguridad sobre sistemas propios o sobre los que el cliente tenga autorización explícita son permitidas siempre que no generen tráfico abusivo ni infrinjan esta política o las reglas del proveedor de infraestructura.

**Escaneo abusivo**
Escanear masivamente Internet, realizar port scanning indiscriminado, enumeración masiva de servicios o reconocimiento automatizado contra terceros sin autorización.

**Spam y abuso de red**
Enviar spam, correo no solicitado en volumen, campañas automatizadas abusivas, tráfico diseñado para evadir filtros, relay de correo no autorizado o utilizar el pod como infraestructura de proxy o retransmisión para ocultar actividades abusivas.

**Cryptomining y uso no autorizado de recursos**
Ejecutar cryptomining, participar en pools de minería o utilizar CPU, RAM, red o almacenamiento de AgentPod para generar activos digitales mediante procesos de minería.

**Evasión y abuso de proveedores**
Intentar ocultar el origen de actividades prohibidas, evadir mecanismos de suspensión, falsificar identidad, utilizar múltiples cuentas para eludir límites o crear cuentas mediante información falsa o medios de pago fraudulentos.

**Infracción de derechos**
Utilizar AgentPod para actividades que infrinjan derechos de propiedad intelectual, privacidad, protección de datos u otras obligaciones legales.

**Contenido ilegal**
Almacenar, transmitir o distribuir contenido cuya posesión, distribución o uso sea ilegal conforme a la legislación aplicable.

## 2. Credenciales y acceso
El cliente debe mantener seguras sus credenciales.
Si un token o cuenta resulta comprometido, debe avisar inmediatamente a AgentPod y solicitar su revocación o suspensión.
AgentPod puede revocar credenciales comprometidas sin previo aviso cuando exista riesgo para la seguridad.

## 3. Detección y respuesta
AgentPod puede utilizar métricas técnicas, registros, tráfico de red y señales automatizadas para detectar abuso (ver `docs/OPERATIONS.md` §2 — detección con Netdata Agent, sin ML ni SIEM).

Las señales pueden incluir, entre otras:
- crecimiento anormal de conexiones;
- conexiones masivas a numerosos destinos;
- actividad relacionada con SMTP no autorizada;
- patrones compatibles con escaneo;
- actividad que pueda perjudicar la infraestructura de terceros.

Una señal automática no implica por sí sola que exista una infracción. AgentPod puede investigar el contexto antes de adoptar una medida definitiva.

## 4. Suspensión inmediata
Ante una amenaza activa o riesgo significativo, AgentPod puede suspender inmediatamente el pod sin previo aviso (ver "Suspend Pod" en `docs/OPERATIONS.md` §2 y `docs/ABUSE_RUNBOOK.md`).

La suspensión puede incluir:
1. bloqueo de tráfico entrante y saliente;
2. apagado del servidor;
3. revocación de credenciales;
4. conservación temporal de evidencia técnica;
5. eliminación del servidor cuando sea necesaria por seguridad o cuando corresponda según los Términos.

## 5. Reportes de abuso
Los reportes de abuso pueden enviarse a: [ABUSE EMAIL]
AgentPod cooperará razonablemente con proveedores de infraestructura y autoridades competentes cuando exista una obligación legal válida o un riesgo operativo que requiera intervención.

## 6. Incumplimiento
Una violación de esta política puede provocar suspensión, archivado, eliminación del pod, cancelación de la cuenta y pérdida de acceso al servicio.
Cuando corresponda, AgentPod también puede informar el incidente al proveedor de infraestructura o a las autoridades competentes.
La aplicación de esta política no limita otros derechos legales o contractuales del Proveedor.
