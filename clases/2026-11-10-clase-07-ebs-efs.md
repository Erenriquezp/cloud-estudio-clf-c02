# Clase 07: Almacenamiento persistente con Amazon EBS y Amazon EFS

- **Fecha:** 2026-11-10 (confirmada por el usuario)
- **Syllabus:** U1 Cloud Computing: Virtual Machines. El tema corresponde a la semana 2 del syllabus (*almacenamiento persistente*, práctica "crear y configurar disco persistente"), aunque la fecha cae en la semana 5 (U2 Cloud SQL). Continúa la [clase 06](2026-11-05-clase-06-terraform.md).
- **Cloud Foundations:** [M7 Almacenamiento](../modulos/m07-almacenamiento.md) (EBS, EFS, bloque vs. archivo) · [M6 Cómputo](../modulos/m06-computo.md)

## Conceptos clave

| Concepto | Definición breve | CLF |
|---|---|---|
| Almacenamiento persistente | Los datos **perduran en el tiempo**, aunque la instancia se detenga | D3 |
| Almacenamiento no persistente | **Volátil**: existe mientras un proceso está en ejecución (ej. caché). En AWS, el *instance store* `[externo]` | D3 |
| Amazon EBS (Elastic Block Store) | Almacenamiento **persistente por bloques** para EC2. Teoría: [M7](../modulos/m07-almacenamiento.md#amazon-ebs-elastic-block-store) | D3 |
| `gp3` | EBS **SSD de propósito general** (*general purpose*), de tercera generación: lectura y escritura equilibradas | D3 |
| `io1` / `io2` | EBS **Provisioned IOPS**: alto rendimiento, para **bases de datos** | D3 |
| Amazon EFS (Elastic File System) | Servicio **administrado** de **archivos**: un sistema de almacenamiento **en red** que varias instancias usan a la vez. Teoría: [M7](../modulos/m07-almacenamiento.md#amazon-efs-elastic-file-system) | D3 |
| NFS (Network File System) | Protocolo que permite acceder a archivos remotos **como si fueran locales**. EFS lo usa por **TCP 2049** (diagrama de la clase) | D3 |
| Destino de montaje (*mount target*) `[externo]` | Punto de acceso de red de EFS **dentro de una zona de disponibilidad**; las instancias de esa AZ montan el sistema a través de él. En las notas aparece como "puntos de acceso asociados a la zona de disponibilidad" | D3 |
| Requisito de red de EFS | Debe estar en la **misma VPC** que las instancias | D3 |

### EBS vs. EFS (lo visto en clase)

| | Amazon EBS | Amazon EFS |
|---|---|---|
| Tipo | Bloque: un **disco** que se formatea y monta | Archivo: una **carpeta de red** compartida |
| Quién lo usa | Una instancia | **Muchas** instancias a la vez, incluso en distintas AZ |
| Hay que formatearlo | Sí (`mkfs`) | No: ya es un sistema de archivos |
| Protocolo | Dispositivo de bloque (`/dev/sdb`) | NFS (TCP 2049) |
| Taller | 9 | 10 |

![Diagrama de EFS: EC2-1 en la AZ A y EC2-2 en la AZ B montan /efs por NFS (TCP 2049) sobre un Amazon EFS persistente](../_archivo/clase-07/diagrama-efs.png)

## Procedimientos de lab

### 1. Taller 9: agregar un disco EBS a una instancia Linux

Instancia `instancia-ebs-ee` (Amazon Linux 2023, `t3.micro`). Se le agregó un volumen EBS **de 10 GiB** (dispositivo `/dev/sdb`); el disco raíz (`/dev/xvda`) es de 20 GiB. Ambos aparecen en *Almacenamiento* → *Dispositivos de bloques* de la instancia.

| Paso | Comando | Qué hace |
|---|---|---|
| 1 | `df -hT` · `lsblk -f` | Listar los discos y sistemas de archivos montados. El disco nuevo aparece **sin** sistema de archivos |
| 2 | `sudo mkdir -p /disco2` | Crear el **punto de montaje** |
| 3 | `sudo mkfs -t xfs /dev/sdb` | **Formatear** el disco con el sistema de archivos XFS |
| 4 | `sudo blkid /dev/sdb` | Mostrar el UUID y el tipo (`TYPE="xfs"`): confirma que el formato quedó |
| 5 | `sudo mount /dev/sdb /disco2` | **Montar** el disco en `/disco2` |
| 6 | `echo "test test" \| sudo tee /disco2/prueba.txt` | Escribir un archivo de prueba |
| 7 | `cat /disco2/prueba.txt` · `lsblk -f` | Leer el archivo y comprobar el montaje |

**Error visto en la captura:** al intentar `mount` **antes** de `mkfs`, sale `wrong fs type, bad superblock on /dev/sdb, missing codepage or helper program, or other error`. Significa que el disco está **en crudo, sin formato**: hay que crear el sistema de archivos primero. El orden correcto es crear carpeta → formatear → montar.

Resultado en `lsblk -f`: el disco nuevo (`nvme1n1` `[externo]`) con `xfs`, 9.9 GiB, montado en `/disco2`.

### 2. Taller 10: Amazon EFS compartido entre dos instancias

Lo que muestran las capturas:
1. **Sistema de archivos EFS** → pestaña **Red**: un destino de montaje por zona de disponibilidad (`us-east-1a`, `b`, `c`, `d`, `f`), todos en la misma VPC y con el security group `default`.
2. Dos instancias `t3.medium`: `instancia-efs-ee1` e `instancia-efs-ee2`.

Las notas no traen los comandos de montaje. Procedimiento habitual `[externo]` (verificar contra la consola, botón *Conectar* del sistema de archivos):

```bash
sudo dnf install -y amazon-efs-utils        # cliente de EFS (Amazon Linux 2023)
sudo mkdir /efs                             # punto de montaje (nombre del diagrama)
sudo mount -t efs -o tls fs-XXXXXXXX:/ /efs # montar el EFS
echo "hola" | sudo tee /efs/prueba.txt      # escribir en una instancia…
cat /efs/prueba.txt                         # …y leer en la otra
```

`[externo]` El security group de los destinos de montaje debe **permitir TCP 2049 (NFS) de entrada** desde las instancias; si no, el montaje se queda colgado.

### 3. Tarea 6: Almacenamiento persistente con EBS, replicar el taller 9 en Windows

Enunciado: *"TAREA 6: Almacenamiento persistente con EBS. Replicar el TALLER 9 en Windows"*. Equivalente en Windows Server `[externo]`, sin comandos Linux:

1. Lanzar una EC2 Windows ([clase 02](2026-10-20-clase-02-red-ec2-amis.md#5-lanzar-una-ec2-con-windows-taller-3)) y **agregar un volumen EBS** (en la misma AZ) al crear la instancia o después desde *Volúmenes* → *Asociar volumen*.
2. Conectarse por RDP ([paso 6 de la clase 02](2026-10-20-clase-02-red-ec2-amis.md#6-conectarse-por-rdp)).
3. **Administración de discos** (*Disk Management*, `diskmgmt.msc`): el disco nuevo aparece **sin conexión / sin inicializar**.
4. Ponerlo en línea → **Inicializar disco** (GPT) → **Nuevo volumen simple** → formato **NTFS** → asignar letra (ej. `D:`).
5. Crear un archivo de prueba en `D:` y capturar pantalla (disco en Administración de discos + archivo + volumen en la consola).

Equivalencias: `mkfs -t xfs` → formatear NTFS · `mount /dev/sdb /disco2` → asignar letra `D:` · `lsblk -f` → Administración de discos.

## Aclaraciones `[externo]`

| Tema | Aclaración |
|---|---|
| "Persistente" no es "montado" | Los **datos** del volumen sobreviven, pero el **montaje** del `mount` se pierde al reiniciar. Para que se monte solo hay que agregarlo a `/etc/fstab` (mejor por UUID, el que da `blkid`) |
| Instancia vs. volumen | Al **terminar** la instancia se borra su volumen raíz (por defecto); el volumen extra solo si tiene marcado *Eliminar al terminar*. En el taller 9 se terminó la instancia: revisar en *Volúmenes* que no quede el de 10 GiB |
| Nombre del dispositivo | La consola dice `/dev/sdb`, pero en instancias Nitro (como `t3`) el SO lo ve como `/dev/nvme1n1`; Amazon Linux crea el alias `/dev/sdb` |
| EFS y Windows | EFS es para Linux (NFS); para Windows existe Amazon FSx. Por eso la tarea usa EBS |
| Costos `[D4]` | EBS se cobra por **GB aprovisionado** aunque no se use; EFS, por **GB realmente usado**. Borrar volúmenes y el sistema de archivos al terminar el lab |

## Posibles preguntas de lección o examen

1. ¿Qué diferencia hay entre almacenamiento persistente y no persistente? Un ejemplo de cada uno.
2. ¿Para qué sirven `gp3` e `io1`/`io2`?
3. Ordena: `mount`, `mkdir -p`, `mkfs`. ¿Por qué falla `mount` si el disco no se formateó?
4. ¿Qué hacen `df -hT`, `lsblk -f` y `blkid`?
5. ¿Qué es EFS, qué protocolo usa y por qué puerto?
6. ¿Qué requisito de red tiene EFS respecto a las instancias?
7. ¿Qué es un destino de montaje y cuántos hay por AZ?
8. ¿Cuándo conviene EBS y cuándo EFS?

Preguntas tipo CLF-C02 → [banco, clase 07](../repaso/banco-preguntas.md#clase-07).