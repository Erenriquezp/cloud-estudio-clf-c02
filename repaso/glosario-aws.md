# Glosario AWS

Orden alfabético. Una línea por término, con su dominio CLF y un enlace a la teoría.

| Término | Definición | CLF | Ver |
|---|---|---|---|
| `0.0.0.0/0` | Origen de una regla que significa "cualquier dirección IPv4" | D2 | [M5](../modulos/m05-redes.md#security-groups) |
| Access key | Credencial de acceso programático: ID (`aws_access_key_id`) + clave secreta (`aws_secret_access_key`) | D2 | [clase 04](../clases/2026-10-27-clase-04-aws-cli.md#conceptos-clave) |
| Almacenamiento de archivos | Jerarquía de carpetas en un sistema de archivos compartido (EFS) | D3 | [M7](../modulos/m07-almacenamiento.md#tipos-de-almacenamiento-d3) |
| Almacenamiento de bloque | Datos en bloques de tamaño fijo, cada uno con su ID; al modificar, cambia solo el bloque afectado (EBS) | D3 | [M7](../modulos/m07-almacenamiento.md#tipos-de-almacenamiento-d3) |
| Almacenamiento de objetos | Objeto = datos + metadatos + clave; al modificar, se reemplaza el objeto entero (S3) | D3 | [M7](../modulos/m07-almacenamiento.md#tipos-de-almacenamiento-d3) |
| Amazon Bedrock | Servicio completamente administrado con modelos fundacionales para crear aplicaciones de IA generativa | D3 `[externo]` | [M0](../modulos/m00-introduccion-curso.md#tendencias-ia-generativa) |
| Amazon EBS | Volúmenes de bloque para EC2; viven en una sola AZ y persisten aunque la instancia se detenga | D3 | [M7](../modulos/m07-almacenamiento.md#amazon-ebs-elastic-block-store) |
| Amazon EC2 | Servidores virtuales (instancias) bajo demanda; IaaS | D3 | [M6](../modulos/m06-computo.md) |
| Amazon EFS | Sistema de archivos compartido, elástico y administrado, en varias AZ; cifrado con KMS y TLS | D3 | [M7](../modulos/m07-almacenamiento.md#amazon-efs-elastic-file-system) |
| Amazon Macie | Detecta y protege datos sensibles en S3 | D2 | [M7](../modulos/m07-almacenamiento.md#seguridad-y-cumplimiento-de-s3-d2) |
| Amazon Q Business | Asistente de IA generativa que responde, resume y completa tareas con los datos de la empresa | D3 `[externo]` | [M0](../modulos/m00-introduccion-curso.md#tendencias-ia-generativa) |
| Amazon Q Developer | Asistente de IA generativa para programar: chatea sobre código, lo completa, lo genera y busca vulnerabilidades | D3 `[externo]` | [M0](../modulos/m00-introduccion-curso.md#tendencias-ia-generativa) |
| Amazon S3 | Almacenamiento de objetos en buckets; capacidad ilimitada, objetos de hasta 5 TB; 11 nueves de durabilidad `[externo]` | D3 | [M7](../modulos/m07-almacenamiento.md#amazon-s3-simple-storage-service) |
| Amazon VPC | Virtual Private Cloud: red privada lógica creada en la nube | D3 | [M5](../modulos/m05-redes.md) |
| AMI | Amazon Machine Image: plantilla de SO y software para lanzar una instancia | D3 | [M6](../modulos/m06-computo.md) |
| AMI personalizada | AMI creada desde una instancia ya configurada para lanzar copias idénticas | D3 | [M6](../modulos/m06-computo.md#amis-personalizadas) |
| AWS CLI | Interfaz de línea de comandos para administrar AWS (`aws <servicio> <acción>`); se configura con `aws configure` | D3 | [M1](../modulos/m01-conceptos-nube.md#formas-de-interactuar-con-aws) |
| AWS CloudTrail | Registra las llamadas a la API (quién hizo qué); sirve para auditar | D2 | [M7](../modulos/m07-almacenamiento.md#seguridad-y-cumplimiento-de-s3-d2) |
| AWS DataSync | Transferencia de datos online hacia AWS (por ejemplo, desde un Snowcone) | D3 | [M7](../modulos/m07-almacenamiento.md#mover-grandes-volúmenes-de-datos-a-s3) |
| `aws ec2 authorize-security-group-ingress` | Comando de la CLI que agrega una regla de **entrada** a un security group (`-egress` para salida) | D2 | [M5](../modulos/m05-redes.md#security-groups-con-la-aws-cli) |
| AWS Outposts | Hardware de AWS instalado en las instalaciones del cliente (nube privada o híbrida) | D3 | [M1](../modulos/m01-conceptos-nube.md#modelos-de-implementación) |
| AWS SDK | Librerías para usar AWS desde el código de una aplicación | D3 | [M1](../modulos/m01-conceptos-nube.md#formas-de-interactuar-con-aws) |
| AWS Snow Family | Dispositivos físicos para migrar datos: Snowcone (TB), Snowball (PB), Snowmobile (EB, hasta 100 PB) | D3 | [M7](../modulos/m07-almacenamiento.md#mover-grandes-volúmenes-de-datos-a-s3) |
| AWS Support | Servicio de soporte de cuenta, facturación y técnico; planes Basic (gratis), Business Support+, Enterprise y Unified Operations `[externo]` | D4 | [M2](../modulos/m02-economia-facturacion.md#aws-support) |
| AWS Trusted Advisor | Revisa la cuenta y recomienda mejoras de costo, seguridad, rendimiento y tolerancia a fallos `[externo]` | D4 | [M2](../modulos/m02-economia-facturacion.md#relacionados) |
| AWS Well-Architected Framework | Buenas prácticas para diseñar y operar cargas de trabajo en AWS, organizadas en seis pilares `[externo]` | D1 | [M9](../modulos/m09-arquitectura-nube.md#aws-well-architected-framework) |
| AWS Well-Architected Tool | Servicio sin costo para revisar cargas de trabajo contra el framework y detectar riesgos altos `[externo]` | D1 | [M9](../modulos/m09-arquitectura-nube.md#aws-well-architected-framework) |
| Block Public Access | Configuración de S3 que bloquea el acceso público; activa por defecto | D2 | [M7](../modulos/m07-almacenamiento.md#2-configurar-el-bucket) |
| Bucket | Contenedor de objetos de S3, creado en una región; su nombre es único en todo el mundo (3-63 caracteres: minúsculas, números, `.` y `-`) | D3 | [M7](../modulos/m07-almacenamiento.md#1-crear-el-bucket) |
| Bucket policy | Política JSON asignada al bucket que permite o niega acciones; un `Deny` explícito gana sobre cualquier `Allow` `[externo]` | D2 | [M7](../modulos/m07-almacenamiento.md#seguridad-del-bucket-d2) |
| CAPEX / OPEX | Gasto de capital inicial / gasto operativo variable | D1 | [M1](../modulos/m01-conceptos-nube.md#6-ventajas-de-la-nube) |
| Carga multiparte (multipart upload) | Sube un objeto en partes paralelas; recomendada por encima de 100 MB | D3 | [M7](../modulos/m07-almacenamiento.md#3-subir-objetos) |
| CIDR | Notación `IP/prefijo` de un rango de direcciones: `0.0.0.0/0` = cualquier IPv4; `/32` = una IP; `/24` = 256 | D3 | [clase 05](../clases/2026-10-29-clase-05-security-groups-cli.md#conceptos-clave) |
| Clave de objeto (key) | Identificador único de un objeto dentro del bucket (ej. `2021-04-13/myphotos.zip`) | D3 | [M7](../modulos/m07-almacenamiento.md#1-crear-el-bucket) |
| cloud-init | Programa del SO que lee y ejecuta el user data en el primer arranque | D3 | [M6](../modulos/m06-computo.md#datos-de-usuario-user-data) |
| Consola web (AWS Management Console) | Interfaz gráfica de AWS en el navegador | D3 | [M1](../modulos/m01-conceptos-nube.md#formas-de-interactuar-con-aws) |
| CRR / SRR | Replicación de S3 entre regiones (recuperación ante desastres, latencia) / dentro de la misma región (logs, soberanía de datos) | D3 | [M7](../modulos/m07-almacenamiento.md#funciones-adicionales) |
| Datos de usuario (user data) | Script que se pasa al lanzar una instancia y corre como root solo en su primer arranque | D3 | [M6](../modulos/m06-computo.md#datos-de-usuario-user-data) |
| DNS público | Nombre de host asociado a la IP pública de una instancia | D3 | [M5](../modulos/m05-redes.md) |
| Elastic IP | IP pública fija que no cambia al detener la instancia `[externo]` | D3 | [M5](../modulos/m05-redes.md) |
| Elasticidad | Escalar recursos hacia arriba o hacia abajo según la demanda | D1 | [M1](../modulos/m01-conceptos-nube.md#6-ventajas-de-la-nube) |
| Etiqueta (tag) | Par `Key=Value` asignado a un recurso; el nombre de una instancia es la etiqueta `Name` | D3 | [M6](../modulos/m06-computo.md#amazon-ec2-con-la-aws-cli) |
| gp3 | Tipo de volumen EBS SSD de propósito general; disco estándar de EC2 `[externo]` | D3 | [M6](../modulos/m06-computo.md) |
| ICMP | Protocolo de `ping`; requiere su propia regla de entrada en el security group | — | [M5](../modulos/m05-redes.md#security-groups) |
| Infraestructura como código (IaC) | Definir y crear la infraestructura con archivos de configuración (Terraform; en AWS, AWS CloudFormation `[externo]`) | D3 | [M1](../modulos/m01-conceptos-nube.md#infraestructura-como-código-iac) |
| Instance store | Disco local de la instancia; efímero `[externo]` | D3 | [M7](../modulos/m07-almacenamiento.md#amazon-ebs-elastic-block-store) |
| IPv4 privada | IP interna de la instancia dentro de la VPC (ej. `172.31.x.x`); la devuelve `hostname -I` | D3 | [clase 03](../clases/2026-10-22-clase-03-user-data.md#conceptos-clave) |
| IPv4 pública | IP para acceder desde internet; cambia al detener e iniciar la instancia `[externo]` | D3 | [M5](../modulos/m05-redes.md) |
| Learner Lab | Entorno sandbox de AWS Academy para practicar (Start Lab / End Lab); credenciales temporales | — | [clase 01](../clases/2026-10-15-clase-01-intro-nube-ec2.md#learner-lab-uso-en-clase) |
| MFA Delete | Exige un código MFA además de la contraseña para borrar objetos o versiones; requiere versionado `[externo]` | D2 | [M7](../modulos/m07-almacenamiento.md#5-borrar-objetos-y-buckets) |
| Modelo de responsabilidad compartida | AWS se encarga de la seguridad de la nube (hardware, infraestructura); el cliente, de la seguridad en la nube (datos, IAM, SO, cifrado) | D2 | [M7](../modulos/m07-almacenamiento.md#seguridad-del-bucket-d2) |
| Modelo fundacional (FM) | Modelo de IA de uso general que Amazon Bedrock ofrece para crear aplicaciones de IA generativa | D3 `[externo]` | [M0](../modulos/m00-introduccion-curso.md#tendencias-ia-generativa) |
| Par de claves (.pem) | Llave pública y privada para autenticarse en una instancia o descifrar la contraseña de Windows | D2 | [M6](../modulos/m06-computo.md) |
| Paradigma declarativo | Se declara el estado objetivo de la infraestructura, no los pasos para lograrlo | D3 | [clase 06](../clases/2026-11-05-clase-06-terraform.md#conceptos-clave) |
| Pilares del Well-Architected | Excelencia operativa, seguridad, fiabilidad, eficiencia del rendimiento, optimización de costos y sostenibilidad `[externo]` | D1 | [M9](../modulos/m09-arquitectura-nube.md#los-seis-pilares) |
| Presigned URL | URL temporal que da acceso a un objeto privado; cualquiera que la tenga entra mientras sea válida | D2 | [M7](../modulos/m07-almacenamiento.md#procedimientos-sitio-web-estático-en-s3-simulación-del-curso) |
| RDP | Escritorio remoto para instancias Windows (puerto 3389 `[externo]`) | — | [M6](../modulos/m06-computo.md#acceso-a-la-instancia) |
| Recurso (Terraform) | Bloque `resource "tipo" "nombre"` de un `.tf`; ej. `aws_instance`, `aws_security_group` | — | [clase 06](../clases/2026-11-05-clase-06-terraform.md#conceptos-clave) |
| Regla de ciclo de vida (lifecycle) | Acciones automáticas sobre objetos: transición (cambiar de clase) y expiración (borrar) | D3 | [M7](../modulos/m07-almacenamiento.md#funciones-adicionales) |
| Regla de entrada / salida | Tráfico permitido hacia / desde la instancia en un security group | D2 | [M5](../modulos/m05-redes.md#security-groups) |
| S3 Glacier Deep Archive | La clase más barata, para conservar datos por años (cumplimiento normativo); recuperación en horas | D3 | [M7](../modulos/m07-almacenamiento.md#clases-de-almacenamiento-de-s3-d3-d4) |
| S3 Glacier Flexible Retrieval | Archivo de bajo costo; recuperación en horas (o 1–5 minutos con expedited) | D3 | [M7](../modulos/m07-almacenamiento.md#clases-de-almacenamiento-de-s3-d3-d4) |
| S3 Intelligent-Tiering | Mueve los objetos de nivel solo, según el uso; para patrones de acceso impredecibles | D3 | [M7](../modulos/m07-almacenamiento.md#clases-de-almacenamiento-de-s3-d3-d4) |
| S3 One Zone-IA | Como Standard-IA, pero en una sola AZ; para datos reemplazables | D3 | [M7](../modulos/m07-almacenamiento.md#clases-de-almacenamiento-de-s3-d3-d4) |
| S3 Standard | Clase por defecto, para datos de acceso frecuente | D3 | [M7](../modulos/m07-almacenamiento.md#clases-de-almacenamiento-de-s3-d3-d4) |
| S3 Standard-IA | Acceso poco frecuente pero rápido; cobra por recuperación | D3 | [M7](../modulos/m07-almacenamiento.md#clases-de-almacenamiento-de-s3-d3-d4) |
| S3 Transfer Acceleration | Acelera las subidas y descargas usando la edge location de CloudFront más cercana | D3 | [M7](../modulos/m07-almacenamiento.md#mover-grandes-volúmenes-de-datos-a-s3) |
| Security group | Firewall virtual a nivel de instancia, con reglas de entrada y salida | D2 | [M5](../modulos/m05-redes.md#security-groups) |
| Session token | Tercer valor de las credenciales temporales (`aws_session_token`); el Learner Lab lo renueva en cada sesión | D2 | [clase 04](../clases/2026-10-27-clase-04-aws-cli.md#conceptos-clave) |
| Snapshot de EBS | Respaldo incremental y puntual de un volumen EBS, guardado en S3 | D3 | [M7](../modulos/m07-almacenamiento.md#amazon-ebs-elastic-block-store) |
| Soberanía de datos | Obligación de que ciertos datos no salgan del país | D3 | [M1](../modulos/m01-conceptos-nube.md#modelos-de-implementación) |
| SSH | Acceso remoto por terminal a instancias Linux (puerto 22) | — | [M6](../modulos/m06-computo.md#acceso-a-la-instancia) |
| Stateful | Un security group deja salir la respuesta a un tráfico permitido sin regla de salida `[externo]` | D2 | [M5](../modulos/m05-redes.md#security-groups) |
| Subred | Segmento de red creado dentro de una VPC | D3 | [M5](../modulos/m05-redes.md) |
| TAM | Technical Account Manager: ingeniero de AWS asignado al cliente; incluido desde el plan Enterprise `[externo]` | D4 | [M2](../modulos/m02-economia-facturacion.md#aws-support) |
| Terraform | Herramienta de código abierto de IaC: archivos `.tf`; flujo `init`, `plan`, `apply`, `destroy` | D3 `[externo]` | [clase 06](../clases/2026-11-05-clase-06-terraform.md#conceptos-clave) |
| terraform.tfstate | Archivo donde Terraform guarda los recursos que creó `[externo]`; no subirlo a repositorios públicos | — | [clase 06](../clases/2026-11-05-clase-06-terraform.md#conceptos-clave) |
| Tipo de instancia | `familia.tamaño` (ej. `t3.micro`): CPU y memoria de la instancia | D3 | [M6](../modulos/m06-computo.md) |
| Versionado de S3 | Cada subida con la misma clave crea una versión nueva con Version ID; desactivado por defecto; una vez activado solo se puede suspender | D3 | [M7](../modulos/m07-almacenamiento.md#2-configurar-el-bucket) |
| VPC por defecto | VPC que trae cada región; se obtiene con `--filters "Name=isDefault,Values=true"` | D3 | [clase 05](../clases/2026-10-29-clase-05-security-groups-cli.md#1-obtener-la-vpc-por-defecto) |
| Whitepaper | Documento técnico de AWS (visión general, arquitectura, precios, TCO) recomendado para Cloud Practitioner | — | [M0](../modulos/m00-introduccion-curso.md#documentación-de-aws) |
