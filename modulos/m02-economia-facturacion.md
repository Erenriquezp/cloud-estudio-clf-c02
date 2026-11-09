# M2: Economía y facturación de la nube (Cloud Economics and Billing)

- **Syllabus:** Modelo de costos (sem 4, 8, 12, 16) · **CLF-C02:** D4
- **Fuentes:** [clase 03](../clases/2026-10-22-clase-03-user-data.md#tarea-3-aws-support) (tarea 3). Todo es `[externo]`, tomado de las páginas oficiales de AWS el 2026-10-25: [planes](https://aws.amazon.com/premiumsupport/plans/) · [precios](https://aws.amazon.com/premiumsupport/pricing/) · [guía de usuario](https://docs.aws.amazon.com/awssupport/latest/user/aws-support-plans.html).

## AWS Support

`[D4]` Servicio de AWS que ayuda con preguntas de cuenta y facturación, aumentos de cuotas de servicio y problemas técnicos. AWS lo describe como *"AI-powered support backed by AWS experts"*: soporte con IA respaldado por expertos de AWS.

- Se contrata por **planes**. Cada plan incluye todo lo del anterior.
- Los planes de pago se cobran por mes y no exigen contrato a largo plazo.
- Precio = el **mayor** entre un mínimo mensual y un porcentaje escalonado del gasto mensual en AWS.

### Planes vigentes

| Plan | Costo | Para qué | Respuesta en caso crítico | Lo que agrega |
|---|---|---|---|---|
| **Basic** | Gratis, incluido en toda cuenta | Cuenta y facturación | Sin casos técnicos | Atención 24/7 de cuenta y facturación, documentación, whitepapers, re:Post, AWS Health Dashboard, checks básicos de Trusted Advisor |
| **Business Support+** | Desde USD 29/mes por cuenta (9 % de los primeros USD 10K) | Mínimo recomendado por AWS para **producción** | < 30 min | IA contextual, ingenieros de soporte 24/7 por teléfono, web y chat, casos ilimitados, todos los checks de Trusted Advisor (más de 500), AWS Support API, ayuda con software de terceros |
| **Enterprise Support** | Desde USD 5.000/mes | Cargas **críticas para el negocio** | < 15 min | **TAM designado**, AWS Security Incident Response sin costo extra, revisiones estratégicas de negocio, AWS Countdown, especialista de facturación |
| **Unified Operations** | Desde USD 50.000/mes | Cargas **de misión crítica** | < 5 min (Incident Management Engineers) | TAM y Domain Specialist Engineer (DSE) designados, monitoreo 24/7 de cargas, guía de arquitectura, revisiones de cargas críticas y runbooks personalizados |

Tiempos de respuesta comunes a los planes de pago: sistema de producción caído < 1 h · producción afectada < 4 h · sistema afectado < 12 h · consulta general < 24 h.

### Planes que se retiran

| Plan anterior | Fin | Pasa a |
|---|---|---|
| Developer | 2027-01-01 | Business Support+ |
| Business | 2027-01-01 | Business Support+ |
| Enterprise On-Ramp | 2027-01-01 (se migra automáticamente durante 2026) | Enterprise Support |

Siguen disponibles en AWS GovCloud (US).

⚠️ La guía del CLF-C02 es anterior a este cambio. El examen puede preguntar por los planes clásicos (Basic, Developer, Business, Enterprise On-Ramp, Enterprise). Para el examen hay que recordar:

- Basic es gratis y no tiene casos técnicos.
- Developer era para pruebas y desarrollo.
- Business es el primer plan para producción, con todos los checks de Trusted Advisor.
- Enterprise es el que trae **TAM dedicado**.

### Relacionados

| Recurso | Qué es |
|---|---|
| AWS Trusted Advisor | Revisa la cuenta y recomienda mejoras en costo, rendimiento, seguridad, tolerancia a fallos y cuotas |
| AWS Health Dashboard | Estado de los servicios de AWS y eventos que afectan a tu cuenta |
| AWS re:Post | Foro comunitario de preguntas y respuestas (incluido en Basic) |
| TAM | Technical Account Manager: ingeniero de AWS asignado al cliente que da guía proactiva |
| AWS Support Center | Consola donde se abren y siguen los casos de soporte |
