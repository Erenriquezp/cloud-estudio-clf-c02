# Clase 04: AWS CLI, administrar EC2 desde la terminal

- **Fecha:** 2026-10-27 (fecha del taller 6; la página de la instancia marca `Wed Oct 28 00:48:15 UTC 2026` = 27/10 19:48 hora de Ecuador)
- **Syllabus:** U1 Cloud Computing: Virtual Machines · Semana 3. El syllabus marca balanceo de carga y Cloud CDN (Elastic Load Balancing y Amazon CloudFront `[externo]`). En clase se vio cómo administrar EC2 con la AWS CLI.
- **Cloud Foundations:** [M1 Formas de interactuar con AWS](../modulos/m01-conceptos-nube.md#formas-de-interactuar-con-aws) · [M6 EC2 con la AWS CLI](../modulos/m06-computo.md#amazon-ec2-con-la-aws-cli)

## Conceptos clave

| Concepto | Definición breve | CLF |
|---|---|---|
| Consola web (AWS Management Console) | Interfaz gráfica en el navegador. Es la que se usó en las clases 01 a 03 | D3 |
| AWS CLI | Interfaz de línea de comandos: administra los servicios de AWS con comandos en la terminal (`aws <servicio> <acción>`) | D3 |
| AWS SDK | Conjunto de librerías para acceder a AWS desde el código de un programa (Python, Java, JavaScript… `[externo]`) | D3 |
| Acceso programático `[externo]` | Nombre que da AWS al acceso por CLI, SDK o API, en contraste con la consola. Usa access keys, no usuario y contraseña | D2 · D3 |
| Access key | Par de credenciales para el acceso programático: **ID** (`aws_access_key_id`, en el lab empieza con `ASIA`) + **clave secreta** (`aws_secret_access_key`) | D2 |
| Session token | Tercer valor (`aws_session_token`) que acompaña a las credenciales **temporales**. El Learner Lab solo da credenciales temporales: duran lo que dura la sesión del lab (4 h) y hay que volver a configurarlas en cada sesión | D2 |
| Región por defecto | Región donde la CLI crea y consulta recursos si no se indica otra. En el lab: `us-east-1` (Norte de Virginia) | D3 |
| Formato de salida | Cómo muestra la CLI los resultados: `json` (por defecto), `table` y `text` `[externo]` | — |
| `--query` | Filtra y renombra los campos de la respuesta antes de mostrarla. Usa el lenguaje JMESPath `[externo]` | — |
| Etiqueta (tag) | Par `Key=Value` que se asigna a un recurso. El **nombre** que se ve en la consola es la etiqueta `Name` | D3 |

### Consola vs. CLI vs. SDK

| | Consola web | AWS CLI | AWS SDK |
|---|---|---|---|
| Cómo se usa | Clics en el navegador | Comandos en la terminal | Código dentro de una aplicación |
| Credenciales | Usuario y contraseña (en el lab, ya entras logueado) | Access key ID + clave secreta (+ token si son temporales) | Las mismas que la CLI `[externo]` |
| Sirve para | Explorar, aprender, tareas puntuales | Automatizar y repetir tareas con scripts | Que un programa use AWS |
| En clase | Clases 01–03 | Esta clase | Solo se mencionó |

## Procedimientos de lab

### 1. Comprobar que la AWS CLI está instalada

```powershell
aws --version
```

Resultado en el taller: `aws-cli/2.x … Windows/11` (AWS CLI versión 2). `[externo]` Si dice que el comando no existe, hay que instalarla desde la página oficial de AWS CLI v2 y abrir una terminal nueva.

### 2. Configurar las credenciales del Learner Lab

1. Learner Lab → **Start Lab** → esperar el círculo verde.
2. **AWS Details** → AWS CLI → **Show**. Aparecen `aws_access_key_id`, `aws_secret_access_key` y `aws_session_token`.
3. En la terminal:

   ```powershell
   aws configure
   ```

   | Pregunta | Qué poner |
   |---|---|
   | `AWS Access Key ID` | `aws_access_key_id` del lab |
   | `AWS Secret Access Key` | `aws_secret_access_key` del lab |
   | `AWS Session Token` | `aws_session_token` del lab (en la versión de la CLI del taller lo pidió `aws configure`) |
   | `Default region name` | `us-east-1` |
   | `Default output format` | `json` |

4. Comprobar:

   ```powershell
   aws configure list    # muestra perfil, claves (enmascaradas), región y de qué archivo sale cada una
   aws help              # ayuda general; también: aws ec2 help
   ```

   `aws configure list` del taller:

   ```
   NAME       VALUE                 TYPE                     LOCATION
   profile    <not set>             None                     None
   access_key ****************THKE  shared-credentials-file
   secret_key ****************jHDa  shared-credentials-file
   region     us-east-1             config-file              ~/.aws/config
   ```

   Las claves quedan en `~/.aws/credentials` y la región y el formato en `~/.aws/config` (`C:\Users\<usuario>\.aws\` en Windows). El panel del lab ofrece la alternativa: copiar el bloque `[default]` directamente en `~/.aws/credentials`.

5. **En cada sesión nueva del lab hay que repetir `aws configure`**: las credenciales anteriores ya no sirven (en el taller se configuró dos veces). `aws configure` muestra entre corchetes el valor actual; Enter lo deja igual.


### 3. Listar instancias

```powershell
aws ec2 describe-instances                    # JSON completo (muy largo)
aws ec2 describe-instances --output table     # el mismo contenido en tabla
```

Versión resumida, con solo las columnas útiles:

```powershell
aws ec2 describe-instances --query 'Reservations[].Instances[].{Nombre:Tags[?Key==`Name`]|[0].Value,ID:InstanceId,Tipo:InstanceType,Estado:State.Name,IP_Publica:PublicIpAddress,IP_Privada:PrivateIpAddress}' --output table
```

| Parte del `--query` | Qué hace |
|---|---|
| `Reservations[].Instances[]` | La respuesta agrupa las instancias en *reservations* (una por cada `run-instances`); esto las junta en una sola lista |
| `{Nombre: …, ID: …}` | Crea una columna por campo, con el nombre que elijas |
| ``Tags[?Key==`Name`]\|[0].Value`` | Busca la etiqueta `Name` y toma su valor: el nombre que se ve en la consola |
| `State.Name` | Estado: `pending`, `running`, `stopped`, `terminated` |

Resultado del taller: tabla `DescribeInstances` con 9 instancias. La CLI ordena las columnas alfabéticamente (Estado, ID, IP_Privada, IP_Publica, Nombre, Tipo), no en el orden del `--query`.

El `--query` va entre comillas **simples** y los backticks marcan el texto literal `Name`. Así funciona igual en PowerShell (donde se hizo el taller) y en Bash `[externo]`.

### 4. Lanzar instancias y ponerles nombre

```powershell
aws ec2 run-instances --image-id ami-04b938437b69a2c9c --instance-type t2.medium --key-name mi-primera-llaves-ee --count 2
```

| Parámetro | Equivale en la consola a |
|---|---|
| `--image-id` | AMI (Amazon Linux). El ID se copia de *Launch instance* o de *Imágenes → Catálogo de AMI*; cada región tiene IDs distintos `[externo]` |
| `--instance-type` | Tipo de instancia |
| `--key-name` | Par de claves (nombre, sin `.pem`) |
| `--count` | Número de instancias (en la consola, *Número de instancias*) |

Así, sin más parámetros, las instancias salen **sin nombre** y con el security group por defecto de la VPC `[externo]`. El nombre se pone después con una etiqueta:

```powershell
aws ec2 create-tags --resources i-0ade5b1d3972a8d12 --tags Key=Name,Value=MiServidorWeb
```

El ID (`i-…`) sale en la respuesta JSON de `run-instances` (`"InstanceId"`) o en `describe-instances`.

### 5. Taller 6: la tarea 2 (user data) con la AWS CLI

1. Guardar el [script de la tarea 2](2026-10-22-clase-03-user-data.md#script-de-user-data) como `user-data.sh` en la carpeta donde está abierta la terminal.
2. Lanzar con todo en un solo comando:

   ```powershell
   aws ec2 run-instances --image-id ami-04b938437b69a2c9c --instance-type t2.medium --key-name mi-primera-llaves-ee --count 1 --security-group-ids sg-03a113bf4fad231d7 --user-data file://user-data.sh --tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value=taller-aws-cli-ee}]"
   ```

   | Parámetro nuevo | Equivale en la consola a |
   |---|---|
   | `--security-group-ids` | *Seleccionar security group existente*. Debe tener la regla HTTP para ver la página (ID en *EC2 → Security Groups*) |
   | `--user-data file://user-data.sh` | *Detalles avanzados → Datos de usuario*. `file://` le dice a la CLI que lea el contenido del archivo |
   | `--tag-specifications` | *Name* al lanzar: pone la etiqueta en la creación, sin `create-tags` aparte |

3. La CLI responde con el JSON de la instancia (`ReservationId`, `InstanceId`, …).
4. Verificar con el `describe-instances` resumido o en la consola: `taller-aws-cli-ee`, `t2.medium`, *En ejecución*, 2/2 comprobaciones.
5. Abrir `http://<DNS público>`. Resultado del taller:

   ```
   Tarea 2: herramientas instaladas con User Data
   Apache: Server version: Apache/2.4.69 (Amazon Linux)
   Git: git version 2.50.1
   Docker: Docker version 25.0.14
   Node.js: v18.20.8
   Tree: tree v1.8.0
   Hostname: ip-172-31-20-118.ec2.internal
   ```

6. `[externo]` Al terminar, apagar lo creado y hacer *End Lab*: `aws ec2 terminate-instances --instance-ids <id1> <id2>`.

#### Si algo falla `[externo]`

| Síntoma | Causa probable | Solución |
|---|---|---|
| `Unable to locate credentials` o `ExpiredToken` | No se configuró o la sesión del lab cambió | Copiar las credenciales nuevas y repetir `aws configure` |
| `InvalidAMIID.NotFound` | Región distinta a la del ID de la AMI | Revisar que la región sea `us-east-1` (`aws configure list`) |
| Error al leer `file://user-data.sh` | El archivo no está en la carpeta actual | Ir a esa carpeta o poner la ruta completa |
| La instancia arranca pero la página nunca aparece | El script se guardó con saltos de línea de Windows (CRLF) y Bash no lo lee | Guardarlo con fin de línea LF (en VS Code, abajo a la derecha: *CRLF → LF*) |
| La página no abre y el script terminó | El security group no tiene HTTP | Usar el ID del security group que sí la tiene |

## Posibles preguntas de lección o examen

1. Nombra las 3 formas de interactuar con AWS y para qué sirve cada una.
2. ¿Qué datos pide `aws configure`? ¿De dónde salen en el Learner Lab?
3. ¿Por qué hay que volver a configurar la CLI en cada sesión del lab?
4. ¿Qué muestra `aws configure list`? ¿En qué archivos guarda la configuración?
5. ¿Qué diferencia hay entre `--output json` y `--output table`?
6. ¿Qué hace `--query 'Reservations[].Instances[]…'`? ¿Cómo se obtiene el nombre de la instancia?
7. Escribe el comando para lanzar 2 instancias `t2.medium` con tu par de claves.
8. ¿Cómo le pones nombre a una instancia desde la CLI? Da las dos formas.
9. ¿Qué parámetro de `run-instances` equivale a *Datos de usuario* en la consola? ¿Qué significa `file://`?

### Tipo CLF-C02

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
