# M0: Introducción al curso AWS Academy Cloud Foundations [194584]

- **CLF-C02:** apoyo (qué se evalúa, cómo prepararse) · tendencias: D3 `[externo]`
- **Fuentes:** material del curso (Introducción, Tendencias en la computación en la nube, ¿Cómo completar los ejercicios de laboratorio?) · [guía CLF-C01 v1.6](../_archivo/curso/guia-examen-clf-c01-v1-6-obsoleta.pdf) (obsoleta, ver abajo)

## Objetivos del curso

Al terminar se puede:
- Definir la nube de AWS y explicar su filosofía de precios.
- Identificar los componentes de la infraestructura global.
- Describir seguridad y cumplimiento, incluido AWS IAM.
- Crear una Amazon VPC.
- Decidir cuándo usar Amazon EC2, AWS Lambda y AWS Elastic Beanstalk.
- Diferenciar Amazon S3, Amazon EBS, Amazon EFS y Amazon S3 Glacier.
- Decidir cuándo usar Amazon RDS, Amazon DynamoDB, Amazon Redshift y Amazon Aurora.
- Explicar principios básicos de arquitectura en la nube.
- Explorar Elastic Load Balancing, Amazon CloudWatch y Auto Scaling.

**Requisitos:** conocimientos de TI y de empresas de TI. Deseable: conceptos de nube, sistemas distribuidos, redes y arquitecturas de varios niveles.

## Esquema del curso

| Módulo | Tema | Secciones | Archivo |
|---|---|---|---|
| 1 | Conceptos de la nube | Introducción a la computación en la nube · Ventajas · Introducción a AWS · AWS Cloud Adoption Framework (AWS CAF) | [M1](m01-conceptos-nube.md) |
| 2 | Economía y facturación | Aspectos básicos de precios · Costo total de propiedad · AWS Organizations · Administración de facturación y costos · Soporte técnico | [M2](m02-economia-facturacion.md) |
| 3 | Infraestructura global | Regiones, zonas de disponibilidad, red, puntos de presencia · Categorías de servicios (cierra con actividad de clic en la consola) | pendiente |
| 4 | Seguridad | Responsabilidad compartida · AWS IAM · Proteger una cuenta nueva · Proteger cuentas · Proteger datos · Conformidad | pendiente |
| 5 | Redes y entrega de contenido | Conceptos de redes · Amazon VPC · VPC networking · Seguridad de VPC · Amazon Route 53 · Amazon CloudFront | [M5](m05-redes.md) |
| 6 | Cómputo | Información general · Amazon EC2 · Optimización de costos de EC2 · Contenedores · AWS Lambda · AWS Elastic Beanstalk | [M6](m06-computo.md) |
| 7 | Almacenamiento | Amazon EBS · Amazon S3 · Amazon EFS · Amazon S3 Glacier | [M7](m07-almacenamiento.md) |
| 8 | Bases de datos | Amazon RDS · Amazon DynamoDB · Amazon Redshift · Amazon Aurora | pendiente |
| 9 | Arquitectura en la nube | AWS Well-Architected Framework · Fiabilidad y disponibilidad · AWS Trusted Advisor | pendiente |
| 10 | Escalado automático y supervisión | Elastic Load Balancing · Amazon CloudWatch · Amazon EC2 Auto Scaling | pendiente |

## Tendencias: IA generativa

`[D3]` `[externo]`

| Servicio | Qué es |
|---|---|
| Amazon Bedrock | Servicio **completamente administrado** que ofrece modelos fundacionales (foundation models, FM) de alto rendimiento y las capacidades para crear aplicaciones de IA generativa, con seguridad, privacidad e IA responsable |
| Amazon Q | Asistente de trabajo con IA generativa, seguro y privado por defecto; integrado en muchos productos de AWS |
| Amazon Q Business | Responde preguntas, resume, genera contenido y completa tareas, adaptado a los **datos y sistemas de la empresa** |
| Amazon Q Developer | Asistente de **codificación**: chatea sobre código, lo completa en línea, genera código nuevo, analiza vulnerabilidades, optimiza, depura y actualiza lenguajes; se le describe la aplicación en lenguaje natural |

Para mantenerse al día: [Novedades de AWS](https://aws.amazon.com/new) · [AWS Architecture Blog](https://aws.amazon.com/blogs/architecture/) · [AWS Training and Certification Blog](https://aws.amazon.com/blogs/training-and-certification/) · cursos de AWS Skill Builder (requieren cuenta).

## Certificaciones AWS

| Nivel | Experiencia requerida | Ejemplos |
|---|---|---|
| Foundational | 6 meses de conocimiento básico de AWS y del sector | **Cloud Practitioner** (el único fundamental) |
| Associate | 1 año resolviendo problemas e implementando soluciones | Solutions Architect, SysOps Administrator, Developer |
| Professional | 2 años diseñando, operando y resolviendo problemas | Solutions Architect, DevOps Engineer |
| Specialty | Experiencia técnica en el dominio específico | Security, Machine Learning, Advanced Networking… |

- Se obtiene aprobando un **examen supervisado**; luego se reciben las credenciales.
- AWS no publica la lista de servicios evaluados; las **guías del examen** traen los objetivos y temas.
- Renovación (recertificación) cada **3 años** (dato de una diapositiva de 2019; verificar `[externo]`).
- Cloud Foundations cubre gran parte de *Cloud Practitioner Essentials*, con más profundidad; también se parece a AWS Technical Essentials y AWS Business Essentials. No figura como capacitación recomendada en la guía, pero prepara bien.
- Los servicios evaluados cambian: como mínimo, saber describir la funcionalidad general de una amplia gama de servicios.

### ⚠️ La guía del inbox es de la versión anterior (CLF-C01)

El PDF es la guía **CLF-C01 v1.6**; el examen objetivo es **CLF-C02**. Para dominios, pesos y servicios en alcance vale la [guía oficial CLF-C02](https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02.html) (resumen en el [syllabus](../syllabus.md#plan-de-certificación-clf-c02)). La tabla de pesos del PDF se extrae con las etiquetas desordenadas, así que no se usa.

Del PDF se rescata solo lo general:
- Tipos de pregunta: opción única (1 correcta, 3 distractores) y respuesta múltiple (2 o más correctas de 5 o más opciones).
- Sin penalización por adivinar; las no respondidas cuentan como incorrectas.
- Puntaje escalado de 100 a 1000, aprobación **700**; modelo compensatorio (no hay que aprobar cada dominio).
- Puede haber preguntas sin puntuar, no identificadas.

## Documentación de AWS

- [docs.aws.amazon.com](https://docs.aws.amazon.com/): guías de usuario y de desarrollador, referencias de API, tutoriales; filtrables por producto, categoría o sector. Cada SDK y herramienta (AWS CLI, boto3) tiene la suya.
- [Whitepapers](https://aws.amazon.com/whitepapers/) recomendados para Cloud Practitioner:

| Whitepaper | Tema |
|---|---|
| Overview of Amazon Web Services | Visión general de AWS |
| Architecting for the Cloud: AWS Best Practices | Buenas prácticas de arquitectura |
| How AWS Pricing Works | Precios |
| The Total Cost of (Non) Ownership of Web Applications in the Cloud | TCO de aplicaciones web |

- También: casos prácticos, glosario de AWS de la A a la Z, preguntas frecuentes, información de capacitación y certificación. Preguntas de muestra y ruta recomendada: página oficial de Cloud Practitioner.

## Laboratorios del curso (AWS Academy)

| Paso | Cómo |
|---|---|
| Iniciar | **Start Lab** → crea una cuenta temporal de AWS y arranca el cronómetro. Esperar `Lab status: ready` y cerrar el diálogo |
| Abrir la consola | Botón **AWS** → pestaña nueva con la sesión ya iniciada en la cuenta temporal |
| Datos del lab | **Details → Show**: tiempo restante, IPs y nombres que pida el ejercicio, credenciales SSH y de la CLI |
| Más tiempo | Al llegar a cero se **terminan todos los servicios** y se desactiva la cuenta. **Start Lab** otra vez reinicia el cronómetro (las veces que haga falta) |
| Terminar | **End Lab**: finaliza los servicios; puede tardar ~5 min y hasta entonces no se puede reiniciar |

Límites:
- Algunos servicios no necesarios están **deshabilitados** (da error de permisos).
- Cada laboratorio tiene un **límite de crédito** (al menos el doble de lo que requiere el ejercicio). Si se agota, la cuenta se deshabilita y no se puede reiniciar ese lab (no afecta a otros).
- No se puede ver el saldo de crédito. Los labs se repiten mientras quede crédito.
- Relacionado: [credenciales temporales y session token](../clases/2026-10-27-clase-04-aws-cli.md#conceptos-clave).
