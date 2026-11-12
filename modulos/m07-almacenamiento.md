# M7: Almacenamiento (Storage)

- **Syllabus:** U4 Object Storage · **CLF-C02:** D3 (servicios de almacenamiento), D2 (seguridad de S3)
- **Fuentes:** curso AWS Educate *Getting Started with Storage* (2026-11-03) · [clase 07](../clases/2026-11-10-clase-07-ebs-efs.md) (EBS y EFS en EC2)

## Persistente vs. no persistente `[D3]`

| | Persistente | No persistente |
|---|---|---|
| Datos | **Perduran en el tiempo** | **Volátiles**: existen mientras corre un proceso (ej. caché) |
| En AWS | Amazon EBS, Amazon EFS, Amazon S3 | Instance store `[externo]` |

## Nube vs. on-premises

Beneficios del almacenamiento en la nube: rentable, seguro, accesible, escalable, administrado y con respaldo.

## Tipos de almacenamiento `[D3]`

| Tipo | Cómo guarda los datos | Al modificar | Servicio AWS | Casos de uso |
|---|---|---|---|---|
| **Bloque** | Bloques de tamaño fijo, cada uno con un ID único, guardados donde sea más eficiente. Las aplicaciones y el sistema de archivos los combinan | Cambia solo el bloque afectado | **Amazon EBS** | Bases de datos (SQL Server, MySQL, PostgreSQL, Oracle, SAP HANA; NoSQL como MongoDB y Cassandra), big data (Hadoop, Spark: se pueden separar y volver a conectar volúmenes), aplicaciones empresariales críticas (baja latencia, IOPS altos) |
| **Archivo** | Jerarquía de carpetas en un sistema de archivos compartido, como una unidad de red de una empresa | — | **Amazon EFS** | Varias instancias EC2 (incluso en distintas AZ) u on-premises comparten los mismos archivos |
| **Objeto** | Objetos = **datos + metadatos + clave** (la clave es el ID único) | Se reemplaza **el objeto entero** | **Amazon S3** | Recuperación ante desastres (sin segundo sitio físico), data lakes, aplicaciones cloud-native/microservicios, archivo de proyectos terminados |

**Cómo elegir:** (1) definir requisitos: carga de trabajo, restricciones de seguridad y rendimiento esperado → (2) revisar las opciones: bloque, archivo u objeto → (3) diseñar la estrategia: elegir los servicios y las opciones de diseño.

## Servicios principales

### Amazon EBS (Elastic Block Store)
- Volúmenes de bloque **para EC2**: se crean y se conectan a una instancia.
- Diseñado para **99,999 % de disponibilidad**.
- **Bloque en AWS hay de dos tipos:** el **instance store**, que es el disco local de la instancia, y **EBS**, que es almacenamiento de red y se respalda con snapshots. `[externo]` El instance store es **efímero**: sus datos se pierden si la instancia se detiene o se termina.

| Característica | Detalle |
|---|---|
| Disponibilidad | Al crearse, el volumen se **replica dentro de su AZ**. Solo se conecta a instancias de **esa misma AZ** y la instancia lo ve como un disco local |
| Persistencia | Vive fuera de la instancia y **sobrevive a ella**. Pagas mientras existan los datos. Se puede desconectar con los datos intactos y conectar a otra instancia. En una instancia respaldada por EBS, detenerla y volver a iniciarla no afecta los datos |
| Seguridad | Se entrega como dispositivo de bloque **en crudo, sin formato**. AWS verifica que esté lógicamente vacío antes de cada uso o reutilización |
| Cifrado | Disponible `[externo]` (con AWS KMS) |
| **Snapshots** | Respaldos **puntuales** que se guardan **en S3**. Son **incrementales**: solo guardan los bloques que cambiaron desde el último snapshot. Cada uno trae todo lo necesario para restaurar un volumen nuevo |
| Flexibilidad | Se cambia el tipo, el tamaño y los IOPS **en producción, sin interrumpir el servicio**. Se cobra la configuración nueva desde que se aplica |

| Familia | Tipo | Perfil | Casos de uso |
|---|---|---|---|
| SSD | **General Purpose** | Equilibrio entre precio y rendimiento | Cargas transaccionales, escritorios virtuales, BD medianas, apps interactivas de baja latencia, **volúmenes de arranque**, desarrollo y pruebas |
| SSD | **Provisioned IOPS** | Máximo rendimiento, misión crítica, baja latencia | IOPS sostenidos, **BD con mucha E/S** |
| HDD | **Throughput Optimized** | HDD barato para acceso frecuente con mucho throughput | **Big data, data warehouses, procesamiento de logs** |
| HDD | **Cold** | El HDD más barato | Datos de acceso infrecuente; cuando lo que importa es el costo mínimo |

`[externo]` Los HDD (st1 y sc1) **no sirven como volumen de arranque**.

En la [clase 07](../clases/2026-11-10-clase-07-ebs-efs.md#conceptos-clave): **`gp3`** = General Purpose (SSD de tercera generación) · **`io1` / `io2`** = Provisioned IOPS, para bases de datos. Para usar un volumen nuevo en Linux hay que **formatearlo y montarlo** ([taller 9](../clases/2026-11-10-clase-07-ebs-efs.md#1-taller-9-agregar-un-disco-ebs-a-una-instancia-linux)): `mkfs` → `mount`.

### Amazon EFS (Elastic File System)
- Sistema de archivos **elástico y compartido**: no hay que aprovisionar capacidad.
- Se monta desde EC2 (varias instancias en **distintas AZ**), contenedores, AWS Lambda y servidores on-premises.

| Característica | Detalle |
|---|---|
| **Totalmente administrado** | AWS gestiona los servidores de archivos, el hardware, el software y los respaldos. Tú solo respondes por tus datos. Se crea en segundos |
| Disponibilidad y durabilidad | **11 nueves de durabilidad** y hasta **99,99 % de disponibilidad**. Por defecto guarda cada objeto (directorio, archivo, enlace) en **varias AZ** |
| Elástico | La capacidad crece y se reduce sola. No se aprovisiona y **se paga por lo que se usa**. El throughput y los IOPS escalan con el tamaño |
| Cifrado | **En reposo** con claves de **AWS KMS**; **en tránsito** con **TLS** |

- Casos de uso: gestión de contenido y servidores web, procesamiento de medios, directorios compartidos y *home*, respaldos de BD, herramientas de desarrollo, analítica de big data, aplicaciones serverless.
- Se accede por **NFS** (acceder a archivos remotos como si fueran locales), puerto **TCP 2049**. Debe estar en la **misma VPC** que las instancias. Cada AZ tiene un **destino de montaje** `[externo]` (*mount target*) por el que las instancias de esa AZ se conectan. Taller → [clase 07](../clases/2026-11-10-clase-07-ebs-efs.md#2-taller-10-amazon-efs-compartido-entre-dos-instancias).
- `[externo]` Solo para Linux (NFS). El equivalente para Windows es Amazon FSx.

### EBS vs. EFS vs. S3 (resumen para el examen)

| | EBS | EFS | S3 |
|---|---|---|---|
| Tipo | Bloque | Archivo | Objeto |
| Alcance | **1 AZ**, una instancia `[externo]` | **Varias AZ**, muchas instancias a la vez | Región (≥ 3 AZ `[externo]`), acceso por URL/API |
| Capacidad | Se aprovisiona (tamaño fijo, modificable) | Elástica | Ilimitada |
| Caso típico | Disco de arranque y BD de una EC2 | Carpeta compartida entre servidores | Archivos, respaldos, sitios estáticos, data lakes |

### Amazon S3 (Simple Storage Service)
- Almacenamiento de **objetos** dentro de **buckets**. Objeto = archivo + metadatos; bucket = contenedor de objetos.
- Usos: data lakes, sitios web, apps móviles, respaldo y restauración, archivo, aplicaciones empresariales, IoT, big data.
- Flujo básico: **mover** datos a S3 → **guardarlos** como objetos → **usarlos** en aplicaciones.
- Elementos: **objetos** (datos, metadatos, clave), **buckets** y **regiones** (cada bucket se crea en una región).
- `[externo]` Durabilidad de **99,999999999 %** (11 nueves). El nombre del bucket es **único a nivel mundial**.

#### Seguridad y cumplimiento de S3 `[D2]`
- Tres formas de cifrado. `[externo]` Del lado del servidor: SSE-S3 (claves de S3, por defecto), SSE-KMS (claves en AWS KMS) y SSE-C (claves del cliente). Aparte existe el cifrado del lado del cliente.
- Auditoría con **AWS CloudTrail** (registra quién hizo qué llamada a la API).
- **Amazon Macie**: detecta y protege datos sensibles en S3 (por ejemplo, datos personales).
- Cumple estándares de seguridad y certificaciones de cumplimiento.

## Clases de almacenamiento de S3 `[D3]` `[D4]`

| Clase | Acceso | Casos de uso (del curso) |
|---|---|---|
| **S3 Standard** | Datos activos, acceso frecuente | Sitios web estáticos, logs que se revisan o analizan seguido, instaladores, archivos de configuración y herramientas de despliegue |
| **S3 Standard-IA** | Poco frecuente, pero rápido cuando se necesita | Respaldos de sistemas, archivos que casi no se usan pero deben estar disponibles al instante |
| **S3 One Zone-IA** | Poco frecuente, **una sola AZ** | Copias de replicación entre regiones, copias externas de respaldos on-premises, archivos fáciles de reemplazar |
| **S3 Glacier Flexible Retrieval** | Archivo | Respaldos de largo plazo o por cumplimiento normativo, reemplazo de cintas magnéticas, archivo de medios pesados |
| **S3 Glacier Deep Archive** | Archivo, el más barato | Datos que se deben conservar por cumplimiento, bibliotecas de datos de largo plazo |
| **S3 Intelligent-Tiering** | Mueve los objetos de clase sola según el uso | Cargas impredecibles, desconocidas o que cambian rápido |

`[externo]` Para el examen:
- Mientras más frío el almacenamiento, **más barato guardar y más caro y lento recuperar**. Deep Archive tarda horas (hasta unas 12); Flexible Retrieval, de minutos a horas.
- Las clases IA cobran por recuperación y tienen una duración mínima de almacenamiento.
- Existe también **S3 Glacier Instant Retrieval** (archivo con recuperación en milisegundos), que el curso no menciona.
- One Zone-IA es más barata que Standard-IA, pero **se pierde si falla esa AZ**. Las demás clases guardan en 3 AZ o más.

### Casos de práctica del curso

| Caso | Pistas | Respuesta del curso |
|---|---|---|
| 1. Miniaturas de video: accesibles 1 año, casi no se usan después de 60 días, pero deben abrirse rápido | Infrecuente + acceso rápido | **Standard-IA**. El curso sugiere además una **política de ciclo de vida** para pasarlas a archivo después de 60 días |
| 2. Continuidad del negocio: respaldos de largo plazo, acceso raro, recuperarlos en pocas horas ante un desastre | Largo plazo + horas | **Glacier Flexible Retrieval**: recuperación estándar en horas o *expedited* en 1–5 minutos |
| 3. App de exámenes: uso moderado en el semestre y muy alto en época de exámenes, patrón impredecible, controlar costos | Impredecible | **Intelligent-Tiering** |

**Regla:** "infrecuente + acceso rápido" → **Standard-IA** por defecto. Solo se elige **One Zone-IA** si el enunciado dice que el dato es reemplazable o que perderlo es aceptable. En el caso 1 elegí One Zone-IA porque la miniatura se puede regenerar, y el curso la marcó como incorrecta.

## Costos de S3 `[D4]`

| Se cobra | No se cobra |
|---|---|
| GB almacenados por mes (según la clase) | Transferencia **de entrada** (IN) a S3 |
| Transferencia **de salida** (OUT) a otras regiones o a internet | Transferencia entre buckets de la **misma región** |
| Solicitudes PUT, COPY, POST, LIST y GET | Salida hacia EC2 o CloudFront en la **misma región** |
| | Solicitudes DELETE y CANCEL |

- **Capa gratuita según el curso:** 5 GB durante los primeros 12 meses; cada mes incluye 20 000 GET, 2 000 PUT/COPY/POST/LIST y 100 GB de transferencia de salida.
- `[externo]` ⚠️ Las cuentas abiertas desde 2025-07-15 tienen el nuevo plan gratuito con créditos (hasta USD 200, 6 meses), no los 12 meses del curso. Para el examen, puede aparecer cualquiera de los dos esquemas.
- Para estimar costos: **AWS Pricing Calculator**.

## Usar Amazon S3

Ciclo: **crear bucket → configurarlo → subir objetos → trabajar con objetos → borrar objetos y buckets**.

### 1. Crear el bucket
- El nombre debe ser **único a nivel mundial**: de 3 a 63 caracteres, solo minúsculas, números, puntos y guiones.
- Formato de URL: `https://<bucket>.s3.amazonaws.com/<clave>`. Ejemplo: en `photobucket.s3.amazonaws.com/2021-04-13/myphotos.zip`, el bucket es `photobucket` y la clave es `2021-04-13/myphotos.zip`. `[externo]` Las "carpetas" son solo prefijos de la clave: S3 es plano.

### 2. Configurar el bucket

| Opción | Qué es | Por defecto |
|---|---|---|
| Región | Dónde viven los datos | Se elige al crear |
| Object ownership | Control por **ACL** (listas de quién puede acceder a qué) | **ACL desactivadas** |
| Block public access | Bloquea el acceso público al bucket, a los puntos de acceso y a los objetos. Se puede abrir con políticas o permisos | **Sin acceso público** |
| Versionado | Cada PUT con la misma clave crea una **versión nueva** con su Version ID. Protege de sobrescrituras y borrados por error | **Desactivado** |
| Etiquetas | Pares clave/valor (ej. `project`/`Alpha`, `cost-center`/`Alpha`). La clave es obligatoria y única; el valor es opcional y se puede repetir | — |

`[externo]` Una vez activado, el versionado ya no se puede desactivar, solo **suspender**.

### 3. Subir objetos
- Capacidad total ilimitada. Cada objeto puede pesar hasta **5 TB**; desde la consola se suben hasta **160 GB**.
- Clase de almacenamiento por defecto: **S3 Standard**.
- **Carga multiparte:** se recomienda para objetos de más de **100 MB**. Divide el archivo en partes que se suben en paralelo. `[externo]` Es obligatoria por encima de 5 GB.

### 4. Trabajar con objetos
- **Copiar:** sirve para crear una copia, mover un objeto, renombrarlo o cambiar sus metadatos.
- **Descargar** o copiar la URL del objeto.

### 5. Borrar objetos y buckets
- **MFA Delete:** para borrar se pide usuario y contraseña **más** un código de MFA. `[externo]` Requiere que el versionado esté activado. `[externo]` Un bucket tiene que estar vacío para poder borrarlo.

### Funciones adicionales
- **Reglas de ciclo de vida:**
  - *Transition action*: mueve objetos de clase según su antigüedad. Ejemplo del curso: Standard → Standard-IA a los 30 días → Glacier Flexible Retrieval a los 60.
  - *Expiration action*: borra los objetos. En el ejemplo, a los 365 días.
- **Replicación:** de un bucket a uno o varios, en la misma cuenta o en otra, dentro de la misma región o hacia otra.

| | Cross-Region Replication (CRR) | Same-Region Replication (SRR) |
|---|---|---|
| Copia entre | Buckets de **regiones distintas** | Buckets de la **misma región** |
| Para qué | Cumplimiento que exige más distancia para recuperación ante desastres · menor latencia con copias cerca de los usuarios · clústeres de cómputo en dos regiones | Juntar logs en un solo bucket · replicar en vivo entre cuentas de producción y de prueba (conserva los metadatos) · **soberanía de datos**: varias copias en cuentas distintas sin salir de la región |

### Seguridad del bucket `[D2]`

**Modelo de responsabilidad compartida:**

| AWS: seguridad **de** la nube | Cliente: seguridad **en** la nube |
|---|---|
| Hardware e infraestructura global (regiones, AZ, edge locations), cómputo, almacenamiento, bases de datos y redes | Datos del cliente; plataforma, aplicaciones e IAM; SO, red y firewall; cifrado del lado del cliente; cifrado del lado del servidor; protección del tráfico de red |

- Los recursos de S3 son **privados por defecto**.
- **Políticas de IAM:** se asignan a usuarios o aplicaciones y dicen qué pueden hacer en S3. En el ejemplo del curso, la aplicación A tiene acceso y la B no.
- **Bucket policy:** documento JSON que se asigna al bucket. Ejemplo del curso: `"Effect": "Deny"`, `"Principal": "*"`, `"Action": "s3:*"` sobre `arn:aws:s3:::examplebucket/*` con la condición `NotIpAddress aws:SourceIp 54.240.143.0/24`. Es decir, **niega todo a quien no venga de ese rango de IP**.
- **Cifrado:** convierte los datos para que solo los lea quien tiene la clave.
  - *Del lado del cliente*: cifras antes de subir, así que protege los datos **en tránsito y en reposo**.
  - *Del lado del servidor*: S3 cifra al guardar, así que protege los datos **en reposo**.

## Mover grandes volúmenes de datos a S3

| Servicio | Qué es | Cuándo |
|---|---|---|
| **S3 Transfer Acceleration** | Las subidas y descargas viajan por la **edge location de CloudFront más cercana** y luego por la red de AWS | Clientes en todo el mundo que suben a un bucket central; GB o TB entre continentes con regularidad |
| **AWS Snowcone** | Dispositivo portátil y resistente, para edge computing y transferencia: 2 vCPU, 4 GB de RAM, 8 TB HDD o 14 TB SSD. Los datos viajan offline (enviando el equipo) u online (con **AWS DataSync**) | Migraciones únicas de hasta 8 o 14 TB |
| **AWS Snowball** | Dispositivos físicos que mueven datos entre S3 y tu sitio más rápido que internet. **Import job**: hasta 72 TB en un Snowball → S3. **Export job**: cualquier cantidad desde S3, en varios Snowball | Migrar petabytes |
| **AWS Snowmobile** | Contenedor de 45 pies en un camión; hasta **100 PB** | Escala de exabytes: bibliotecas de video, repositorios de imágenes, migrar un centro de datos entero |

`[externo]` ⚠️ AWS dejó de ofrecer Snowmobile y Snowcone (2024). En el examen todavía pueden salir como respuesta según la escala: TB → Snowcone/Snowball, PB → Snowball, EB → Snowmobile.

## Procedimientos: sitio web estático en S3 (simulación del curso)

Un **sitio estático** tiene HTML, imágenes, CSS y JS que corre en el navegador. No tiene scripts del lado del servidor ni base de datos. S3 lo sirve **sin servidores**, con alta disponibilidad.

| Tarea | Pasos clave en la consola | Qué enseña |
|---|---|---|
| 1. Crear el bucket | S3 → **Create bucket** → nombre `sim-website` → Object Ownership: **ACLs enabled** (*Bucket owner preferred*) → desmarcar **Block all public access** y confirmar → Bucket Versioning: **Enable** → tag `Department`=`Marketing` | El nombre es global. Sin desbloquear el acceso público, el sitio no se puede abrir |
| 2. Activar el hosting | Bucket → **Properties** → *Static website hosting* → Enable → *Host a static website* → index `index.html`, error `error.html` → abrir el **Bucket website endpoint** | Responde **403 Forbidden** porque los objetos siguen privados |
| 3. Subir contenido | **Objects** → Upload → `index.html`, `script.js`, `style.css` | Subidos, pero todavía con 403 |
| 4. Hacer públicos los objetos | Seleccionar los 3 → Actions → **Make public using ACL** → refrescar el endpoint | Ya se ve el sitio |
| 5. Compartir con **presigned URL** | Subir `new-report.png` (privado) → Actions → **Share with a presigned URL** → 2 minutos → copiar y abrir | Funciona hasta que vence; después da **Access denied** |
| 6. Proteger con **bucket policy** | **Permissions** → Bucket policy → Edit → política `Deny` + `s3:DeleteObject` sobre los 3 archivos → intentar borrar `index.html` | Aparece en *Failed to delete*: la política funciona |
| 7. Actualizar el sitio | Editar `index.html` en local → volver a subirlo → **Make public using ACL** de nuevo | En S3 no se edita un objeto: **se reemplaza entero**. La versión nueva vuelve a ser privada hasta que la publicas |
| 8. Ver versiones | **Show versions** | Cada objeto tiene su Version ID y `index.html` tiene 2 versiones |

Política usada en la tarea 6:
```json
{
  "Version": "2012-10-17",
  "Id": "MyBucketPolicy",
  "Statement": [{
    "Sid": "BucketPutDelete",
    "Effect": "Deny",
    "Principal": "*",
    "Action": "s3:DeleteObject",
    "Resource": [
      "arn:aws:s3:::sim-website/index.html",
      "arn:aws:s3:::sim-website/script.js",
      "arn:aws:s3:::sim-website/style.css"
    ]
  }]
}
```

**Dos formas de hacer público el contenido:**
- **Bucket policy**: para todo el bucket o un directorio. Úsala solo si nada en el bucket es sensible.
- **ACL**: por objeto. El curso dice que es más segura porque evita publicar otros objetos por accidente.

**Presigned URL:** da acceso temporal a un objeto privado. **Cualquiera que tenga la URL** entra mientras sea válida, así que conviene la duración mínima y compartirla solo con gente de confianza.

**Versionado:** con el versionado apagado, una subida nueva **reemplaza** el archivo y un borrado es **definitivo**. Con el versionado activo se guardan las versiones anteriores y se pueden recuperar.

`[externo]` Más allá del curso:
- Un `Deny` explícito **siempre gana** sobre cualquier `Allow`. Por eso ni el dueño del bucket pudo borrar los archivos.
- AWS hoy recomienda **dejar las ACL desactivadas** y publicar con bucket policy. En producción se usa **CloudFront con Origin Access Control (OAC)** y el bucket **privado**, lo que además da HTTPS: el endpoint de sitio web de S3 solo sirve HTTP.
- Ese es el patrón que hay que usar para el frontend de la tesis, no el de la simulación.