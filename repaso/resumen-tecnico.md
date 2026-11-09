# Resumen técnico (clases 01–06)

## Amazon EC2

| Elemento | Clave |
|---|---|
| AMI | Plantilla de la instancia: SO + software. Vistas: Amazon Linux, Windows Server 2025 Base |
| Tipo de instancia | `familia.tamaño` = CPU + memoria (`t2.medium`, `t3.medium`, `t3.large`) |
| Par de claves | `.pem` = llave privada. Linux: entrar por SSH. Windows: descifrar la contraseña de `Administrator` |
| Almacenamiento | gp3 (EBS SSD); rendimiento en IOPS |
| Nombre | Es la etiqueta `Name`; usar guiones, no `_` |

## Red

| Elemento | Clave |
|---|---|
| Amazon VPC | Red lógica privada en la nube |
| Subred | Segmento de la VPC |
| IPv4 pública / DNS público | Acceso desde internet (`ec2-<ip>.compute-1.amazonaws.com`); cambia al detener e iniciar `[externo]` |
| IPv4 privada | Interna de la VPC (`172.31.x.x`); hostname `ip-172-31-x-x.ec2.internal` |
| Security group | Firewall a nivel de instancia. Entrada: todo bloqueado salvo reglas. Salida: all traffic. Solo reglas de permitir, stateful, cambios inmediatos `[externo]` |

| Regla de entrada | Puerto | Origen |
|---|---|---|
| SSH | 22 | `0.0.0.0/0` |
| HTTP | 80 `[externo]` | `0.0.0.0/0` |
| RDP | 3389 `[externo]` | `0.0.0.0/0` |
| Todos los ICMP - IPv4 (ping) | — | `0.0.0.0/0` |

## Acceso a la instancia

| SO | Usuario | Cómo |
|---|---|---|
| Amazon Linux | `ec2-user` | `chmod 400 llave.pem` → `ssh -i llave.pem ec2-user@<DNS público>` · o MobaXterm (SSH + *Use private key*) |
| Windows | `Administrator` | *Connect → RDP client* → descargar `.rdp` → *Get password* → subir `.pem` → *Decrypt password* |

## Apache en Amazon Linux

```bash
sudo dnf update -y
sudo dnf install httpd -y
sudo systemctl enable --now httpd      # start + enable (arranca en cada inicio)
sudo systemctl status httpd            # active (running)
echo "<h1>Hola</h1>" | sudo tee /var/www/html/index.html
```

Ver en `http://<DNS público>` (http, no https; requiere regla HTTP).

## AMI personalizada

- Crear: instancia configurada → *Actions → Image and templates → Create image* → *Images → AMIs* (*Owned by me*) hasta *Available*.
- Usar: *Launch instance → My AMIs*. Arranca con el software ya instalado (Apache debe estar `enable`).

## User data y cloud-init

- *Detalles avanzados → Datos de usuario*. Empieza con `#!/bin/bash`.
- Lo ejecuta **cloud-init** como `root` (sin `sudo`), **solo en el primer arranque** (`once-per-instance`).
- Heredoc: `<<EOF` reemplaza variables (`$IP`); `<<'EOF'` las deja literales.

```bash
#!/bin/bash
dnf update -y
dnf install -y httpd
systemctl enable --now httpd
IP=$(hostname -I)          # IP privada
cat > /var/www/html/index.html <<EOF
<h1>IP: $IP</h1>
EOF
```

| Archivo | Contenido |
|---|---|
| `/var/log/cloud-init.log` | Log completo de cloud-init |
| `/var/log/cloud-init-output.log` | Salida y errores del script |
| `/var/lib/cloud/instance/user-data.txt` | Copia del script (solo `root`) |

`cloud-init status` → `running` / `done`.

| | Manual (SSH) | User data | AMI personalizada |
|---|---|---|---|
| Cuándo | Tras lanzar | Al lanzar, primer arranque | Una vez, en la instancia original |
| Arranque | Inmediato, luego instalas | Espera al script | Inmediato, ya instalado |

## Formas de interactuar con AWS

| Forma | Credenciales |
|---|---|
| Consola web | Usuario y contraseña |
| AWS CLI | Access key ID + secret access key + session token (temporales) |
| AWS SDK (librerías en código) | Las mismas que la CLI `[externo]` |

## AWS CLI

Credenciales: Learner Lab → *AWS Details → AWS CLI → Show*. Cambian en cada sesión del lab → repetir `aws configure`.

```powershell
aws --version
aws configure              # key ID, secret, token, región us-east-1, salida json
aws configure list         # ~/.aws/credentials y ~/.aws/config
aws help

aws ec2 describe-instances --output table
aws ec2 describe-instances --query 'Reservations[].Instances[].{Nombre:Tags[?Key==`Name`]|[0].Value,ID:InstanceId,Tipo:InstanceType,Estado:State.Name,IP_Publica:PublicIpAddress,IP_Privada:PrivateIpAddress}' --output table

aws ec2 run-instances --image-id ami-04b938437b69a2c9c --instance-type t2.medium --key-name mi-primera-llaves-ee --count 1 --security-group-ids sg-… --user-data file://user-data.sh --tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value=mi-instancia}]"

aws ec2 create-tags --resources i-… --tags Key=Name,Value=MiServidorWeb
```

| Parámetro | Consola |
|---|---|
| `--image-id` | AMI |
| `--instance-type` | Tipo de instancia |
| `--key-name` | Par de claves (sin `.pem`) |
| `--count` | Número de instancias |
| `--security-group-ids` | Security group existente |
| `--user-data file://…` | Datos de usuario |
| `--tag-specifications` | Name |

## Security groups con la AWS CLI

```powershell
$VPC_ID = (aws ec2 describe-vpcs --filters "Name=isDefault,Values=true" --query "Vpcs[0].VpcId" --output text)
$SG_APACHE_ID = (aws ec2 create-security-group --group-name "taller-sg-aws-cli-ee" --description "Security group creado desde AWS CLI" --vpc-id $VPC_ID --query 'GroupId' --output text)

aws ec2 authorize-security-group-ingress --group-id $SG_APACHE_ID --protocol tcp --port 80 --cidr 0.0.0.0/0    # HTTP
aws ec2 authorize-security-group-ingress --group-id $SG_APACHE_ID --protocol tcp --port 22 --cidr 0.0.0.0/0    # SSH
aws ec2 authorize-security-group-egress  --group-id $SG_APACHE_ID --protocol tcp --port 443 --cidr 192.168.1.0/24

$INSTANCE_ID = (aws ec2 run-instances --image-id ami-073f45061d35c86f0 --instance-type t3.micro --key-name mi-primera-llaves-ee --security-group-ids $SG_APACHE_ID --user-data file://user-data.sh --count 1 --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=taller-sg-cli-ee}]' --query 'Instances[0].InstanceId' --output text)
```

| Idea | Clave |
|---|---|
| `--query 'X' --output text` | Devuelve un solo valor, listo para una variable |
| CIDR | `0.0.0.0/0` cualquiera · `/32` una IP · `/24` 256 IPs |
| Salida | `authorize-…-egress` suma; la regla *All traffic* por defecto sigue hasta `revoke-security-group-egress` `[externo]` |

## Terraform

Instalación: `C:\Tools` → mover `terraform.exe` → agregar al PATH del usuario → `$env:PATH += ";C:\Tools"` → `terraform -version`.

IaC declarativa, usa las credenciales de la AWS CLI. Flujo: `terraform init` → `plan` → `apply` → `destroy`.

```hcl
provider "aws" { region = "us-east-1" }

resource "aws_security_group" "mi-sg" {
  name = "mi-security-group"
  description = "Security Group para mi instancia Terraform"
  vpc_id = "vpc-…"
  ingress { from_port = 22 to_port = 22 protocol = "tcp" cidr_blocks = ["0.0.0.0/0"] }
  ingress { from_port = 80 to_port = 80 protocol = "tcp" cidr_blocks = ["0.0.0.0/0"] }
  egress  { from_port = 80 to_port = 80 protocol = "tcp" cidr_blocks = ["0.0.0.0/0"] }
}

resource "aws_instance" "mi_instancia_2" {
  ami = "ami-…"
  instance_type = "t2.micro"
  key_name = "mi-primera-llaves-ee"
  tags = { Name = "MiInstanciaTerraform2" }
  vpc_security_group_ids = [aws_security_group.mi-sg.id]
}
```

`[externo]` En Terraform, declarar `egress` quita la salida abierta por defecto; en la CLI no.
