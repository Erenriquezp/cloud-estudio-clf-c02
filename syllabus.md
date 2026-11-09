# Syllabus: Computación Grid y Cloud (TGP10BFT03)

UCE · FICA · Computación · Décimo semestre · 2026-2027 · Presencial
Prerrequisito: Programación Concurrente y Paralela (TGP09BFT04)
Horas: 64 con docente + 32 práctico-experimental + 64 autónomo = 160
Original: [_archivo/curso/syllabus-tgp10bft03.pdf](_archivo/curso/syllabus-tgp10bft03.pdf) · Rúbrica: [_archivo/curso/rubrica-evaluacion.png](_archivo/curso/rubrica-evaluacion.png)

**Eje del curso:** las clases siguen este syllabus. AWS Academy (Cloud Foundations + Learner Lab) es complementario y se evalúa dentro de la nota individual.

## Calendario

Inicio: lunes 2026-10-12. Hay un **receso justo después de la sumativa 1** (⚠️ fechas por confirmar). Las fechas de las semanas 10–16 suponen que no hay receso y se correrán según lo que dure.

| Sem | Semana del | Unidad | Contenido | Práctica | Equivalente AWS `[externo]` |
|---|---|---|---|---|---|
| 1 | 2026-10-12 | U1 VMs | Introducción | Servicios básicos en consola | Consola, EC2 |
| 2 | 2026-10-19 | U1 VMs | Almacenamiento persistente, grupos de instancias, recursos dinámicos | Crear y configurar disco persistente | EBS, Auto Scaling groups |
| 3 | 2026-10-26 | U1 VMs | Balanceo de carga, Cloud CDN | Regla de reenvío para balanceo | ELB, CloudFront |
| 4 | 2026-11-02 | U1 VMs | Modelo de costos | Costo estimado de una VM | Precios EC2, Pricing Calculator |
| 5 | 2026-11-09 | U2 Cloud SQL | Introducción | Desplegar BD relacional | RDS |
| 6 | 2026-11-16 | U2 Cloud SQL | BD en la nube, escalado | Cambiar tipo de instancia de la BD | RDS (escalado vertical) |
| 7 | 2026-11-23 | U2 Cloud SQL | Replicación, backups | Backup manual y restauración | RDS Multi-AZ, réplicas de lectura, snapshots |
| 8 | 2026-11-30 | U2 Cloud SQL | Modelo de costos | Costo mensual de almacenamiento y consultas | Precios RDS |
| 9 | 2026-12-07 | U3 Clúster | Introducción, contenedores | Correr contenedor desde imagen pública | ECS, ECR |
| **9** | **2026-12-07** | — | **SUMATIVA FINAL 1** (examen práctico, U1–U2) | | |
| — | ⚠️ por confirmar | — | **RECESO** | | |
| 10 | 2026-12-14 | U3 Clúster | Docker, Kubernetes | Desplegar contenedor con archivo de config | EKS |
| 11 | 2026-12-21 | U3 Clúster | Administración del clúster | Inspeccionar nodos y pods | EKS, kubectl |
| 12 | 2026-12-28 | U3 Clúster | Modelo de costos | Costo de un clúster pequeño | Precios ECS/EKS/Fargate |
| 13 | 2027-01-04 | U4 Object Storage | Introducción | Crear bucket y subir archivos | S3 |
| 14 | 2027-01-11 | U4 Object Storage | No estructurado, storage classes | Asignar storage classes | Clases de almacenamiento S3 |
| 15 | 2027-01-18 | U4 Object Storage | Control de acceso, versionado | Permisos público/privado en un objeto | Políticas de bucket, IAM, versionado S3 |
| 16 | 2027-01-25 | U4 Object Storage | Modelo de costos | Costo de almacenamiento y transferencia | Precios S3 |
| **16** | **2027-01-25** | — | **SUMATIVA FINAL 2** (examen práctico, **todo el semestre** U1–U4) | | |

Resultados de aprendizaje: construir apps con la nube como infraestructura; usar almacenamiento estructurado y no estructurado; elegir tecnología según el problema; trabajo en grupo; IA como apoyo al aprendizaje autónomo.

## Evaluación (sobre 20)

| Componente | % | Pts finales | Desglose interno (sobre 20) |
|---|---|---|---|
| Formativa individual | 35% | 7 | AWS Academy M1–5 (6) + M6–10 (6) = 12 · Tareas (3) + Lecciones (3) + Talleres de clase (2) = 8 |
| Formativa grupal | 25% | 5 | Proyecto final en grupo (20) |
| Sumativa final 1 | 10% | 2 | Examen práctico (20), semana 9, al terminar U2 |
| Sumativa final 2 | 30% | 6 | Examen práctico (20) sobre todo el semestre |
| **Total** | 100% | 20 | Examen de recuperación práctico (20) |

- Las evaluaciones de AWS Academy pesan 12/20 de la nota individual, es decir **4,2 pts de la nota final**.
- La rúbrica de la captura aplica a las evaluaciones y exámenes.

## Exoneración por CLF-C02

Si apruebas **AWS Certified Cloud Practitioner (CLF-C02)**, quedas exonerado de:
- Sumativa final 2 (30%)
- Proyecto grupal (25%)
- **Total exonerado: 55% de la nota.** Siguen vigentes la formativa individual (35%) y la sumativa 1 (10%).
- **Plazo:** la certificación debe estar aprobada **un mes antes de que termine el semestre**. Sin receso, el semestre terminaría el 2027-01-29 y el plazo caería hacia el **2026-12-29**. El receso lo corre hacia más tarde. ⚠️ Recalcular cuando se conozcan sus fechas.

## Plan de certificación CLF-C02

**Examen** (guía oficial, verificada el 2026-10-16): 65 preguntas (50 puntuadas + 15 sin puntuar), opción única y respuesta múltiple, escala 100–1000, **aprobación 700**, modelo compensatorio (no hace falta aprobar cada dominio). Fuera de alcance: código, diseño de arquitectura, troubleshooting.

| Dominio | Peso | Módulos de Cloud Foundations `[externo]` | Unidad del syllabus |
|---|---|---|---|
| D1 Conceptos de nube | 24% | M1, M2, M9 | U1 |
| D2 Seguridad y cumplimiento | 30% | M4 | U4 (control de acceso) |
| D3 Tecnología y servicios | 34% | M3, M5, M6, M7, M8, M10 | U1–U4 |
| D4 Facturación, precios y soporte | 12% | M2 | Modelo de costos (sem 4, 8, 12, 16) |

Módulos de Cloud Foundations `[externo]`: M1 Cloud Concepts Overview · M2 Cloud Economics and Billing · M3 AWS Global Infrastructure Overview · M4 AWS Cloud Security · M5 Networking and Content Delivery · M6 Compute · M7 Storage · M8 Databases · M9 Cloud Architecture · M10 Auto Scaling and Monitoring.

**Hitos propuestos**

Se planifica con el plazo conservador (2026-12-29, sin receso).

| Hasta | Meta |
|---|---|
| 2026-11-13 | Cloud Foundations M1–M5 terminado (D1, D2, D4: 66% del examen) |
| 2026-12-04 | M6–M10 terminado (D3), antes de la sumativa 1 |
| 2026-12-18 | Banco de preguntas repasado y exámenes de práctica con ≥80% |
| Semana del 2026-12-21 | **Rendir el CLF-C02**, con margen antes del plazo |

Debilidad conocida: el syllabus casi no toca **D2 Seguridad (30%)** ni **D4 Soporte**. Hay que cubrirlos con M2 y M4 de Cloud Foundations.

## Bibliografía

| Tipo | Obra | Fuente |
|---|---|---|
| Básica | Mishra, A. *Microsoft Azure for Java Developers*. Apress, 2022 | https://link.springer.com/book/10.1007/978-1-4842-8251-9 |
| Complementaria | Geewax, J. J. *Google Cloud Platform in Action*. Manning, 2018 | — |
| Complementaria | Foulds, I. *Learn Azure in a Month of Lunches*. Manning, 2020 | — |

La bibliografía es de Azure/GCP. Para la certificación, la fuente principal es la [guía oficial del CLF-C02](https://docs.aws.amazon.com/aws-certification/latest/cloud-practitioner-02/cloud-practitioner-02.html) junto con Cloud Foundations.
