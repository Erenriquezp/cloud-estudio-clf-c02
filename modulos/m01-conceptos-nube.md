# M1: Conceptos de nube (Cloud Concepts Overview)

- **Syllabus:** U1 · **CLF-C02:** D1 (principalmente), D3
- **Fuentes:** [clase 01](../clases/2026-10-15-clase-01-intro-nube-ec2.md) · [clase 04](../clases/2026-10-27-clase-04-aws-cli.md)

## Definición

Nube = conjunto de recursos tecnológicos ubicados en otra región geográfica, aprovisionados **bajo demanda**, a través de una **red** (internet), con **pago por uso**. `[D1]`

## Modelos de servicio

`[D1]` FaaS, CaaS y otros "X as a Service" siempre caben en uno de estos tres.

| Modelo | Qué se entrega | Qué administras tú | Ejemplo |
|---|---|---|---|
| IaaS | Recursos tecnológicos (servidores) como servicio | SO, plataforma, apps, datos `[externo]` | Amazon EC2 |
| PaaS | Plataforma lista; el servidor no lo administras | La plataforma (ej. la base de datos) y tus datos | Bases de datos (ej. Amazon RDS `[externo]`) |
| SaaS | Software terminado como servicio | Solo su uso y configuración | Office 365 |

## Modelos de implementación

`[D3]` (tarea 3.1)

| Modelo | Dónde / acceso | Clave |
|---|---|---|
| Pública | Región geográfica del proveedor, vía internet | Ej. AWS en EE. UU.; los datos salen del país |
| Privada | Dentro del país y de la empresa, vía intranet | El proveedor aporta el hardware (en AWS: **AWS Outposts**); **más cara**; resuelve la soberanía de datos |
| Híbrida | Privada + pública | Datos confidenciales en la privada, el resto en la pública |
| Comunitaria | Varias organizaciones se unen para proveer servicios | — |

- **Soberanía de datos:** las políticas prohíben que los datos confidenciales salgan del territorio nacional. `[D3]`
- `[externo]` El CLF-C02 usa la terminología **cloud / hybrid / on-premises**: privada ≈ on-premises. El modelo comunitario no aparece en la guía del examen.

## 6 ventajas de la nube

`[D1]` (tareas 1.1 y 1.4)

| # | Clase | Nombre oficial de AWS `[externo]` |
|---|---|---|
| 1 | Evitar grandes inversiones: CAPEX → OPEX | Trade fixed expense for variable expense |
| 2 | Economías de escala: el costo unitario baja al aumentar la producción | Benefit from massive economies of scale |
| 3 | Escalabilidad y elasticidad: ajustar bajo demanda sin estimar capacidad | Stop guessing capacity |
| 4 | Más velocidad y agilidad | Increase speed and agility |
| 5 | No gastar en operar y mantener centros de datos | Stop spending money running and maintaining data centers |
| 6 | Alcance mundial en minutos | Go global in minutes |

## Categorías de servicios AWS

- Redes → [M5](m05-redes.md) (Amazon VPC, security groups).
- Cómputo → [M6](m06-computo.md) (Amazon EC2, AMIs).
- Las demás categorías se agregarán a medida que se vean.

## Formas de interactuar con AWS

`[D3]` (tarea 3.1 de la guía `[externo]`: elegir entre consola, acceso programático e infraestructura como código).

| Forma | Qué es | Credenciales |
|---|---|---|
| Consola web (AWS Management Console) | Interfaz gráfica en el navegador | Usuario y contraseña |
| AWS CLI | Comandos en la terminal (`aws <servicio> <acción>`); permite automatizar con scripts | Access key ID + clave secreta (+ session token si son temporales) `[D2]` |
| AWS SDK | Librerías para usar AWS desde el código de una aplicación | Las mismas que la CLI `[externo]` |

CLI y SDK son **acceso programático** `[externo]`. Configuración de la CLI y comandos de EC2 → [M6](m06-computo.md#amazon-ec2-con-la-aws-cli) · [clase 04](../clases/2026-10-27-clase-04-aws-cli.md).

## Infraestructura como código (IaC)

`[D3]` Definir, crear, actualizar o eliminar infraestructura mediante archivos de configuración. Beneficios: automatización y repetibilidad; paradigma **declarativo** (se declara el estado objetivo).

- **Terraform** (código abierto, de terceros `[externo]`): archivos `.tf`; flujo `init → plan → apply → destroy`. Instalación → [clase 05](../clases/2026-10-29-clase-05-security-groups-cli.md#5-instalar-terraform-en-windows). Uso con EC2 → [M6](m06-computo.md#terraform-instancia-ec2-y-security-group) · [clase 06](../clases/2026-11-05-clase-06-terraform.md).
- Servicio de AWS equivalente: AWS CloudFormation `[externo]`.
- Es la tercera forma de interactuar con AWS junto a consola y CLI/SDK (tarea 3.1 de la guía `[externo]`).
