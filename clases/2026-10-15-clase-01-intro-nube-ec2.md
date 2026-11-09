# Clase 01: Introducción a la nube y primera EC2

- **Fecha:** 2026-10-15
- **Syllabus:** U1 Cloud Computing: Virtual Machines · Semana 1 (Introducción)
- **Cloud Foundations:** [M1 Conceptos de nube](../modulos/m01-conceptos-nube.md) · [M6 Cómputo](../modulos/m06-computo.md)
- **Original:** [_archivo/clase-01/notas-crudas.txt](../_archivo/clase-01/notas-crudas.txt)
- **Avisos del docente:** 2 cursos de AWS Academy en paralelo:
  - **Cloud Foundations:** los módulos M1–M5 deben estar terminados, con su evaluación, **al cierre de la U2** del syllabus (antes de la sumativa 1, semana 9, 2026-12-07). Es una fecha límite, no una evaluación de esta semana. Los "2 puntos" coinciden con el [syllabus](../syllabus.md#evaluación-sobre-20): M1–M5 vale 6/20 de la formativa individual (35%), es decir 6/20 × 7 = **2,1 pts de la nota final**. El bloque M6–M10 vale otro tanto.
  - **Learner Lab:** entorno de práctica para los talleres (ver abajo).
- **Corrección a las notas:** decían que en la nube privada el proveedor aporta el hardware con *Snowball*. En AWS ese servicio es **AWS Outposts**; AWS Snowball solo sirve para transferir datos de forma física `[externo]`.

## Conceptos clave

| Concepto | Definición breve | CLF |
|---|---|---|
| Computación en la nube | Conjunto de recursos tecnológicos ubicados en otra región geográfica y aprovisionados **bajo demanda**, a través de una **red** (internet), con **pago por uso** | D1 |
| 3 características | 1. Bajo demanda · 2. Red · 3. Pago por uso | D1 |
| IaaS / PaaS / SaaS | Infraestructura / plataforma / software entregados como servicio (detalle abajo) | D1 |
| Nube pública / privada / híbrida / comunitaria | Dónde se despliega y quién accede (detalle abajo) | D3 |
| Soberanía de datos | Los datos confidenciales no pueden salir del territorio nacional; motiva la nube privada o híbrida | D3 |
| AWS Outposts | Hardware de AWS instalado en el país o la empresa del cliente para montar nube privada o híbrida | D3 |
| CAPEX → OPEX | La inversión de capital inicial (CAPEX) se convierte en gasto operativo variable (OPEX) | D1 |
| Economías de escala | El costo unitario baja cuando aumenta la producción | D1 |
| Elasticidad | Usar recursos cuando se necesitan y devolverlos cuando no; evita estimar la capacidad | D1 |
| Amazon EC2 | Elastic Compute Cloud: servicio de cómputo para crear y administrar servidores virtuales (instancias EC2); ejemplo de IaaS | D3 |
| AMI | Imagen base de la instancia (SO); en clase, Amazon Linux | D3 |
| Tipo de instancia | `familia.tamaño` → capacidad de procesamiento y memoria (ej. `t3.micro`, `t2.medium`) | D3 |
| Par de claves | Mecanismo criptográfico de autenticación para entrar al servidor; la llave privada es el `.pem` | D2 `[externo]` |
| IOPS | Operaciones de entrada/salida por segundo; mide el rendimiento del disco | D3 |

### Modelos de servicio `[D1]`

FaaS, CaaS y otros "X as a Service" siempre caben en uno de estos tres.

| Modelo | Qué se entrega | Qué administras tú | Ejemplo |
|---|---|---|---|
| IaaS | Recursos tecnológicos (servidores) como servicio | SO, plataforma, apps, datos `[externo]` | Amazon EC2 |
| PaaS | Plataforma lista; el servidor no lo administras | La plataforma (ej. la base de datos) y tus datos | Bases de datos (ej. Amazon RDS `[externo]`) |
| SaaS | Software terminado como servicio | Solo su uso y configuración | Office 365 |

### Modelos de implementación `[D3]`

| Modelo | Dónde / acceso | Clave |
|---|---|---|
| Pública | Región geográfica del proveedor, vía internet | Ej. AWS en EE. UU.; los datos salen del país |
| Privada | Dentro del país y de la empresa, vía intranet | El proveedor aporta el hardware (en AWS: **AWS Outposts**); **más cara**; resuelve la soberanía de datos |
| Híbrida | Privada + pública | Datos confidenciales en la privada, el resto en la pública |
| Comunitaria | Varias organizaciones se unen para proveer servicios | — |

`[externo]` El CLF-C02 usa la terminología **cloud / hybrid / on-premises**: privada ≈ on-premises. El modelo comunitario no aparece en la guía del examen.

### 6 ventajas de la nube `[D1]`

| # | Clase | Nombre oficial de AWS `[externo]` |
|---|---|---|
| 1 | Evitar grandes inversiones: CAPEX → OPEX | Trade fixed expense for variable expense |
| 2 | Economías de escala: el costo unitario baja al aumentar la producción | Benefit from massive economies of scale |
| 3 | Escalabilidad y elasticidad: ajustar bajo demanda sin estimar capacidad | Stop guessing capacity |
| 4 | Más velocidad y agilidad | Increase speed and agility |
| 5 | No gastar en operar y mantener centros de datos | Stop spending money running and maintaining data centers |
| 6 | Alcance mundial en minutos | Go global in minutes |

### Amazon EC2 para el examen `[externo]`

- Familias de instancias: propósito general (`t`, `m`), optimizadas para cómputo (`c`), para memoria (`r`) y para almacenamiento (`i`).
- Las reglas de tráfico (ej. SSH) se definen en un **security group** (D2/D3); se vio en la [clase 02](2026-10-20-clase-02-red-ec2-amis.md#security-group-comportamiento-externo).
- gp3 es un volumen **Amazon EBS** SSD de propósito general (M7).

## Learner Lab: uso en clase

1. *Launch AWS Academy Learner Lab* → **Start Lab**.
2. Esperar las señales: el indicador AWS pasa de amarillo a verde, Start Lab muestra que carga y aparece un mensaje emergente.
3. **AWS Details** → AWS CLI → *Show*. Aparecen 3 valores: `aws_access_key_id`, `aws_secret_access_key` y `aws_session_token` (credenciales temporales). `[externo]` Cambian cada vez que se inicia o reinicia el lab, y el lab solo permite usar las regiones us-east-1 y us-west-2.
4. Clic en el ícono verde (AWS) → abre la consola web → buscar **EC2**.
5. **End Lab** al terminar cada taller.

## Lanzar una instancia EC2 (taller)

EC2 → Instances (running) → **Launch instance**:

| Paso | Valor usado en clase / nota |
|---|---|
| Name | `mi-primera-instancia-ee`. Convención del docente: usar guiones `-`, nunca `_` (AWS sí acepta `_` `[externo]`) |
| AMI | Amazon Linux |
| Tipo de instancia | `familia.tamaño` = procesamiento + memoria (ej. `t3.micro`, `t2.medium`) |
| Par de claves | `mi-primera-llaves-ee.pem` (llave privada) |
| Configuración de red | Permitir tráfico **SSH** (puerto 22). Esto crea un security group con esa regla de entrada |
| Almacenamiento | Disco **gp3** estándar (acceso rápido). Se mide en IOPS |
| Final | **Launch instance** |

## Posibles preguntas de lección o examen

1. Define la computación en la nube y sus 3 características.
2. Diferencia entre IaaS, PaaS y SaaS, con un ejemplo de cada uno. ¿EC2 a cuál pertenece?
3. ¿Qué problema resuelve la nube privada? Relaciónalo con la soberanía de datos y con AWS Outposts.
4. ¿Qué significa que el CAPEX se transforma en OPEX?
5. Enumera las 6 ventajas de la computación en la nube.
6. ¿Qué indica la notación `t3.micro`?
7. ¿Para qué sirve el par de claves al lanzar una EC2 y qué tráfico se habilita para entrar?
8. Pasos para lanzar una instancia EC2 desde la consola. ¿Qué tipo de disco se usó?
9. ¿Qué credenciales entrega el Learner Lab para la AWS CLI y por qué hay que renovarlas?

### Tipo CLF-C02

**1. `[D1]`** Una empresa quiere dejar de comprar servidores por adelantado y pagar solo lo que consume. ¿Qué ventaja de la nube describe esto?
A) Alcance mundial en minutos · B) Cambiar gasto fijo por gasto variable · C) Economías de escala · D) Aumentar la agilidad
<details><summary>Respuesta</summary>B. CAPEX → OPEX.</details>

**2. `[D1]`** ¿Qué ventaja permite que AWS ofrezca precios más bajos porque agrega el uso de cientos de miles de clientes?
A) Dejar de adivinar la capacidad · B) Economías de escala · C) Elasticidad · D) Alta disponibilidad
<details><summary>Respuesta</summary>B. Más volumen implica menor costo unitario.</details>

**3. `[D1]`** Una tienda tiene picos de tráfico impredecibles y no quiere sobredimensionar servidores. ¿Qué ventaja aplica?
A) Dejar de adivinar la capacidad · B) Alcance mundial · C) Economías de escala · D) Soberanía de datos
<details><summary>Respuesta</summary>A. Elasticidad: escala bajo demanda.</details>

**4. `[D3]`** Una entidad pública debe mantener sus datos confidenciales dentro del país, pero quiere usar la nube pública para el resto. ¿Qué modelo de implementación le conviene?
A) Pública · B) Híbrida · C) Comunitaria · D) SaaS
<details><summary>Respuesta</summary>B. Datos sensibles on-premises o en nube privada, el resto en la pública.</details>

**5. `[D1]`** ¿Qué modelo de servicio corresponde a Amazon EC2?
A) SaaS · B) PaaS · C) IaaS · D) FaaS
<details><summary>Respuesta</summary>C. Entrega servidores virtuales; tú administras el SO y lo que va encima.</details>

**6. `[D3]`** ¿Qué determina el *tipo de instancia* de EC2 (ej. `t3.micro`)?
A) El sistema operativo · B) La región · C) La capacidad de CPU y memoria · D) El tipo de disco
<details><summary>Respuesta</summary>C. El SO lo define la AMI.</details>

**7. `[D3]`** Un banco necesita ejecutar servicios de AWS dentro de su propio centro de datos por requisitos de residencia de datos. ¿Qué servicio usa?
A) AWS Snowball · B) AWS Outposts · C) Amazon CloudFront · D) AWS Direct Connect
<details><summary>Respuesta</summary>B. Outposts lleva la infraestructura de AWS al sitio del cliente. Snowball sirve para mover datos de forma física.</details>
