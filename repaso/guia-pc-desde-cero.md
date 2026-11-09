# Guía: PC desde cero (Terraform, AWS CLI, Git y GitHub)

`[externo]` Para PC de laboratorio con Windows. **No requiere administrador**: todo queda en `$HOME\Tools`. Comandos para PowerShell.

Probada el 2026-11-09 en Windows PowerShell 5.1 sin administrador, con Terraform 1.17.1, AWS CLI 2.39.7 y Git 2.56.0.

**Necesitas:** internet, tu cuenta de GitHub y el Learner Lab iniciado.

## 1. Instalar las tres herramientas

Pega el bloque completo. Tarda unos minutos.

```powershell
$ProgressPreference = 'SilentlyContinue'
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
$tools = "$HOME\Tools"
$temp = "$env:TEMP\pc-cero"
New-Item -ItemType Directory -Force $tools, $temp | Out-Null

# Terraform
Invoke-WebRequest "https://releases.hashicorp.com/terraform/1.17.1/terraform_1.17.1_windows_amd64.zip" -OutFile "$temp\terraform.zip" -UseBasicParsing
Expand-Archive "$temp\terraform.zip" -DestinationPath "$tools\terraform" -Force

# AWS CLI (extrae el instalador, sin instalar en el sistema)
Invoke-WebRequest "https://awscli.amazonaws.com/AWSCLIV2.msi" -OutFile "$temp\aws.msi" -UseBasicParsing
Start-Process msiexec.exe -ArgumentList "/a `"$temp\aws.msi`" /qn TARGETDIR=`"$tools\awscli`"" -Wait

# Git portable
$release = Invoke-RestMethod "https://api.github.com/repos/git-for-windows/git/releases/latest"
$asset = $release.assets | Where-Object { $_.name -like "PortableGit-*-64-bit.7z.exe" } | Select-Object -First 1
Invoke-WebRequest $asset.browser_download_url -OutFile "$temp\git.exe" -UseBasicParsing
Start-Process "$temp\git.exe" -ArgumentList "-o`"$tools\git`" -y" -Wait

# PATH del usuario y de esta terminal
$rutas = "$tools\terraform;$tools\awscli\Amazon\AWSCLIV2;$tools\git\cmd"
$pathUsuario = [Environment]::GetEnvironmentVariable("Path", "User")
if ($pathUsuario -notlike "*$tools\terraform*") {
    [Environment]::SetEnvironmentVariable("Path", "$pathUsuario;$rutas", "User")
}
$env:Path = "$rutas;$env:Path"
```

Comprobar:

```powershell
terraform -version
aws --version
git --version
```

## 2. Conectar con AWS (en cada sesión del lab)

1. Learner Lab → **AWS Details** → AWS CLI → **Show**.
2. Copia el bloque completo, desde `[default]` hasta el token.
3. Con el bloque copiado, ejecuta:

```powershell
New-Item -ItemType Directory -Force "$HOME\.aws" | Out-Null
Get-Clipboard | Set-Content "$HOME\.aws\credentials" -Encoding ascii
aws configure set region us-east-1
aws configure set output json
aws sts get-caller-identity
```

Debe mostrar tu `Account` y un `Arn`. Más detalle en la [clase 04](../clases/2026-10-27-clase-04-aws-cli.md#2-configurar-las-credenciales-del-learner-lab).

## 3. Configurar Git

```powershell
git config --global user.name "TU_USUARIO_DE_GITHUB"
git config --global user.email "TU_CORREO_DE_GITHUB"
git config --global init.defaultBranch main
git config --global credential.helper manager
```

## 4. Subir el código a GitHub

La primera vez que hagas `git push` se abrirá el navegador para iniciar sesión en GitHub.

### Opción A: repositorio nuevo

1. Crea el repositorio en <https://github.com/new>, **vacío**: sin README, sin `.gitignore` y sin licencia.
2. Entra a la carpeta del proyecto y crea el `.gitignore`:

```powershell
@'
.terraform/
*.tfstate
*.tfstate.*
*.tfvars
*.tfvars.json
*.pem
*.rdp
crash.log
'@ | Set-Content .gitignore -Encoding ascii
```

3. Prepara los archivos y revisa la lista:

```powershell
git init
git add .
git status
```

En la lista **no** debe aparecer ningún `.tfstate`, `.pem`, `.rdp` ni la carpeta `.terraform/`. Si está bien:

```powershell
git commit -m "Primer commit"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/TU_REPO.git
git push -u origin main
```

### Opción B: rama nueva en un repositorio que ya existe

```powershell
git clone https://github.com/TU_USUARIO/TU_REPO.git
cd TU_REPO
git switch -c nombre-de-la-rama
```

Copia o edita tus archivos dentro de esa carpeta y luego:

```powershell
git add .
git status
git commit -m "Describe el cambio"
git push -u origin nombre-de-la-rama
```

Enlace para entregar: `https://github.com/TU_USUARIO/TU_REPO/tree/nombre-de-la-rama`.

## 5. Usar Terraform

Dentro de la carpeta del proyecto ([clase 06](../clases/2026-11-05-clase-06-terraform.md#flujo-de-trabajo)):

```powershell
terraform init
terraform plan
terraform apply
terraform destroy
```

Guarda los `.pem` fuera de la carpeta del repositorio.

## 6. Antes de irte: limpiar la PC

⚠️ Solo en una PC compartida. En tu PC personal, esto borra tu configuración.

1. Ejecuta `terraform destroy` y luego *End Lab*.
2. Comprueba que el código esté en GitHub.
3. Borra tus credenciales:

```powershell
git credential-manager github logout TU_USUARIO
Remove-Item "$HOME\.aws" -Recurse -Force
Remove-Item "$HOME\.gitconfig" -Force
```

4. Cierra tu sesión de GitHub y de AWS Academy en el navegador.
5. Borra la carpeta del proyecto y cualquier `.pem` o `.rdp`, también de Descargas.
6. Opcional, para quitar las herramientas: `Remove-Item "$HOME\Tools" -Recurse -Force`.

## Errores comunes

| Error | Causa | Solución |
|---|---|---|
| `terraform` / `aws` / `git` no se reconoce | La terminal no tiene el PATH nuevo | Abre otra terminal |
| Falla `Invoke-RestMethod` de GitHub (403) | Límite de consultas compartido por la red del laboratorio | Descarga *Portable* en <https://git-scm.com/downloads/win> y ejecútalo extrayendo en `$HOME\Tools\git` |
| `ExpiredToken` / `Unable to locate credentials` | La sesión del lab cambió o faltan credenciales | Repite el paso 2 |
| `InvalidVpcID.NotFound` / `InvalidGroup.NotFound` | El `vpc_id` del `main.tf` es de otra cuenta | Usa el resultado de `aws ec2 describe-vpcs --filters Name=isDefault,Values=true --query "Vpcs[].VpcId" --output text` |
| `InvalidGroup.Duplicate` | Ya existe un security group con ese nombre | Cambia `name` o borra el anterior |
| `InvalidKeyPair.NotFound` | El par de claves del `main.tf` no existe en esa cuenta | Crea el par o cambia `key_name` |
| `Author identity unknown` | Falta la configuración de Git | Repite el paso 3 |
| `rejected ... fetch first` al hacer `push` | El repositorio no estaba vacío | `git pull --rebase origin main` y de nuevo `git push -u origin main` |
| Aparece `terraform.tfstate` en `git status` | Se agregó antes del `.gitignore` | `git rm --cached terraform.tfstate` |
