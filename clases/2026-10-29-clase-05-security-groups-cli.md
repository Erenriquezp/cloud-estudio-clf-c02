# Clase 05: Security groups con la AWS CLI (y Terraform instalado)

- **Fecha:** 2026-10-29 (fecha del taller 7)
- **Syllabus:** U1 Cloud Computing: Virtual Machines · Semana 3 (la misma de la clase 04). Se continúa con la AWS CLI; el syllabus de esa semana marca balanceo de carga y Cloud CDN (Elastic Load Balancing y Amazon CloudFront `[externo]`).
- **Cloud Foundations:** [M5 Security groups con la AWS CLI](../modulos/m05-redes.md#security-groups-con-la-aws-cli) · [M6 EC2 con la AWS CLI](../modulos/m06-computo.md#amazon-ec2-con-la-aws-cli) · [M1 Infraestructura como código](../modulos/m01-conceptos-nube.md#infraestructura-como-código-iac)
- **Original:** [_archivo/clase-05/notas-crudas.txt](../_archivo/clase-05/notas-crudas.txt)
- **Taller 7 entregado (2026-10-29):** [_archivo/clase-05/taller-07-security-groups-cli.pdf](../_archivo/clase-05/taller-07-security-groups-cli.pdf). Crear un security group, sus reglas y una instancia que lo use, todo con la CLI.

## Conceptos clave

| Concepto | Definición breve | CLF |
|---|---|---|
| Security group (SG) | Firewall virtual de la instancia, con reglas de entrada (*ingress*) y de salida (*egress*). Por defecto: entrada denegada, salida libre | D2 · D3 |
| VPC por defecto | VPC que trae cada región de la cuenta; se identifica con el filtro `isDefault=true`. Si no se indica `--vpc-id`, el SG se crea en ella `[externo]` | D3 |
| CIDR | Notación `IP/prefijo` que define un rango de direcciones. `0.0.0.0/0` = cualquier IPv4; `/32` = una sola IP (ej. `192.168.1.100/32`); `/24` = 256 direcciones (`192.168.1.0/24`) | D3 |
| Regla de entrada (ingress) | Tráfico permitido **hacia** la instancia: protocolo + puerto + origen (CIDR) | D2 |
| Regla de salida (egress) | Tráfico permitido **desde** la instancia: protocolo + puerto + destino (CIDR) | D2 |
| Stateful `[externo]` | La respuesta a un tráfico permitido sale sin necesitar regla de salida (y al revés) | D2 |
| Variable de PowerShell | `$NOMBRE = (comando)` guarda en una variable lo que devuelve el comando; luego se reutiliza (`$SG_APACHE_ID`). Sirve para encadenar comandos sin copiar IDs a mano | — |
| `--query` + `--output text` | `--query 'GroupId'` extrae un solo campo de la respuesta JSON; `--output text` lo devuelve como texto plano, sin comillas, listo para guardar en una variable | — |
| Terraform | Herramienta de **infraestructura como código**: se describe la infraestructura en archivos y la herramienta la crea. Se instaló aquí y se usó en la [clase 06](2026-11-05-clase-06-terraform.md) | D3 `[externo]` |

### Comandos de security groups

| Comando | Para qué |
|---|---|
| `aws ec2 describe-vpcs --filters "Name=isDefault,Values=true" --query "Vpcs[0].VpcId" --output text` | ID de la VPC por defecto |
| `aws ec2 create-security-group --group-name … --description … --vpc-id … --query 'GroupId' --output text` | Crear el SG; devuelve su ID (`sg-…`) |
| `aws ec2 authorize-security-group-ingress --group-id … --protocol tcp --port … --cidr …` | Agregar una regla de **entrada** |
| `aws ec2 authorize-security-group-egress --group-id … --protocol tcp --port … --cidr …` | Agregar una regla de **salida** |
| `aws ec2 run-instances … --security-group-ids …` | Lanzar una instancia con ese SG |

| Parámetro | Qué indica |
|---|---|
| `--group-name` | Nombre del SG (usado en clase: `taller-sg-aws-cli-ee`) |
| `--description` | Descripción obligatoria del SG |
| `--vpc-id` | VPC donde se crea |
| `--group-id` | SG al que se agrega la regla |
| `--protocol` / `--port` | Protocolo (`tcp`) y puerto (80, 22, 443) |
| `--cidr` | Origen (entrada) o destino (salida) permitido |

### Reglas del SG del taller

| Tipo | Protocolo | Puerto | CIDR | Para qué |
|---|---|---|---|---|
| Entrada | TCP | 80 (HTTP) | `0.0.0.0/0` | Ver la página web desde cualquier lugar |
| Entrada | TCP | 22 (SSH) | `0.0.0.0/0` | Entrar por SSH desde cualquier lugar |
| Salida | TCP | 443 (HTTPS) | `192.168.1.0/24` | Ejemplo de regla de salida hacia un CIDR concreto (en las notas, un rango de ejemplo) |

## Procedimientos de lab

Todo se hizo en PowerShell, con las credenciales ya configuradas ([clase 04, paso 2](2026-10-27-clase-04-aws-cli.md#2-configurar-las-credenciales-del-learner-lab)). El backtick `` ` `` al final de la línea continúa el comando en la siguiente.

### 1. Obtener la VPC por defecto

```powershell
$VPC_ID = (aws ec2 describe-vpcs `
    --filters "Name=isDefault,Values=true" `
    --query "Vpcs[0].VpcId" `
    --output text)
```

### 2. Crear el security group

```powershell
$SG_APACHE_ID = (aws ec2 create-security-group `
    --group-name "taller-sg-aws-cli-ee" `
    --description "Security group creado desde AWS CLI" `
    --vpc-id $VPC_ID `
    --query 'GroupId' `
    --output text)
```

`$SG_APACHE_ID` queda con el ID del SG (`sg-…`) y se usa en todos los comandos siguientes.

### 3. Agregar las reglas

```powershell
# Entrada: HTTP (puerto 80)
aws ec2 authorize-security-group-ingress `
    --group-id $SG_APACHE_ID `
    --protocol tcp `
    --port 80 `
    --cidr 0.0.0.0/0

# Entrada: SSH (puerto 22)
aws ec2 authorize-security-group-ingress `
    --group-id $SG_APACHE_ID `
    --protocol tcp `
    --port 22 `
    --cidr 0.0.0.0/0

# Salida: HTTPS (puerto 443) hacia un CIDR concreto (ej: 192.168.1.100/32 o la IP de un servicio externo)
aws ec2 authorize-security-group-egress `
    --group-id $SG_APACHE_ID `
    --protocol tcp `
    --port 443 `
    --cidr 192.168.1.0/24
```

### 4. Lanzar una instancia con ese security group

Requiere `user-data.sh` en la carpeta actual (el script de [user data](2026-10-22-clase-03-user-data.md#script-de-user-data), que instala Apache).

```powershell
$INSTANCE_ID = (aws ec2 run-instances `
    --image-id ami-073f45061d35c86f0 `
    --instance-type t3.micro `
    --key-name mi-primera-llaves-ee `
    --security-group-ids $SG_APACHE_ID `
    --user-data file://user-data.sh `
    --count 1 `
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=taller-sg-cli-ee}]' `
    --query 'Instances[0].InstanceId' `
    --output text)

Write-Host "Instancia lanzada exitosamente con ID: $INSTANCE_ID"
Write-Host "Security Group ID: $SG_APACHE_ID"
```

| Novedad frente a la [clase 04](2026-10-27-clase-04-aws-cli.md#5-taller-6-la-tarea-2-user-data-con-la-aws-cli) | Efecto |
|---|---|
| `$SG_APACHE_ID` en `--security-group-ids` | La instancia usa el SG creado por la CLI, no uno copiado a mano |
| `--query 'Instances[0].InstanceId' --output text` | `run-instances` devuelve solo el ID de la instancia, que se guarda en `$INSTANCE_ID` |
| `Write-Host` | Imprime mensajes en la terminal (instancia y SG creados) |
| `t3.micro` y otra AMI | Tipo y AMI distintos a los de la clase 04 (`t2.medium`, `ami-04b9…`) |

`[externo]` Para comprobar el SG y sus reglas: `aws ec2 describe-security-groups --group-ids $SG_APACHE_ID`.

### 5. Instalar Terraform en Windows

```powershell
# 1. Crear una carpeta para herramientas si no existe
if (!(Test-Path "C:\Tools")) { New-Item -ItemType Directory -Path "C:\Tools" }

# 2. Mover terraform.exe (ya descomprimido) a C:\Tools. Ajustar la ruta de Descargas si la carpeta se llama distinto
Move-Item -Path "C:\Users\<usuario>\Downloads\terraform_1.17.1_windows_amd64\terraform.exe" -Destination "C:\Tools\terraform.exe" -Force

# 3. Agregar C:\Tools al PATH del usuario (sin requerir admin)
$userPath = [Environment]::GetEnvironmentVariable("Path", "User")
if ($userPath -notlike "*C:\Tools*") {
    [Environment]::SetEnvironmentVariable("Path", "$userPath;C:\Tools", "User")
}

# 4. Refrescar el PATH en la sesión actual de PowerShell
$env:PATH += ";C:\Tools"
terraform -version
```

| Paso | Qué hace |
|---|---|
| 1 | Crea `C:\Tools` para guardar herramientas de terminal |
| 2 | Mueve `terraform.exe` ahí. Antes hay que haber descargado y descomprimido el ZIP de Terraform `[externo]` |
| 3 | Agrega la carpeta al **PATH del usuario** (variable que lista dónde busca programas la terminal); no pide permisos de administrador |
| 4 | Aplica el PATH a la sesión abierta, sin cerrar la terminal; `terraform -version` confirma la instalación |

`[externo]` Un cambio en el PATH del usuario solo lo ven las terminales nuevas; por eso el paso 4.

## Correcciones y límites `[externo]`

| Tema | Aclaración |
|---|---|
| Regla de salida "solo HTTPS" | `authorize-security-group-egress` **agrega** una regla; no quita la que viene por defecto (*All traffic* hacia `0.0.0.0/0`). Para dejar **solo** HTTPS hay que eliminar esa con `aws ec2 revoke-security-group-egress --group-id $SG_APACHE_ID --protocol all --cidr 0.0.0.0/0` |
| Rango `192.168.1.0/24` | Es un rango **privado**: no es una dirección de internet. Es solo un ejemplo; un destino real sería la IP o CIDR del servicio al que se quiere llegar |
| Restringir la salida antes de que corra el user data | El script usa `dnf` para instalar paquetes (sale a internet). Con la salida restringida podría fallar |
| SSH desde `0.0.0.0/0` | Permite el intento de conexión desde cualquier IP del mundo. Es práctico en el lab, pero en producción se limita a la IP propia (`<tu-IP>/32`) |
| Revocar reglas | Las reglas de entrada se quitan con `revoke-security-group-ingress` (mismos parámetros que `authorize-…`) |
| Borrar el SG | Solo se puede eliminar (`delete-security-group`) cuando ninguna instancia lo usa |

## Posibles preguntas de lección o examen

1. ¿Para qué sirve `--query 'GroupId' --output text` al crear un SG?
2. Escribe el comando que permite HTTP desde cualquier IP en un SG.
3. ¿Qué significa `0.0.0.0/0`? ¿Y `192.168.1.100/32`?
4. ¿Qué diferencia hay entre `authorize-security-group-ingress` y `authorize-security-group-egress`?
5. ¿Cómo se asocia un SG a una instancia al lanzarla con la CLI?
6. ¿Para qué se guarda el ID del SG en una variable?
7. ¿Qué hace el paso 3 de la instalación de Terraform? ¿Por qué no se necesita administrador?

### Tipo CLF-C02

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

## ⚠️ Dudas

- **`user-data.sh` del taller 7:** el PDF solo lo referencia. Se asume que es el script de la tarea 2 (Apache + herramientas), como en el taller 6.
- **Lanzamiento con `t3.micro` y `ami-073f45061d35c86f0`:** las notas no dicen por qué cambió la AMI respecto a la clase 04 (`ami-04b938437b69a2c9c`). Verificar que sea Amazon Linux.
