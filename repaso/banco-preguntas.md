# Banco de preguntas tipo CLF-C02

Formato: pregunta → opciones → respuesta + justificación breve. Secciones por clase.

## Clase 01

**1. `[D1]`** Una empresa quiere dejar de comprar servidores por adelantado y pagar solo lo que consume. ¿Qué ventaja de la nube describe esto?
A) Alcance mundial en minutos · B) Cambiar gasto fijo por gasto variable · C) Economías de escala · D) Aumentar la agilidad
<details><summary>Respuesta</summary>B. CAPEX → OPEX.</details>

**2. `[D1]`** ¿Qué ventaja permite que AWS ofrezca precios más bajos porque agrega el uso de cientos de miles de clientes?
A) Dejar de adivinar la capacidad · B) Economías de escala · C) Elasticidad · D) Alta disponibilidad
<details><summary>Respuesta</summary>B. Más volumen implica menor costo unitario.</details>

**3. `[D1]`** Una tienda tiene picos de tráfico impredecibles y no quiere sobredimensionar servidores. ¿Qué ventaja aplica?
A) Dejar de adivinar la capacidad · B) Alcance mundial · C) Economías de escala · D) Soberanía de datos
<details><summary>Respuesta</summary>A. Elasticidad: escala bajo demanda.</details>

**4. `[D3]`** Una entidad pública debe mantener sus datos confidenciales dentro del país, pero quiere usar la nube pública para el resto. ¿Qué modelo de implementación le conviene?
A) Pública · B) Híbrida · C) Comunitaria · D) SaaS
<details><summary>Respuesta</summary>B. Datos sensibles on-premises o en nube privada, el resto en la pública.</details>

**5. `[D1]`** ¿Qué modelo de servicio corresponde a Amazon EC2?
A) SaaS · B) PaaS · C) IaaS · D) FaaS
<details><summary>Respuesta</summary>C. Entrega servidores virtuales; tú administras el SO y lo que va encima.</details>

**6. `[D3]`** ¿Qué determina el *tipo de instancia* de EC2 (ej. `t3.micro`)?
A) El sistema operativo · B) La región · C) La capacidad de CPU y memoria · D) El tipo de disco
<details><summary>Respuesta</summary>C. El SO lo define la AMI.</details>

**7. `[D3]`** Un banco necesita ejecutar servicios de AWS dentro de su propio centro de datos por requisitos de residencia de datos. ¿Qué servicio usa?
A) AWS Snowball · B) AWS Outposts · C) Amazon CloudFront · D) AWS Direct Connect
<details><summary>Respuesta</summary>B. Outposts lleva la infraestructura de AWS al sitio del cliente. Snowball sirve para mover datos de forma física.</details>

## Clase 02

**1. `[D2]`** ¿Qué componente de AWS actúa como firewall virtual a nivel de instancia EC2 para controlar el tráfico de entrada y salida?
A) Amazon VPC · B) Security group · C) AWS Shield · D) Subred
<details><summary>Respuesta</summary>B. La VPC es la red; el security group filtra el tráfico de cada instancia.</details>

**2. `[D3]`** Una empresa necesita lanzar 20 servidores idénticos con el mismo SO y el mismo software ya instalado. ¿Qué debe usar?
A) Un tipo de instancia más grande · B) Una AMI personalizada · C) Un par de claves · D) Un security group
<details><summary>Respuesta</summary>B. La AMI es la plantilla que copia el SO y el software en cada instancia nueva.</details>

**3. `[D2]`** Un usuario no puede hacer ping a su instancia EC2, pero sí entra por SSH. ¿Qué debe revisar primero?
A) El tipo de instancia · B) La AMI · C) Las reglas de entrada del security group · D) El volumen EBS
<details><summary>Respuesta</summary>C. Falta una regla de entrada ICMP. SSH funciona porque el puerto 22 sí está permitido.</details>

**4. `[D3]`** ¿Qué servicio permite crear una red lógicamente aislada dentro de la nube de AWS?
A) Amazon VPC · B) Amazon EC2 · C) AWS Outposts · D) Amazon CloudFront
<details><summary>Respuesta</summary>A. Virtual Private Cloud.</details>

**5. `[D2]`** ¿Qué se necesita para descifrar la contraseña de administrador de una instancia EC2 con Windows?
A) La access key de la AWS CLI · B) La llave privada del par de claves usado al lanzarla · C) El ID de la AMI · D) El security group
<details><summary>Respuesta</summary>B. AWS cifra la contraseña con la llave pública; solo la llave privada (`.pem`) la descifra.</details>

**6. `[D3]` `[externo]`** Un servidor web debe conservar la misma IP pública aunque se detenga y se vuelva a iniciar. ¿Qué se usa?
A) DNS público · B) Elastic IP · C) Una subred nueva · D) Una AMI
<details><summary>Respuesta</summary>B. La IP pública automática cambia al detener e iniciar la instancia; la Elastic IP es fija.</details>

**7. `[D2]` `[externo]`** Un security group recién creado no tiene reglas personalizadas. ¿Cuál es su comportamiento?
A) Permite toda la entrada y bloquea toda la salida · B) Bloquea toda la entrada y permite toda la salida · C) Bloquea todo · D) Permite todo
<details><summary>Respuesta</summary>B. Hay que agregar reglas de entrada de forma explícita; la salida viene abierta (All traffic).</details>

## Clase 03

**1. `[D3]`** Una empresa quiere que cada instancia EC2 nueva instale y arranque un servidor web automáticamente al lanzarse, sin conectarse por SSH. ¿Qué debe usar?
A) Un security group · B) Datos de usuario (user data) · C) Un par de claves · D) Una Elastic IP
<details><summary>Respuesta</summary>B. El user data ejecuta el script en el primer arranque de la instancia.</details>

**2. `[D3]`** ¿Cuándo se ejecuta por defecto el script de user data de una instancia Linux?
A) En cada reinicio · B) Solo en el primer arranque · C) Cada hora · D) Solo al detenerla
<details><summary>Respuesta</summary>B. Por defecto corre una vez por instancia (`once-per-instance`).</details>

**3. `[D3]`** Hay que lanzar muchas instancias con el mismo software y que arranquen lo más rápido posible, sin esperar instalaciones. ¿Qué opción conviene más?
A) User data que instale todo · B) Una AMI personalizada con el software ya instalado · C) Instalar a mano por SSH · D) Un tipo de instancia más grande
<details><summary>Respuesta</summary>B. Con la AMI el software ya viene instalado; el user data lo instala en cada arranque inicial y eso toma tiempo.</details>

**4. `[D4]` `[externo]`** ¿Qué plan de AWS Support viene incluido sin costo en todas las cuentas?
A) Business Support+ · B) Enterprise · C) Basic · D) Unified Operations
<details><summary>Respuesta</summary>C. Basic: atención de cuenta y facturación, documentación, re:Post y checks básicos de Trusted Advisor; sin casos técnicos.</details>

**5. `[D4]` `[externo]`** Una empresa con cargas críticas en producción necesita un Technical Account Manager (TAM) asignado. ¿Qué plan de soporte necesita?
A) Basic · B) Business Support+ · C) Enterprise · D) Developer
<details><summary>Respuesta</summary>C. El TAM designado aparece desde Enterprise (y sigue en Unified Operations). En los planes clásicos también era Enterprise.</details>

**6. `[D4]` `[externo]`** ¿Cuál es el plan de AWS Support más económico que AWS recomienda para cargas en producción, con acceso 24/7 a ingenieros de soporte?
A) Basic · B) Business Support+ · C) Enterprise · D) Unified Operations
<details><summary>Respuesta</summary>B. Desde USD 29/mes; Basic no tiene casos técnicos. En los planes clásicos la respuesta era Business.</details>

## Clase 04

**1. `[D3]`** Un administrador quiere automatizar con scripts la creación diaria de instancias EC2 desde su terminal. ¿Qué herramienta debe usar?
A) AWS Management Console · B) AWS CLI · C) AWS Support · D) AWS Trusted Advisor
<details><summary>Respuesta</summary>B. La AWS CLI ejecuta comandos de AWS desde la terminal y se puede usar en scripts.</details>

**2. `[D3]`** Un desarrollador necesita que su aplicación en Python suba archivos a AWS directamente desde el código. ¿Qué debe usar?
A) AWS SDK · B) AWS Management Console · C) Un par de claves de EC2 · D) Datos de usuario
<details><summary>Respuesta</summary>A. Los SDK son librerías para usar AWS desde el código de un programa (en Python: Boto3 `[externo]`).</details>

**3. `[D2]`** ¿Qué credenciales necesita la AWS CLI para acceder a una cuenta?
A) Usuario y contraseña de la consola · B) Un archivo `.pem` · C) Access key ID y clave secreta de acceso · D) El ID de la cuenta
<details><summary>Respuesta</summary>C. El acceso programático usa access keys. El `.pem` sirve para entrar a la instancia, no a la cuenta de AWS.</details>

**4. `[D3]`** ¿Cuáles son formas de acceder a los servicios de AWS de manera programática? (Elige dos)
A) AWS CLI · B) AWS Management Console · C) AWS SDK · D) Amazon VPC · E) Security groups
<details><summary>Respuesta</summary>A y C. La consola es acceso gráfico, no programático.</details>

**5. `[D2]` `[externo]`** Un desarrollador subió por error su access key a un repositorio público. ¿Qué debe hacer primero?
A) Borrar el repositorio y seguir usando la clave · B) Desactivar la access key y crear una nueva · C) Cambiar la contraseña de la consola · D) Cambiar la región por defecto
<details><summary>Respuesta</summary>B. Una access key expuesta debe desactivarse y rotarse de inmediato; cambiar la contraseña no la invalida.</details>

## Clase 05

**1. `[D2]`** ¿Cómo se comporta por defecto un security group recién creado?
A) Permite toda la entrada y toda la salida · B) Deniega toda la entrada y permite toda la salida · C) Deniega toda la entrada y toda la salida · D) Permite solo SSH
<details><summary>Respuesta</summary>B. Hasta que se agregan reglas de entrada, nadie puede llegar a la instancia; la salida está abierta.</details>

**2. `[D2]`** Un security group permite entrada HTTP (80). ¿Hace falta una regla de salida para que la respuesta llegue al visitante?
A) Sí, una regla de salida HTTP · B) Sí, una regla de salida en el puerto 80 y otra en el 443 · C) No, los security groups son stateful · D) No, pero solo si la instancia tiene Elastic IP
<details><summary>Respuesta</summary>C. El tráfico de respuesta a una conexión permitida sale automáticamente `[externo]`.</details>

**3. `[D3]`** ¿Qué significa el origen `0.0.0.0/0` en una regla de entrada?
A) Ninguna dirección · B) Solo la VPC · C) Cualquier dirección IPv4 · D) Solo la IP del administrador
<details><summary>Respuesta</summary>C. Cualquier IPv4. Una sola IP se expresa con `/32`.</details>

**4. `[D3]`** Un administrador quiere crear un security group y dos reglas desde un script, sin usar la consola. ¿Qué herramienta usa?
A) AWS Support · B) AWS CLI · C) Amazon S3 · D) AWS Trusted Advisor
<details><summary>Respuesta</summary>B. `create-security-group` y `authorize-security-group-ingress` son comandos de la AWS CLI.</details>

**5. `[D2]` `[externo]`** Para que una instancia pueda recibir tráfico web y SSH, ¿cuántas reglas de entrada necesita su security group como mínimo?
A) Una: HTTP · B) Dos: HTTP (80) y SSH (22) · C) Tres: HTTP, HTTPS y SSH · D) Ninguna, la entrada está abierta por defecto
<details><summary>Respuesta</summary>B. Cada puerto necesita su propia regla de entrada.</details>

**6. `[D2]` `[externo]`** Con la regla de salida por defecto, un administrador agrega una regla de salida solo para HTTPS. ¿Queda la salida restringida a HTTPS?
A) Sí, la nueva regla reemplaza a la anterior · B) No, se suman: sigue permitida toda la salida · C) Sí, pero solo para IPv6 · D) No, la regla se rechaza por duplicada
<details><summary>Respuesta</summary>B. Las reglas se acumulan (solo permiten). Hay que revocar la regla *All traffic* para restringir.</details>

**7. `[D3]` `[externo]`** Una empresa define su infraestructura en archivos de texto y una herramienta la crea automáticamente. ¿Qué práctica es?
A) Infraestructura como código · B) Balanceo de carga · C) Acceso por consola · D) Snapshot de EBS
<details><summary>Respuesta</summary>A. Terraform es una herramienta de terceros para esto; el servicio de AWS equivalente es AWS CloudFormation.</details>

## Clase 06

**1. `[D3]`** Una empresa quiere crear las mismas instancias y redes en varios entornos a partir de archivos de configuración versionados. ¿Qué enfoque es?
A) Infraestructura como código · B) Configuración manual en la consola · C) Snapshots de EBS · D) Soporte Enterprise
<details><summary>Respuesta</summary>A. La IaC define la infraestructura en archivos y la crea de forma repetible.</details>

**2. `[D3]` `[externo]`** ¿Qué servicio de AWS permite hacer infraestructura como código de forma nativa?
A) AWS CloudFormation · B) AWS Trusted Advisor · C) Amazon CloudWatch · D) AWS Support
<details><summary>Respuesta</summary>A. Terraform es una herramienta de terceros equivalente; CloudFormation es el servicio de AWS.</details>

**3. `[D1]`** ¿Qué ventaja ofrece la automatización de la infraestructura con código?
A) Elimina la necesidad de cuenta de AWS · B) Reduce errores manuales y hace los despliegues repetibles · C) Aumenta el costo de cada recurso · D) Sustituye el modelo de responsabilidad compartida
<details><summary>Respuesta</summary>B. Se puede crear, actualizar y borrar la misma infraestructura de forma repetible.</details>

**4. `[D3]`** En un enfoque declarativo, ¿qué se describe?
A) Los pasos para crear cada recurso · B) El estado final deseado de la infraestructura · C) Solo los costos · D) Las credenciales
<details><summary>Respuesta</summary>B. Se declara el estado objetivo; la herramienta decide cómo alcanzarlo.</details>

**5.** En Terraform, ¿qué comando muestra los cambios que se harían sin ejecutarlos?
A) `terraform init` · B) `terraform plan` · C) `terraform apply` · D) `terraform destroy`
<details><summary>Respuesta</summary>B. `plan` analiza el `main.tf`; `apply` ejecuta; `destroy` elimina; `init` prepara el proyecto.</details>

**6. `[D2]` `[externo]`** Un security group declarado con Terraform tiene un solo bloque `egress` para el puerto 80. ¿Qué tráfico saliente permite la instancia?
A) Todo · B) Solo TCP 80 · C) Solo HTTPS · D) Ninguno
<details><summary>Respuesta</summary>B. Terraform quita la regla de salida por defecto al declarar `egress`.</details>

## AWS Academy: Introducción (M0)

**1. `[D3]` `[externo]`** Una empresa quiere crear una aplicación de IA generativa usando modelos fundacionales sin administrar infraestructura. ¿Qué servicio usa?
A) Amazon Bedrock · B) Amazon Q Developer · C) Amazon EBS · D) AWS Elastic Beanstalk
<details><summary>Respuesta</summary>A. Bedrock es un servicio completamente administrado con modelos fundacionales. Q Developer es un asistente para programar.</details>

**2. `[D3]` `[externo]`** Un equipo quiere un asistente que responda preguntas con los datos internos de la empresa. ¿Cuál elige?
A) Amazon Q Developer · B) Amazon Q Business · C) AWS Trusted Advisor · D) Amazon CloudWatch
<details><summary>Respuesta</summary>B. Q Business se adapta a los datos y sistemas de la empresa; Q Developer es para desarrollo de software.</details>

**3.** ¿Qué certificación AWS es la única clasificada como *Foundational*?
A) Solutions Architect – Associate · B) Cloud Practitioner · C) DevOps Engineer – Professional · D) Security – Specialty
<details><summary>Respuesta</summary>B. Cloud Practitioner (seis meses de conocimiento básico de AWS).</details>

**4.** Al llegar a cero el cronómetro de un laboratorio de AWS Academy, ¿qué ocurre?
A) Se pausa hasta el día siguiente · B) Se terminan los servicios configurados y se desactiva la cuenta temporal · C) Se renueva automáticamente · D) Se cobra a la tarjeta del estudiante
<details><summary>Respuesta</summary>B. Con **Start Lab** se reinicia el cronómetro; la cuenta es temporal.</details>

**5.** ¿Dónde se encuentran los whitepapers y las guías de AWS por servicio?
A) En AWS Marketplace · B) En la documentación de AWS (docs.aws.amazon.com) y la página de whitepapers · C) Solo en AWS Support · D) En la AWS CLI
<details><summary>Respuesta</summary>B. La documentación se filtra por producto, categoría o sector.</details>

## AWS Educate: Getting Started with Storage (2026-11-03)

Evaluación final aprobada con 100 %. Las preguntas 1–8 resumen la evaluación; las 9–14 son de práctica, sobre lo que más se confunde. Teoría: [M7](../modulos/m07-almacenamiento.md).

**1. `[D3]`** ¿Qué tipo de almacenamiento guarda datos en bloques de tamaño fijo, cada uno con un identificador único?
A) Objeto · B) Archivo · C) Bloque · D) Gateway
<details><summary>Respuesta</summary>C. Objeto = datos + metadatos + clave; archivo = jerarquía de carpetas.</details>

**2. `[D3]`** Un hospital debe conservar 7 años de historias clínicas que casi nunca consulta. ¿Qué clase de S3 conviene?
A) S3 Standard · B) Intelligent-Tiering · C) Glacier Deep Archive · D) Standard-IA
<details><summary>Respuesta</summary>C. Retención larga por cumplimiento al menor costo; tarda horas en recuperar, pero aquí no importa.</details>

**3. `[D2]`** ¿Para qué estados de los datos ofrece cifrado S3? (Elige dos)
A) En reposo · B) En el borde · C) En tránsito · D) En archivo · E) Desde endpoints
<details><summary>Respuesta</summary>A y C. Del lado del servidor cifra en reposo; del lado del cliente o con TLS, en tránsito.</details>

**4. `[D3]`** ¿Dónde debe ser único el nombre de un bucket de S3?
A) En la cuenta · B) En la región · C) En todas las cuentas de AWS del mundo · D) En la organización
<details><summary>Respuesta</summary>C. El espacio de nombres es global.</details>

**5. `[D2]`** Si un desarrollador crea un bucket sin cambiar los valores por defecto, ¿quién puede ver sus objetos?
A) El público · B) Su organización · C) Solo el desarrollador (el dueño) · D) Los usuarios de su app
<details><summary>Respuesta</summary>C. Todo en S3 es privado por defecto y Block Public Access viene activado.</details>

**6. `[D4]`** ¿Qué se cobra en S3? (Elige tres)
A) GB almacenados por mes · B) Transferencia de entrada · C) Salida a internet o a otra región · D) DELETE y CANCEL · E) PUT, COPY, POST, LIST y GET · F) Salida a EC2 de la misma región
<details><summary>Respuesta</summary>A, C y E. Subir datos es gratis; sacarlos de la región o a internet, no.</details>

**7. `[D3]`** ¿Qué beneficio da la Cross-Region Replication?
A) Menos instancias EC2 · B) Menor latencia con copias en regiones cercanas a los usuarios · C) Más administradores · D) Menos seguimiento
<details><summary>Respuesta</summary>B. También sirve para recuperación ante desastres con más distancia.</details>

**8. `[D3]`** ¿Qué dos tipos de acciones aplican las reglas de ciclo de vida? (Elige dos)
A) Transición · B) Alerta · C) Expiración · D) Duplicación · E) Archivo
<details><summary>Respuesta</summary>A y C. La transición cambia la clase; la expiración borra.</details>

**9. `[D3]`** Tres instancias EC2 en distintas AZ deben leer y escribir la misma carpeta de archivos. ¿Qué servicio usas?
A) Amazon EBS · B) Amazon EFS · C) Instance store · D) S3 Glacier
<details><summary>Respuesta</summary>B. EBS vive en una sola AZ y se conecta a una instancia; EFS es compartido y multi-AZ.</details>

**10. `[D3]`** Miniaturas que casi nadie abre después de 60 días, pero que deben abrirse al instante. ¿Qué clase eliges?
A) One Zone-IA · B) Standard-IA · C) Glacier Flexible Retrieval · D) Standard
<details><summary>Respuesta</summary>B, según el curso. One Zone-IA solo si el enunciado dice que perder los datos es aceptable.</details>

**11. `[D2]`** Debes compartir un PDF privado de S3 con un cliente durante 1 hora, sin hacerlo público. ¿Qué usas?
A) ACL pública · B) Presigned URL · C) Bucket policy con `Principal: *` · D) Transfer Acceleration
<details><summary>Respuesta</summary>B. Da acceso temporal; al vencer, devuelve Access denied.</details>

**12. `[D3]`** Una empresa con usuarios en varios continentes sube con frecuencia GB de datos a un único bucket. ¿Qué acelera esas subidas?
A) Snowball · B) S3 Transfer Acceleration · C) SRR · D) Multipart upload solamente
<details><summary>Respuesta</summary>B. Usa las edge locations de CloudFront. Snowball se usa para volúmenes enormes con poca conectividad.</details>

**13. `[D3]`** ¿Cómo se respalda un volumen EBS y dónde queda el respaldo?
A) Con AMI, en EFS · B) Con snapshots incrementales, en S3 · C) Con versionado, en EBS · D) Con replicación, en otra AZ
<details><summary>Respuesta</summary>B. Cada snapshot guarda solo los bloques que cambiaron.</details>

**14. `[D2]`** Según el modelo de responsabilidad compartida, ¿quién configura el cifrado del lado del servidor de un bucket y sus permisos?
A) AWS · B) El cliente · C) Ambos por igual · D) AWS Support
<details><summary>Respuesta</summary>B. AWS protege la infraestructura (seguridad de la nube); el cliente, sus datos y su configuración (seguridad en la nube).</details>

## Tarea 5: AWS Well-Architected Framework `[externo]`

**1. `[D1]`** ¿Cuál de estos NO es un pilar del AWS Well-Architected Framework?
A) Sostenibilidad · B) Fiabilidad · C) Escalabilidad · D) Optimización de costos
<details><summary>Respuesta</summary>C. Los seis pilares son excelencia operativa, seguridad, fiabilidad, eficiencia del rendimiento, optimización de costos y sostenibilidad.</details>

**2. `[D1]`** Una empresa quiere que su carga de trabajo se recupere automáticamente de los fallos. ¿Qué pilar lo trata?
A) Fiabilidad · B) Seguridad · C) Sostenibilidad · D) Optimización de costos
<details><summary>Respuesta</summary>A. "Recuperarse automáticamente de fallos" es un principio de diseño de fiabilidad.</details>

**3. `[D1]`** Aplicar el mínimo privilegio y la trazabilidad corresponde al pilar de:
A) Excelencia operativa · B) Eficiencia del rendimiento · C) Seguridad · D) Fiabilidad
<details><summary>Respuesta</summary>C. Ambos son principios de diseño de seguridad.</details>

**4. `[D1]`** Apagar los entornos de desarrollo fuera del horario laboral aplica un principio de:
A) Fiabilidad · B) Optimización de costos · C) Seguridad · D) Excelencia operativa
<details><summary>Respuesta</summary>B. Es "adoptar un modelo de consumo": pagar solo por lo que se usa.</details>

**5. `[D1]`** Desplegar en varias regiones para reducir la latencia de los clientes corresponde al pilar de:
A) Sostenibilidad · B) Seguridad · C) Optimización de costos · D) Eficiencia del rendimiento
<details><summary>Respuesta</summary>D. "Globalizarse en minutos" es un principio de eficiencia del rendimiento.</details>

**6. `[D1]`** Hacer cambios frecuentes, pequeños y reversibles es un principio de:
A) Excelencia operativa · B) Sostenibilidad · C) Optimización de costos · D) Seguridad
<details><summary>Respuesta</summary>A. Reduce el radio de impacto y permite revertir más rápido.</details>

**7. `[D1]`** Maximizar la utilización de los recursos para reducir la energía necesaria corresponde al pilar de:
A) Fiabilidad · B) Eficiencia del rendimiento · C) Sostenibilidad · D) Seguridad
<details><summary>Respuesta</summary>C. Un host al 60 % es más eficiente que dos hosts al 30 %.</details>

**8. `[D1]`** ¿Qué es una revisión con el AWS Well-Architected Framework?
A) Una auditoría obligatoria · B) Una conversación constructiva sobre decisiones de arquitectura · C) Una certificación de AWS · D) Un plan de soporte
<details><summary>Respuesta</summary>B. La documentación aclara que no es un mecanismo de auditoría.</details>
