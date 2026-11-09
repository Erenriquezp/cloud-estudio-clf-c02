# M5: Redes y entrega de contenido (Networking and Content Delivery)

- **Syllabus:** U1 (red de las VMs; balanceo de carga y CDN en la semana 3) · **CLF-C02:** D3 (tarea 3.5), D2
- **Fuentes:** [clase 02](../clases/2026-10-20-clase-02-red-ec2-amis.md) · [clase 05](../clases/2026-10-29-clase-05-security-groups-cli.md)

## Amazon VPC y direccionamiento

| Elemento | Qué es | CLF |
|---|---|---|
| Amazon VPC | Virtual Private Cloud: red privada virtual, una red lógica creada en la nube | D3 |
| Subred | Segmento creado a partir de la VPC. `[externo]` Vive en una sola zona de disponibilidad | D3 |
| IPv4 pública | IP para llegar a la instancia desde internet. `[externo]` Se asigna si la subred lo habilita (en la VPC por defecto sí) y cambia al detener e iniciar la instancia | D3 |
| DNS público | Nombre asociado a la IP pública (`ec2-<ip>.compute-1.amazonaws.com`) | D3 |
| Elastic IP `[externo]` | IP pública fija, no cambia al detener la instancia | D3 |

## Security groups

`[D2]` `[D3]` Firewall virtual que protege las instancias. Se configuran reglas de entrada y de salida.

| Regla | Por defecto | Ejemplos vistos |
|---|---|---|
| Entrada | Nada permitido | SSH 22, HTTP 80 `[externo]`, RDP 3389 `[externo]`, "Todos los ICMP - IPv4" (ping); origen `0.0.0.0/0` = cualquier IPv4 |
| Salida | All traffic (salida libre a internet) | — |

`[externo]`:
- Solo hay reglas de **permitir**. Es **stateful**: la respuesta a un tráfico permitido sale sin regla de salida.
- Actúa a nivel de **instancia**. El asistente de lanzamiento crea `launch-wizard-N`; cada VPC trae además un SG llamado `default`.
- Los cambios se aplican al instante, sin reiniciar la instancia.

Procedimiento en consola → [clase 02, reglas de entrada](../clases/2026-10-20-clase-02-red-ec2-amis.md#2-agregar-reglas-de-entrada-ping-y-http).

### Security groups con la AWS CLI

`[D3]` Flujo del taller 7 ([clase 05](../clases/2026-10-29-clase-05-security-groups-cli.md#procedimientos-de-lab)): VPC por defecto → crear SG → reglas → lanzar instancia con ese SG.

| Comando | Para qué |
|---|---|
| `aws ec2 describe-vpcs --filters "Name=isDefault,Values=true" --query "Vpcs[0].VpcId" --output text` | ID de la VPC por defecto |
| `aws ec2 create-security-group --group-name … --description … --vpc-id … --query 'GroupId' --output text` | Crear el SG; devuelve `sg-…` |
| `aws ec2 authorize-security-group-ingress --group-id … --protocol tcp --port … --cidr …` | Regla de entrada |
| `aws ec2 authorize-security-group-egress --group-id … --protocol tcp --port … --cidr …` | Regla de salida |
| `aws ec2 run-instances … --security-group-ids sg-…` | Instancia con ese SG |

CIDR: `0.0.0.0/0` = cualquier IPv4 · `/32` = una IP · `/24` = 256 direcciones.

`[externo]`:
- `authorize-…-egress` **suma** una regla; la salida *All traffic* por defecto sigue activa hasta revocarla (`revoke-security-group-egress`).
- `192.168.1.0/24` es un rango privado, solo de ejemplo.
- SSH desde `0.0.0.0/0` sirve en el lab; en producción se limita a la IP propia (`/32`).
