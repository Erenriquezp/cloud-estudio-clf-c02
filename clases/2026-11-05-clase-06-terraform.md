# Clase 06: Terraform, infraestructura como código en AWS

- **Fecha:** 2026-11-05 (fecha del taller 8)
- **Syllabus:** U1 Cloud Computing: Virtual Machines · Semana 4. El syllabus marca modelo de costos (precios de EC2, AWS Pricing Calculator `[externo]`); en clase se vio Terraform. Continúa la [clase 05](2026-10-29-clase-05-security-groups-cli.md), donde se instaló.
- **Cloud Foundations:** [M1 Infraestructura como código](../modulos/m01-conceptos-nube.md#infraestructura-como-código-iac) · [M6 Terraform](../modulos/m06-computo.md#terraform-instancia-ec2-y-security-group) · [M5 Security groups](../modulos/m05-redes.md#security-groups)
- **Original:** [_archivo/clase-06/notas-crudas.txt](../_archivo/clase-06/notas-crudas.txt)
- **Taller 8 entregado (2026-11-05):** [_archivo/clase-06/taller-08-terraform.pdf](../_archivo/clase-06/taller-08-terraform.pdf) (solo contiene el enlace al repositorio [Erenriquezp/cloud-u1-p5-ee](https://github.com/Erenriquezp/cloud-u1-p5-ee)). Código de la clase: [practicas/cloud-u1-p5-ee/](../practicas/cloud-u1-p5-ee/main.tf) (`main.tf`, `.gitignore`, `README.md`; repositorio de Git).

## Conceptos clave

| Concepto | Definición breve | CLF |
|---|---|---|
| Infraestructura como código (IaC) | Definir, crear, actualizar o eliminar infraestructura mediante archivos de configuración, en vez de hacerlo con clics o comandos sueltos | D3 |
| Terraform | Herramienta **de código abierto** de IaC. Se escribe la infraestructura en archivos `.tf` y Terraform la crea en AWS | D3 `[externo]` |
| `main.tf` | Archivo principal de la configuración, extensión `.tf` | — |
| Paradigma declarativo | Se declara el **estado objetivo** (qué debe existir), no los pasos para lograrlo | D3 |
| Automatización | Beneficio de la IaC: la misma infraestructura se crea, cambia o borra de forma repetible con pocos comandos | D1 · D3 |
| Provider (`provider "aws"`) | Bloque que indica a Terraform con qué plataforma trabaja y en qué región (`us-east-1`) `[externo]` | — |
| Recurso (`resource`) | Pieza de infraestructura: `resource "<tipo>" "<nombre local>" { … }`. Ej.: `aws_instance` (instancia EC2), `aws_security_group` | — |
| Referencia entre recursos | `aws_security_group.mi-sg.id`: un recurso usa un dato de otro; Terraform crea primero el security group y después la instancia `[externo]` | — |
| Estado (`terraform.tfstate`) `[externo]` | Archivo donde Terraform guarda qué recursos creó y su situación actual. Sin él no sabría qué actualizar o destruir. Puede contener datos sensibles: no subirlo a un repositorio público | — |

### Flujo de trabajo

| Comando | Qué hace |
|---|---|
| `terraform init` | Convierte la carpeta en un proyecto de Terraform e inicializa (descarga el provider `[externo]`) |
| `terraform plan` | Analiza la configuración del `main.tf` y muestra qué cambiaría, sin ejecutar nada |
| `terraform apply` | Ejecuta las acciones definidas en el `main.tf` (pide confirmar `yes` `[externo]`) |
| `terraform destroy` | Destruye los recursos creados |
| `terraform -version` | Comprueba la instalación ([clase 05](2026-10-29-clase-05-security-groups-cli.md#5-instalar-terraform-en-windows)) |

### Terraform vs. AWS CLI

| | AWS CLI (clases 04–05) | Terraform |
|---|---|---|
| Estilo | Comandos, uno por acción (imperativo `[externo]`) | Archivo con el estado final deseado (declarativo) |
| Cómo se repite | Volver a ejecutar el script | `terraform apply` |
| Cómo se borra | Un comando por recurso (`terminate-instances`, `delete-security-group`) | `terraform destroy` |
| Credenciales | `aws configure` | Las mismas: Terraform se **conecta con la configuración de la AWS CLI** |

## Procedimientos de lab

### 1. Preparar el proyecto

1. Con el Learner Lab iniciado, configurar la CLI ([clase 04](2026-10-27-clase-04-aws-cli.md#2-configurar-las-credenciales-del-learner-lab)). Terraform usa esas credenciales.
2. Crear la carpeta `cloud-u1-p5-ee` (el sufijo `-ee` son las iniciales del estudiante) y dentro el archivo `main.tf`.
3. Abrir la carpeta en el editor. `Ctrl + Espacio` activa el **autocompletado** (tipos de recurso y argumentos).
4. En la terminal, dentro de la carpeta: `terraform init`.

### 2. Primera instancia: `aws_instance`

Se empezó solo con el provider y un recurso `aws_instance` (la instancia EC2) llamado `mi_instancia_2`, y se corrió:

```powershell
terraform plan      # revisar qué va a crear
terraform apply     # crearla
```

### 3. Agregar el security group con reglas de entrada y salida

Se añadió un `aws_security_group` y la instancia lo usa. Configuración final:

```hcl
# Configuracion del proveedor de AWS
provider "aws" {
  region = "us-east-1" #region donde se desplegaran los recursos
}

# Crear el Security Group
resource "aws_security_group" "mi-sg" {
  name        = "mi-security-group"
  description = "Security Group para mi instancia Terraform"
  vpc_id      = "vpc-0a144b21b34dcee32"

  # Regla de entrada para SSH
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  # Regla de entrada para HTTP
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  # Regla de salida para HTTP
  egress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# Declaracion de los recursos AWS
resource "aws_instance" "mi_instancia_2" {
  ami           = "ami-0c230065b91270bc6" # ID de la imagen
  instance_type = "t2.micro"              # Tipo
  key_name      = "mi-primera-llaves-ee"  # Nombre del par de llaves
  tags = {
    Name = "MiInstanciaTerraform2"        # Etiqueta para identificar la instancia
  }
  vpc_security_group_ids = [aws_security_group.mi-sg.id] # Asignar el Security Group
}
```

| Parte | Equivale en la consola / CLI |
|---|---|
| `ami`, `instance_type`, `key_name` | `--image-id`, `--instance-type`, `--key-name` de `run-instances` |
| `tags = { Name = … }` | Etiqueta `Name` (`--tag-specifications`) |
| `vpc_security_group_ids = [ … ]` | `--security-group-ids` |
| `ingress { … }` / `egress { … }` | `authorize-security-group-ingress` / `-egress` |
| `from_port` / `to_port` / `protocol` / `cidr_blocks` | `--port` / `--protocol` / `--cidr` (rango de puertos; igual al puerto único si son iguales) `[externo]` |

Luego otra vez `terraform plan` y `terraform apply`: Terraform compara lo declarado con el estado y aplica solo la diferencia.

### 4. Destruir

```powershell
terraform destroy
```

Elimina la instancia y el security group. `[externo]` Hacerlo al terminar y luego *End Lab*.

## Aclaraciones `[externo]`

| Tema | Aclaración |
|---|---|
| Salida del SG | En Terraform, al declarar un bloque `egress` el SG queda **solo** con esa salida: se elimina la regla *All traffic* por defecto. Distinto a la CLI, donde `authorize-security-group-egress` la deja ([clase 05](2026-10-29-clase-05-security-groups-cli.md#correcciones-y-límites-externo)). Aquí la instancia solo puede salir por el puerto 80: sin HTTPS (443) ni DNS |
| Servidor web | Esta instancia no tiene `user_data`: aunque el puerto 80 esté abierto, no hay nada que responda. SSH (22) sí funciona con el `.pem` |
| Identificadores fijos | `vpc_id` y `ami` van escritos a mano (la VPC es la misma que se usó en la clase 05); la AMI cambia por región |
| Archivos locales | `terraform init` crea la carpeta `.terraform/` y `apply` el `terraform.tfstate` (y su `.backup`). El repositorio de la clase contiene el estado: contiene datos de la instancia |
| Costos | El estado del repositorio entregado aún registra la instancia y el SG (sin `destroy` posterior): revisar en la consola que no queden recursos |

## Posibles preguntas de lección o examen

1. ¿Qué es Terraform y qué significa que sea declarativo?
2. Menciona dos beneficios de la IaC.
3. Ordena los comandos: `apply`, `init`, `destroy`, `plan`. ¿Qué hace cada uno?
4. ¿Qué extensión tiene el archivo principal de Terraform?
5. ¿Cómo se conecta Terraform a la cuenta de AWS en el lab?
6. ¿Para qué sirve `aws_security_group.mi-sg.id` dentro de `aws_instance`?
7. ¿Qué diferencia hay entre crear una instancia con la AWS CLI y con Terraform?
8. ¿Qué reglas tiene el security group del `main.tf` de la clase?

### Tipo CLF-C02

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

## Deber: Tarea 4, IaC con Terraform

Enunciado: replicar el taller 8 con Terraform para una instancia Windows. Vence el **2026-11-10 a las 18:00**. Código: [practicas/cloud-u1-t4-windows-ee/](../practicas/cloud-u1-t4-windows-ee/main.tf), publicado en la rama [`tarea-4-windows`](https://github.com/Erenriquezp/cloud-u1-p5-ee/tree/tarea-4-windows) del repositorio del taller 8. Desplegado y destruido el 2026-11-09 (estado final: 0 recursos). Pasos y diferencias con el taller 8 en su [README](../practicas/cloud-u1-t4-windows-ee/README.md).

- Valores del [taller 3](2026-10-20-clase-02-red-ec2-amis.md#5-lanzar-una-ec2-con-windows-taller-3): Windows Server 2025 Base, `t3.medium`, `mi-segunda-llaves-ee`, RDP (3389). Conexión: [paso 6 de la clase 02](2026-10-20-clase-02-red-ec2-amis.md#6-conectarse-por-rdp).
- `[externo]` `data "aws_ami"` consulta la AMI más reciente en vez de escribir su ID a mano.
- `[externo]` La salida permite todo el tráfico: con el `egress` del taller 8 (solo TCP 80), Windows se quedaría sin DNS ni HTTPS.
- `[externo]` El security group se llama `mi-security-group-windows` para no chocar con `mi-security-group`, que puede seguir existiendo en la VPC.

## ⚠️ Dudas

- **Forma de entrega de la tarea 4:** el enunciado no la indica. El taller 8 se entregó como enlace a un repositorio.
- **`terraform plan` / `apply` de la instancia sin security group:** las notas dicen "mi_instancia_2 - plan, apply" sin dar la configuración de esa primera versión. Se dedujo que solo tenía provider + `aws_instance`.
- **Estado de la instancia:** el estado entregado no refleja si se hizo `terraform destroy` al final. Confirmar en la consola.
