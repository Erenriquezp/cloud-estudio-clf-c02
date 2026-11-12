# Clase 02: Red de EC2, security groups, servidor web, Windows y AMIs personalizadas

- **Fecha:** 2026-10-20
- **Syllabus:** U1 Cloud Computing: Virtual Machines · Semana 2. El syllabus marca para esta semana almacenamiento persistente, grupos de instancias y recursos dinámicos (Amazon EBS y Auto Scaling `[externo]`). En clase se vio la red de la instancia, los security groups y las AMIs.
- **Cloud Foundations:** [M5 Redes](../modulos/m05-redes.md) · [M6 Cómputo](../modulos/m06-computo.md)
- **Talleres entregados (2026-10-20):** [taller 2: ping y servidor web](../_archivo/clase-02/taller-02-ping-servidor-web.pdf) · [taller 3: EC2 con Windows](../_archivo/clase-02/taller-03-ec2-windows.pdf) · [taller 4: AMIs personalizadas](../_archivo/clase-02/taller-04-amis-personalizadas.pdf)
- **Deber:** AMIs con Windows. Entregar captura de pantalla con el programa instalado y de las AMIs. **Plazo: jueves 2026-10-22, 18:00.**

## Conceptos clave

| Concepto | Definición breve | CLF |
|---|---|---|
| Dirección IPv4 pública | IP con la que se llega a la instancia desde internet. `[externo]` Corrección a las notas: no "siempre" se asigna; depende de que la subred o el lanzamiento la habiliten (en la VPC por defecto sí). Cambia cada vez que la instancia se detiene y se vuelve a iniciar | D3 |
| DNS público | Nombre asociado a la IP pública, con la forma `ec2-34-230-53-117.compute-1.amazonaws.com` | D3 |
| Elastic IP `[externo]` | IP pública fija que se asocia a una instancia y no cambia al detenerla | D3 |
| Amazon VPC | Virtual Private Cloud: red privada virtual, es decir, una red lógica creada a nivel de la nube | D3 |
| Subred (subnet) | Segmento de red creado a partir de la VPC. `[externo]` Cada subred vive en una sola zona de disponibilidad | D3 |
| Security group | Firewall virtual que protege las instancias. Nosotros configuramos sus reglas de entrada y de salida | D2 · D3 |
| Regla de entrada | Tráfico permitido hacia la instancia. Ej.: SSH (puerto 22) para administrar, HTTP para la web, ICMP para ping | D2 |
| Regla de salida | Tráfico que sale de la instancia. Por defecto: **All traffic** (salida libre a internet) | D2 |
| `0.0.0.0/0` | Origen "cualquier dirección IPv4" | D2 |
| ICMP | Protocolo que usa `ping`. Sin una regla de entrada ICMP, el ping a la instancia falla | — |
| Apache (`httpd`) | Servidor web instalado en Amazon Linux | — |
| `dnf` | Gestor de paquetes de Amazon Linux 2023 `[externo]` | — |
| `ec2-user` / `Administrator` | Usuario por defecto para entrar a Amazon Linux (SSH) / a Windows (RDP) | — |
| AMI (Amazon Machine Image) | Imagen o plantilla para crear instancias EC2. Trae un SO ya instalado y puede incluir software | D3 |
| AMI personalizada | AMI creada por ti a partir de una instancia ya configurada (ej. con Apache). Permite lanzar copias idénticas sin reinstalar nada | D3 |
| Contraseña de Windows | AWS la cifra con la llave pública del par de claves; para verla se sube el `.pem` (llave privada) y se descifra | D2 |

### Puertos usados

| Protocolo | Puerto | Para qué |
|---|---|---|
| SSH | 22 | Terminal remota en Linux |
| HTTP | 80 `[externo]` | Ver la página web en el navegador |
| RDP | 3389 `[externo]` | Escritorio remoto en Windows |
| ICMP | (no usa puertos) | `ping` |

### Security group: comportamiento `[externo]`

- Regla nueva = permiso. Solo existen reglas de **permitir**, no de denegar.
- Entrada por defecto: todo bloqueado hasta que agregas una regla. Salida por defecto: todo permitido.
- Es **stateful**: si una petición entra permitida, su respuesta sale sola, sin regla de salida.
- Actúa a nivel de **instancia**. Las notas dicen "se crea uno por defecto": el asistente de lanzamiento crea uno llamado `launch-wizard-N`, y además cada VPC trae un security group llamado `default`.
- Los cambios en las reglas se aplican de inmediato, sin reiniciar la instancia (en el taller 2 el ping pasó de 100% perdidos a 0% solo con agregar la regla).

## Procedimientos de lab

### 1. Datos de red de la instancia

EC2 → Instances → seleccionar la instancia → pestañas **Detalles / Redes / Seguridad**: IPv4 pública, DNS público, VPC ID, Subnet ID y security groups.

### 2. Agregar reglas de entrada (ping y HTTP)

Instancia → **Seguridad** → clic en el security group → **Editar reglas de entrada** → **Agregar regla**:

| Tipo | Origen | Resultado |
|---|---|---|
| Todos los ICMP - IPv4 | `0.0.0.0/0` | Responde a `ping <DNS público>` |
| HTTP | `0.0.0.0/0` | La página abre en el navegador |

→ **Guardar reglas**.

### 3. Conectarse por SSH

**Opción A, cliente SSH** (consola: *Connect → SSH client*), desde la carpeta donde está la llave:

```bash
chmod 400 "mi-primera-llaves-ee.pem"   # solo el dueño puede leer la llave; ssh rechaza llaves con permisos abiertos [externo]
ssh -i "mi-primera-llaves-ee.pem" ec2-user@<DNS público>
```

`[externo]` `chmod` es de Linux/macOS. En Windows se usa MobaXterm (opción B).

**Opción B, MobaXterm:** Session → **SSH** → Remote host: DNS público → *Specify username*: `ec2-user` → **Advanced SSH settings** → **Use private key** → elegir el `.pem` → OK.

### 4. Instalar un servidor web Apache en Amazon Linux

```bash
sudo dnf update -y            # actualiza los paquetes
sudo dnf install httpd -y     # instala Apache
sudo systemctl start httpd    # lo arranca ahora
sudo systemctl enable httpd   # lo arranca solo en cada inicio (clave para la AMI del paso 7)
sudo systemctl status httpd   # debe decir active (running)
echo "<h1>Mi servidor web EC2 funciona</h1>" | sudo tee /var/www/html/index.html   # página de prueba (taller 2)
curl http://localhost         # prueba desde la propia instancia
```

Verificar en el navegador: `http://<DNS público>`. Debe ser **http**, no https: `[externo]` no hay regla para el puerto 443 ni certificado. Requiere la regla HTTP del paso 2.

### 5. Lanzar una EC2 con Windows (taller 3)

| Paso | Valor usado en clase |
|---|---|
| Name | `ec2-windows-ee` |
| AMI | Microsoft Windows Server 2025 Base |
| Tipo de instancia | `t3.medium` |
| Par de claves | `mi-segunda-llaves-ee` |
| Red | Permitir **RDP** `[externo]` (el asistente lo propone por defecto con una AMI de Windows) |

### 6. Conectarse por RDP

1. Instancia → **Connect** → pestaña **RDP client** → **Download remote desktop file** (`.rdp`, ya trae el DNS público y el usuario `Administrator`).
2. **Get password** → **Upload private key file** (`mi-segunda-llaves-ee.pem`) → **Decrypt password** → copiar la contraseña. `[externo]` Puede tardar unos minutos en estar disponible después del lanzamiento.
3. Abrir el `.rdp` → pegar la contraseña.
4. En el taller se instaló IntelliJ IDEA dentro del servidor.

No guardes la contraseña en tus notas: con ella cualquiera entra al servidor.

### 7. Crear una AMI personalizada (taller 4)

1. Seleccionar la instancia ya configurada (`mi-primera-instancia-ee`, con Apache).
2. **Actions → Image and templates → Create image**.
3. Nombre `mi-imagen-linux-ee`, descripción "imagen que contiene un apache web" → **Create image**.
4. Revisar en **Images → AMIs** (filtro *Owned by me*): el estado pasa de *Pending* a **Available**.

`[externo]` Por defecto la instancia se reinicia para crear la imagen. La AMI guarda snapshots de sus volúmenes EBS y pertenece a una sola región.

### 8. Lanzar una instancia desde tu AMI

1. **Launch instance** → Name `mi-segunda-linux-ee`.
2. AMI → **My AMIs** → *Owned by me* → `mi-imagen-linux-ee`.
3. Tipo `t3.medium` (taller 4), par de claves y un security group con regla HTTP → **Launch instance**.
4. Abrir `http://<DNS público de la nueva instancia>` → aparece "Mi servidor web EC2 funciona" sin instalar nada, porque Apache venía en la AMI y estaba `enable`.

Atajo equivalente: Images → AMIs → seleccionar la AMI → **Launch instance from AMI**.

## Posibles preguntas de lección o examen

1. ¿Qué es una VPC y qué relación tiene con una subred?
2. ¿Qué es un security group? Diferencia entre una regla de entrada y una de salida, y cuáles son sus valores por defecto.
3. ¿Por qué falla el `ping` a una instancia recién creada y cómo se soluciona?
4. ¿Qué significa `0.0.0.0/0` como origen de una regla?
5. ¿Qué puertos y protocolos se abren para SSH, para la web y para el escritorio remoto de Windows?
6. ¿Para qué sirven `chmod 400` y `ssh -i`? ¿Qué usuario se usa en Amazon Linux?
7. Escribe los comandos para instalar y dejar activo Apache en Amazon Linux. ¿Qué hace `systemctl enable`?
8. ¿Cómo se obtiene la contraseña de `Administrator` de una EC2 con Windows y qué papel cumple el `.pem`?
9. ¿Qué es una AMI y para qué sirve crear una AMI personalizada? Describe los pasos para crearla y lanzar una instancia desde ella.
10. ¿La IP pública de una instancia es permanente? ¿Qué usar si necesitas una fija?

### Tipo CLF-C02

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
