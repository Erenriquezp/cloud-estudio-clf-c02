# Clase 03: Datos de usuario (user data) y cloud-init en EC2

- **Fecha:** 2026-10-22 (fecha del taller 5; los logs marcan 2026-10-23 00:25 UTC = 22/10 19:25 hora de Ecuador)
- **Syllabus:** U1 Cloud Computing: Virtual Machines · Semana 2. El syllabus marca almacenamiento persistente, grupos de instancias y recursos dinámicos; en clase se vio la automatización del arranque de una instancia con user data.
- **Cloud Foundations:** [M6 Cómputo](../modulos/m06-computo.md#datos-de-usuario-user-data) · [M2 Economía y facturación](../modulos/m02-economia-facturacion.md) (tarea 3: AWS Support)
- **Original:** [_archivo/clase-03/notas-crudas.txt](../_archivo/clase-03/notas-crudas.txt)
- **Taller 5 entregado (2026-10-22):** [_archivo/clase-03/taller-05-user-data.pdf](../_archivo/clase-03/taller-05-user-data.pdf). Archivos descargados de la instancia: [cloud-init.log](../_archivo/clase-03/cloud-init.log) · [cloud-init-output.log](../_archivo/clase-03/cloud-init-output.log) · [user-data.txt](../_archivo/clase-03/user-data.txt)
- **Deberes:** Tarea 2 (user data con 3 herramientas) y Tarea 3 (consulta a mano de AWS Support). **Plazo de ambas: martes 2026-10-27, 18:00** (se aceptan varias entregas). Guía paso a paso [al final](#deberes). Entregadas: [tarea 2 (PDF)](../_archivo/clase-03/tarea-02.pdf) + [user-data.txt](../_archivo/clase-03/tarea-02-user-data.txt) · [tarea 3](../_archivo/clase-03/tarea-03-aws-support.pdf).

## Conceptos clave

| Concepto | Definición breve | CLF |
|---|---|---|
| Datos de usuario (user data) | Instrucciones (comandos de consola: actualizar, instalar apps, crear archivos) que se entregan a una instancia y se ejecutan en su **primera inicialización**. Se escriben en *Detalles avanzados → Datos de usuario* al lanzarla | D3 |
| Script de shell | User data que empieza con `#!/bin/bash`. Sin esa primera línea no se ejecuta como script `[externo]` | — |
| cloud-init | Programa del SO que, al arrancar, lee el user data desde AWS y lo ejecuta. Visto en los logs del taller (`Cloud-init v. 22.2.2`, `Datasource DataSourceEc2`) | D3 |
| Una vez por instancia | El script corre solo en el **primer arranque**, no en cada reinicio. Evidencia en el log: el módulo `scripts-user` corre `with frequency once-per-instance`; en el segundo arranque (00:32) cloud-init terminó en 0,6 s sin reinstalar nada y la página siguió mostrando la fecha del primero (00:26:10) | D3 |
| Usuario `root` | El user data se ejecuta como `root` (la página del taller mostró `Usuario: root`). Por eso `sudo` dentro del script sobra, aunque no estorba `[externo]` | — |
| IP privada | IP interna de la instancia en la VPC (ej. `172.31.33.77`). Es la que devuelve `hostname -I`; **no** es la IP pública con la que se abre la página | D3 |
| Hostname interno | `ip-172-31-33-77.ec2.internal`: nombre que AWS da a la instancia a partir de su IP privada | — |
| Heredoc (`<<EOF … EOF`) | Forma de Bash de escribir un bloque de texto a un archivo. `<<EOF` reemplaza las variables (`$IP`); `<<'EOF'` o `<<"EOF"` las deja literales | — |
| AWS Support | Servicio de AWS para cuenta, facturación y problemas técnicos, en 4 planes vigentes: Basic (gratis), Business Support+, Enterprise y Unified Operations `[externo]`. Tema de la tarea 3 → [M2](../modulos/m02-economia-facturacion.md#aws-support) | D4 |

### User data vs. instalar a mano (clase 02) vs. AMI personalizada

| | A mano por SSH ([clase 02](2026-10-20-clase-02-red-ec2-amis.md#4-instalar-un-servidor-web-apache-en-amazon-linux)) | User data | AMI personalizada |
|---|---|---|---|
| Cuándo se configura | Después de lanzar, conectado por SSH | Al lanzar, antes del primer arranque | Una vez, en la instancia original |
| Quién instala | Tú | cloud-init, solo | Ya viene instalado |
| Arranque | Inmediato, luego instalas | Tarda lo que tarde el script (en el taller, ~33 s) | Inmediato |
| Si cambias el software | Repites a mano | Editas el script | Creas otra AMI |

### Archivos de cloud-init (taller 5)

| Ruta | Qué contiene |
|---|---|
| `/var/log/cloud-init.log` | Log detallado de cloud-init: todo lo que hace para levantar la instancia (red, llaves SSH del host, módulos, lectura del user data) |
| `/var/log/cloud-init-output.log` | Salida de los comandos del user data: lo que imprimen, errores y mensajes. Aquí se revisa si el script falló |
| `/var/lib/cloud/instances/<instance-id>/user-data.txt` | Copia del user data que escribimos. Permisos `600` de `root` (visto en el log: `wb: [600]`), así que se lee con `sudo` |

`/var/lib/cloud/instance` es un enlace a la carpeta de la instancia actual (el log lo crea: `'/var/lib/cloud/instance' => '/var/lib/cloud/instances/i-09b8b7c766437f999'`), así que no hace falta saber el ID.

## Procedimientos de lab

### 1. Lanzar una instancia con user data

Mismos pasos que en la [clase 01](2026-10-15-clase-01-intro-nube-ec2.md#lanzar-una-instancia-ec2-taller), más el script:

| Paso | Valor usado en clase |
|---|---|
| Name | `instancia-user-data-ee` (script 1), `instancia-user-data2-ee` (script 2), `instancia-user-data4-ee` (script 3) |
| AMI | Amazon Linux |
| Tipo | `t3.medium` (1.ª; en la captura quedó `t2.medium`) · `t3.large` (2.ª a 4.ª) |
| Par de claves | `mi-primera-llaves-ee` |
| Security group | Existente: `launch-wizard-1` (ya tiene la regla HTTP de la [clase 02](2026-10-20-clase-02-red-ec2-amis.md#2-agregar-reglas-de-entrada-ping-y-http)) |
| **Detalles avanzados → Datos de usuario** | Pegar el script |

Verificar: esperar a que las comprobaciones de estado pasen (la página tarda un poco más que la instancia, porque primero corre el script) → abrir `http://<DNS público>`.

### 2. Script 1: Apache con "Hola Mundo"

```bash
#!/bin/bash
# Actualizar paquetes
dnf update -y
# Instalar Apache
dnf install -y httpd
# Iniciar y habilitar Apache (start + enable en un solo comando)
systemctl enable --now httpd
# Crear la página
cat > /var/www/html/index.html <<'EOF'
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Hola Mundo</title>
</head>
<body>
    <h1>¡Hola Mundo + TU NOMBRE!</h1>
</body>
</html>
EOF
```

Resultado: "¡Hola Mundo + TU NOMBRE!". En clase se escribió `sudo bash -c 'cat << "EOF" > … EOF'`; funciona igual, pero `sudo bash -c` sobra porque el script ya corre como `root`. `systemctl enable --now` reemplaza al par `start` + `enable` de la clase 02.

### 3. Script 2: misma idea, otra página

Igual que el 1, con una página que dice "Bienvenidos a AWS / Apache instalado con User Data / Computacion Grid y Cloud".

### 4. Script 3: página con variables del sistema

```bash
#!/bin/bash
dnf update -y
dnf install -y httpd
systemctl enable --now httpd

# Declarar las variables
IP=$(hostname -I)        # IP privada
USUARIO=$(whoami)        # root
HOSTNAME=$(hostname)     # ip-172-31-...ec2.internal
FECHA=$(date)            # en UTC

# EOF sin comillas para que Bash reemplace las variables
cat > /var/www/html/index.html <<EOF
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Instancia con user data TU NOMBRE</title>
</head>
<body>
    <h1>IP: $IP</h1>
    <h1>Usuario: $USUARIO</h1>
    <h1>Hostname: $HOSTNAME</h1>
    <h1>Fecha: $FECHA</h1>
</body>
</html>
EOF
```

Resultado real ([user-data.txt](../_archivo/clase-03/user-data.txt)):

```
IP: 172.31.33.77
Usuario: root
Hostname: ip-172-31-33-77.ec2.internal
Fecha: Fri Oct 23 00:26:10 UTC 2026
```

`[externo]` La IP que sale es la privada. La pública no la conoce el SO; se pide al servicio de metadatos de la instancia (`169.254.169.254`, que cloud-init consulta en el log).

### 5. Buscar y descargar los 3 archivos (taller)

Conectarse por SSH o MobaXterm ([clase 02, paso 3](2026-10-20-clase-02-red-ec2-amis.md#3-conectarse-por-ssh)):

```bash
sudo cat /var/log/cloud-init.log
sudo cat /var/log/cloud-init-output.log
sudo cat /var/lib/cloud/instance/user-data.txt      # o instances/<instance-id>/user-data.txt

# Copiar al home de ec2-user para poder descargarlos (user-data.txt es solo de root)
sudo cp /var/log/cloud-init.log /var/log/cloud-init-output.log /var/lib/cloud/instance/user-data.txt ~
sudo chown ec2-user: ~/cloud-init*.log ~/user-data.txt
```

Descargar `[externo]`: en MobaXterm, panel SFTP de la izquierda → clic derecho → *Download*. Con terminal, desde tu PC:

```bash
scp -i "mi-primera-llaves-ee.pem" ec2-user@<DNS público>:~/user-data.txt .
```

Qué buscar en cada uno:

- `cloud-init-output.log`: la instalación de `httpd` termina en `Complete!` y `Created symlink … httpd.service` (efecto de `enable`).
- `cloud-init.log`: `Running module scripts-user … with frequency once-per-instance`.
- `user-data.txt`: el script tal cual se escribió.

## Posibles preguntas de lección o examen

1. ¿Qué es el user data de una instancia EC2? ¿Dónde se configura y cuándo se ejecuta?
2. ¿Con qué usuario corre el user data? ¿Hace falta `sudo`?
3. ¿Qué hace `systemctl enable --now httpd`?
4. ¿Por qué el `EOF` del script 3 va sin comillas? ¿Qué pasaría con `<<'EOF'`?
5. ¿Qué IP devuelve `hostname -I`, la pública o la privada?
6. ¿Qué es cloud-init? Nombra los 3 archivos del taller y qué contiene cada uno.
7. Si el script tiene un error, ¿en qué archivo lo buscas?
8. Si reinicias la instancia, ¿se vuelve a ejecutar el user data? ¿Cómo lo comprobaste en los logs?
9. Diferencia entre configurar con user data y usar una AMI personalizada.
10. ¿Qué es AWS Support y qué planes tiene? (tarea 3)

### Tipo CLF-C02

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

## Deberes

Plazo de las dos tareas: **martes 2026-10-27, 18:00**. Se aceptan varias entregas.

### Tarea 2: EC2 User Data

**Enunciado:** crear una instancia EC2 con un user data que instale al menos 3 herramientas. Subir dos archivos: las evidencias de las herramientas instaladas y el `user-data.txt`.

#### Herramientas elegidas

Todas vienen en el repositorio oficial de Amazon Linux 2023, así que se instalan con `dnf` sin agregar repositorios `[externo]`. Son 5, por si alguna falla:

| Herramienta | Paquete | Para qué sirve | Comando de verificación |
|---|---|---|---|
| Apache | `httpd` | Servidor web; muestra la página de evidencias | `httpd -v` |
| Git | `git` | Control de versiones | `git --version` |
| Docker | `docker` | Contenedores (se ve en la U3 del syllabus) | `docker --version` |
| Node.js | `nodejs` | Entorno de ejecución de JavaScript | `node --version` |
| Tree | `tree` | Muestra carpetas en forma de árbol | `tree --version` |

#### Script de user data

Usa la misma técnica del script 3: guarda las versiones en variables y las escribe en la página. Por eso `EOF` va **sin comillas**.

```bash
#!/bin/bash
# Actualizar paquetes
dnf update -y

# Instalar las herramientas
dnf install -y httpd git docker nodejs tree

# Iniciar y habilitar los servicios
systemctl enable --now httpd
systemctl enable --now docker

# Permitir que ec2-user use docker sin sudo
usermod -aG docker ec2-user

# Guardar las versiones instaladas
APACHE=$(httpd -v | head -n 1)
GIT=$(git --version)
DOCKER=$(docker --version)
NODE=$(node --version)
TREE=$(tree --version)
HOSTNAME=$(hostname)
FECHA=$(date)

# Construir la página de evidencias (EOF sin comillas para reemplazar las variables)
cat > /var/www/html/index.html <<EOF
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Tarea 2 User Data - TU NOMBRE</title>
</head>
<body>
    <h1>Tarea 2: herramientas instaladas con User Data</h1>
    <h2>TU NOMBRE - Computacion Grid y Cloud</h2>
    <ul>
        <li><b>Apache:</b> $APACHE</li>
        <li><b>Git:</b> $GIT</li>
        <li><b>Docker:</b> $DOCKER</li>
        <li><b>Node.js:</b> $NODE</li>
        <li><b>Tree:</b> $TREE</li>
    </ul>
    <p>Hostname: $HOSTNAME</p>
    <p>Fecha de instalacion: $FECHA</p>
</body>
</html>
EOF
```

#### Paso a paso

1. **Learner Lab** → *Start Lab* → esperar el círculo verde → *AWS* → **EC2** → **Launch instance**.
2. Configurar el lanzamiento:

   | Campo | Valor |
   |---|---|
   | Name | `tarea-02-user-data-ee` |
   | AMI | Amazon Linux 2023 |
   | Tipo | `t3.medium` |
   | Par de claves | `mi-primera-llaves-ee` |
   | Security group | *Seleccionar existente* → `launch-wizard-1` (tiene SSH y HTTP) |
   | **Detalles avanzados → Datos de usuario** | Pegar el script completo; la primera línea debe ser `#!/bin/bash` |

3. **Launch instance**. Esperar a que diga *En ejecución* y pase las comprobaciones de estado. Después esperar 2 o 3 minutos más: el script instala 5 paquetes.
4. **Evidencia 1 (consola):** lista de instancias con `tarea-02-user-data-ee` en ejecución, más la pestaña *Detalles* con el ID, la IP pública y el DNS.
5. **Evidencia 2 (user data en la consola):** seleccionar la instancia → *Acciones → Configuración de instancia → Editar datos de usuario*, que muestra el script. Capturar y luego **Cancelar** sin guardar `[externo]`.
6. **Evidencia 3 (página):** abrir `http://<DNS público>` (con **http**, no https). Debe listar las 5 versiones.
7. **Evidencia 4 (terminal):** conectarse por MobaXterm o SSH ([clase 02, paso 3](2026-10-20-clase-02-red-ec2-amis.md#3-conectarse-por-ssh)) y ejecutar:

   ```bash
   httpd -v
   git --version
   docker --version
   node --version
   tree --version
   systemctl status httpd docker --no-pager   # ambos deben decir active (running)
   cloud-init status                          # debe decir status: done
   ```

8. **Evidencia 5 (log):** `sudo tail -n 30 /var/log/cloud-init-output.log`. Debe mostrar `Complete!` y los `Created symlink … httpd.service` y `docker.service`.
9. **Descargar `user-data.txt`:**

   ```bash
   sudo cp /var/lib/cloud/instance/user-data.txt ~
   sudo chown ec2-user: ~/user-data.txt
   cat ~/user-data.txt     # comprobar que es el script
   ```

   En MobaXterm: panel SFTP de la izquierda → refrescar → clic derecho sobre `user-data.txt` → *Download*.
10. **Armar el PDF de evidencias** con el mismo formato de los talleres (o con `_archivo/curso/plantilla.docx`):
    - Encabezado: Universidad Central del Ecuador · Nombre · Fecha · "Tarea 2 Computación Grid y Cloud".
    - Las capturas de los pasos 4 a 8, cada una con una línea que diga qué muestra.
11. **Subir** `tarea-02.pdf` y `user-data.txt` a la tarea.
12. **Apagar:** detener o terminar la instancia y hacer *End Lab* para no gastar crédito del Learner Lab `[externo]`.

#### Si algo falla

| Síntoma | Causa probable | Solución |
|---|---|---|
| La página no abre | El script sigue corriendo | Esperar y revisar `cloud-init status` (dice `running` mientras tanto) |
| La página no abre y el script ya terminó | Falta la regla HTTP o se usó https | Revisar el security group y usar `http://` |
| La página muestra `$GIT` literal | Se escribió `<<'EOF'` con comillas | Volver a lanzar con `<<EOF` |
| Una versión sale vacía | Ese paquete no se instaló | Buscar el error en `/var/log/cloud-init-output.log` |
| Cambié el script y no pasa nada | El user data corre solo en el primer arranque | Lanzar una instancia nueva |

### Tarea 3: AWS Support

**Enunciado:** consultar **a mano** qué es AWS Support y sus tipos; subir la hoja escaneada o en foto. Teoría completa en [M2](../modulos/m02-economia-facturacion.md#aws-support).

#### Texto para copiar a mano (cabe en una hoja)

> **Universidad Central del Ecuador · Computación Grid y Cloud**
> **Tarea 3: AWS Support** · TU NOMBRE · 27/10/2026
>
> **¿Qué es AWS Support?**
> Es el servicio de soporte de Amazon Web Services. Ayuda a los clientes con preguntas de cuenta y facturación, aumentos de cuotas de servicio y problemas técnicos de sus recursos en AWS. AWS lo define como "soporte impulsado por IA y respaldado por expertos de AWS". Se ofrece en planes: cada plan incluye lo del anterior, se paga por mes y no exige contrato a largo plazo. El precio de los planes de pago es el mayor entre un mínimo mensual y un porcentaje del gasto en AWS.
>
> **Tipos (planes vigentes)**
>
> 1. **Basic:** gratis e incluido en todas las cuentas. Atención 24/7 de cuenta y facturación, documentación, whitepapers, foro AWS re:Post, AWS Health Dashboard y checks básicos de Trusted Advisor. No incluye casos técnicos.
> 2. **Business Support+:** desde USD 29/mes. Es el mínimo recomendado para cargas en producción. Ingenieros de soporte 24/7 por teléfono, web y chat, asistencia con IA, casos ilimitados, todos los checks de Trusted Advisor. Respuesta en menos de 30 min si un sistema crítico cae.
> 3. **Enterprise Support:** desde USD 5.000/mes, para cargas críticas del negocio. Agrega un Technical Account Manager (TAM) designado, respuesta en 15 min, AWS Security Incident Response y revisiones estratégicas.
> 4. **Unified Operations:** desde USD 50.000/mes, para cargas de misión crítica. Agrega ingenieros de incidentes (respuesta en 5 min), especialistas por dominio, monitoreo 24/7 y guía de arquitectura.
>
> **Planes anteriores:** Developer y Business se retiran el 01/01/2027 (pasan a Business Support+). Enterprise On-Ramp pasa a Enterprise Support.
>
> **Herramientas relacionadas:** AWS Trusted Advisor (recomendaciones de costo, seguridad y rendimiento), AWS Health Dashboard (estado de los servicios), AWS re:Post (foro), AWS Support Center (casos).
>
> **Fuente:** aws.amazon.com/premiumsupport/plans (consultado el 25/10/2026)

#### Para entregar

1. Copiarlo a mano en una hoja: puede ir como lista o como una tabla de 4 filas (Plan | Costo | Para qué | Lo clave).
2. Escanear con la cámara del celular (Google Drive → *Escanear* o la app de notas) para que salga recto y en PDF.
3. Subir como `tarea-03.pdf` (o la foto).
