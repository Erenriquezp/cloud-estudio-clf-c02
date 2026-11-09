# M6: Cómputo (Compute)

- **Syllabus:** U1 (VMs), U3 (contenedores) · **CLF-C02:** D3 (tarea 3.3)
- **Fuentes:** [clase 01](../clases/2026-10-15-clase-01-intro-nube-ec2.md) · [clase 02](../clases/2026-10-20-clase-02-red-ec2-amis.md) · [clase 03](../clases/2026-10-22-clase-03-user-data.md) · [clase 04](../clases/2026-10-27-clase-04-aws-cli.md)

## Amazon EC2 (Elastic Compute Cloud)

`[D3]` Crea y administra servidores virtuales (**instancias EC2**). Es un ejemplo de IaaS ([M1](m01-conceptos-nube.md#modelos-de-servicio)).

| Elemento | Qué es | CLF |
|---|---|---|
| AMI | Imagen o plantilla para crear instancias: trae el SO y puede incluir software. En clase: Amazon Linux y Microsoft Windows Server 2025 Base | D3 |
| Tipo de instancia | `familia.tamaño` → capacidad de procesamiento y memoria (ej. `t3.micro`, `t2.medium`, `t3.medium`) | D3 |
| Par de claves | Autenticación criptográfica para entrar; la llave privada es el `.pem`. En Windows sirve además para descifrar la contraseña de `Administrator` | D2 |
| Configuración de red | VPC, subred, IP pública y security group → [M5](m05-redes.md) | D3 |
| Almacenamiento | Disco de la instancia; se mide en IOPS. En clase se usó **gp3** estándar | D3 |

`[externo]`:
- Para el CLF-C02 hay que reconocer las familias de instancias: de propósito general (`t`, `m`), optimizadas para cómputo (`c`), para memoria (`r`) y para almacenamiento (`i`).
- gp3 es un volumen **Amazon EBS** SSD de propósito general (M7).

## AMIs personalizadas

`[D3]` AMI creada desde una instancia ya configurada (ej. con Apache instalado y habilitado). Las instancias lanzadas desde ella arrancan con ese software, sin reinstalar.

- Crear: instancia → *Actions → Image and templates → Create image*. Ver en *Images → AMIs* (*Owned by me*).
- Usar: *Launch instance* → *My AMIs*.
- `[externo]` Por defecto reinicia la instancia; guarda snapshots EBS; es regional.

## Datos de usuario (user data)

`[D3]` Script que se pasa a la instancia al lanzarla (*Detalles avanzados → Datos de usuario*) y que se ejecuta en su primer arranque. Automatiza la configuración: actualizar, instalar y arrancar software, crear archivos.

- Empieza con `#!/bin/bash`. Lo ejecuta **cloud-init** como `root`.
- Corre **una vez por instancia** (primer arranque), no en cada reinicio.
- Logs: `/var/log/cloud-init.log` (cloud-init completo) y `/var/log/cloud-init-output.log` (salida y errores del script). Copia del script: `/var/lib/cloud/instances/<instance-id>/user-data.txt`.
- User data instala al arrancar; una [AMI personalizada](#amis-personalizadas) ya trae el software instalado, así que arranca más rápido.

Scripts y taller → [clase 03](../clases/2026-10-22-clase-03-user-data.md#procedimientos-de-lab).

## Amazon EC2 con la AWS CLI

`[D3]` Todo lo de la consola se puede hacer con comandos ([formas de interactuar](m01-conceptos-nube.md#formas-de-interactuar-con-aws)). Antes: `aws configure` con las credenciales del Learner Lab, región `us-east-1`.

| Comando | Para qué |
|---|---|
| `aws ec2 describe-instances [--output table] [--query '…']` | Listar instancias; `--query` elige columnas |
| `aws ec2 run-instances --image-id … --instance-type … --key-name … --count N` | Lanzar instancias |
| `… --security-group-ids sg-…` | Security group |
| `… --user-data file://user-data.sh` | [User data](#datos-de-usuario-user-data) desde un archivo |
| `… --tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value=…}]"` | Nombre al lanzar |
| `aws ec2 create-tags --resources i-… --tags Key=Name,Value=…` | Nombre después de lanzar |
| `… --query 'Instances[0].InstanceId' --output text` | Solo el ID de la instancia lanzada (para guardarlo en una variable) |

El nombre de una instancia es su etiqueta `Name`. Detalle y taller → [clase 04](../clases/2026-10-27-clase-04-aws-cli.md#procedimientos-de-lab). Lanzar con un security group creado por la CLI → [M5](m05-redes.md#security-groups-con-la-aws-cli) · [clase 05](../clases/2026-10-29-clase-05-security-groups-cli.md#4-lanzar-una-instancia-con-ese-security-group).

## Terraform: instancia EC2 y security group

`[D3]` IaC declarativa ([M1](m01-conceptos-nube.md#infraestructura-como-código-iac)). Usa las credenciales configuradas con la AWS CLI. Proyecto: carpeta con `main.tf`.

| Comando | Para qué |
|---|---|
| `terraform init` | Inicializa el proyecto |
| `terraform plan` | Muestra qué cambiaría |
| `terraform apply` | Crea o modifica lo declarado |
| `terraform destroy` | Destruye lo creado |

| Bloque | Ejemplo |
|---|---|
| `provider "aws"` | `region = "us-east-1"` |
| `resource "aws_security_group" "mi-sg"` | `ingress` 22 y 80 desde `0.0.0.0/0`; `egress` 80 |
| `resource "aws_instance" "mi_instancia_2"` | `ami`, `instance_type = "t2.micro"`, `key_name`, `tags = { Name = … }`, `vpc_security_group_ids = [aws_security_group.mi-sg.id]` |

`[externo]` Declarar `egress` en Terraform elimina la salida abierta por defecto. Código completo y procedimiento → [clase 06](../clases/2026-11-05-clase-06-terraform.md#procedimientos-de-lab).

## Acceso a la instancia

| SO | Protocolo | Usuario | Cómo |
|---|---|---|---|
| Amazon Linux | SSH (22) | `ec2-user` | `ssh -i llave.pem ec2-user@<DNS público>` o MobaXterm |
| Windows Server | RDP (3389 `[externo]`) | `Administrator` | Archivo `.rdp` + contraseña descifrada con el `.pem` |

Procedimientos paso a paso → [clase 01, lanzar EC2](../clases/2026-10-15-clase-01-intro-nube-ec2.md#lanzar-una-instancia-ec2-taller) · [clase 02, SSH, Apache, Windows y AMIs](../clases/2026-10-20-clase-02-red-ec2-amis.md#procedimientos-de-lab) · [clase 03, user data](../clases/2026-10-22-clase-03-user-data.md#procedimientos-de-lab).
