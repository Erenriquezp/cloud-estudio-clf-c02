# M9: Arquitectura en la nube (Cloud Architecture)

- **CLF-C02:** D1 (tarea 1.2: entender y diferenciar los pilares)
- **Fuentes:** Tarea 5. Todo este módulo es `[externo]`: sale de la [documentación oficial del AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html) (versión publicada el 2024-11-06, consultada el 2026-11-09), no de material de clase.

## AWS Well-Architected Framework

`[D1]` Guía de AWS para entender los pros y contras de las decisiones al construir sistemas en AWS. Reúne buenas prácticas para diseñar y operar cargas de trabajo seguras, fiables, eficientes, rentables y sostenibles.

- Usa **preguntas fundamentales** para medir una arquitectura contra las buenas prácticas y hallar qué mejorar.
- La revisión es una **conversación constructiva, no una auditoría**.
- **AWS Well-Architected Tool:** servicio sin costo, en la consola, para revisar cargas de trabajo, detectar riesgos altos y registrar mejoras.
- **Lentes (Lenses):** amplían la guía a industrias y tecnologías concretas (ej.: servicios financieros, IA agéntica).

### Principios generales de diseño

| Principio | Idea |
|---|---|
| Dejar de adivinar la capacidad (*Stop guessing your capacity needs*) | Usar la capacidad necesaria y escalar automáticamente |
| Probar a escala de producción (*Test systems at production scale*) | Crear el entorno de prueba, usarlo, eliminarlo y pagar solo mientras corre |
| Automatizar pensando en experimentar (*Automate with architectural experimentation in mind*) | Replicar a bajo costo, auditar cambios y revertirlos |
| Arquitecturas evolutivas (*Consider evolutionary architectures*) | El sistema cambia con el negocio; automatizar y probar reduce el riesgo |
| Decidir con datos (*Drive architectures using data*) | Medir cómo afecta cada decisión a la carga de trabajo |
| Mejorar con *game days* (*Improve through game days*) | Simular eventos de producción de forma periódica |

## Los seis pilares

| Pilar | Definición | Áreas de buenas prácticas |
|---|---|---|
| **Excelencia operativa** (*Operational excellence*) | Construir software correctamente y entregar siempre una gran experiencia al cliente: organizar equipos, diseñar, operar a escala y evolucionar | Organización · Preparar · Operar · Evolucionar |
| **Seguridad** (*Security*) | Proteger datos, sistemas y activos aprovechando la tecnología de la nube | Fundamentos de seguridad · Gestión de identidad y acceso · Detección · Protección de infraestructura · Protección de datos · Respuesta a incidentes · Seguridad de aplicaciones |
| **Fiabilidad** (*Reliability*) | Que la carga de trabajo cumpla su función correcta y consistentemente cuando se espera, durante todo su ciclo de vida | Fundamentos · Arquitectura de la carga de trabajo · Gestión de cambios · Gestión de fallos |
| **Eficiencia del rendimiento** (*Performance efficiency*) | Usar los recursos eficientemente para cumplir los requisitos de rendimiento, y mantener esa eficiencia cuando cambian la demanda y la tecnología | Selección de arquitectura · Cómputo y hardware · Gestión de datos · Redes y entrega de contenido · Procesos y cultura |
| **Optimización de costos** (*Cost optimization*) | Ejecutar sistemas que entreguen valor de negocio al menor precio | Gestión financiera en la nube · Conciencia de gastos y uso · Recursos rentables · Gestionar demanda y oferta · Optimizar con el tiempo |
| **Sostenibilidad** (*Sustainability*) | Reducir el impacto ambiental, sobre todo consumo y eficiencia energética, maximizando el beneficio de los recursos y minimizando el total requerido | Selección de región · Alineación con la demanda · Software y arquitectura · Datos · Hardware y servicios · Procesos y cultura |

## Principios de diseño por pilar

### 1. Excelencia operativa (8)

| Principio | Idea |
|---|---|
| Organizar equipos según resultados de negocio | Liderazgo, modelo operativo y KPI alineados |
| Implementar observabilidad para obtener información accionable | Telemetría y KPI para decidir y actuar a tiempo |
| Automatizar de forma segura donde sea posible | Operaciones como código, con límites y aprobaciones |
| Hacer cambios frecuentes, pequeños y reversibles | Menor radio de impacto y reversión más rápida |
| Refinar los procedimientos con frecuencia | Revisarlos y validar que el equipo los conoce |
| Anticipar los fallos | Simular fallos y probar la respuesta |
| Aprender de todos los eventos y métricas | Compartir lecciones en toda la organización |
| Usar servicios administrados | Reducir la carga operativa |

### 2. Seguridad (7)

| Principio | Idea |
|---|---|
| Implementar una base sólida de identidad | Mínimo privilegio, separación de funciones, identidad centralizada, sin credenciales estáticas de largo plazo |
| Mantener la trazabilidad | Monitorear, alertar y auditar acciones en tiempo real |
| Aplicar seguridad en todas las capas | Defensa en profundidad: borde, VPC, balanceo, instancia, SO, aplicación y código |
| Automatizar las buenas prácticas de seguridad | Controles definidos como código en plantillas versionadas |
| Proteger los datos en tránsito y en reposo | Clasificar por sensibilidad; cifrado, tokenización y control de acceso |
| Mantener a las personas lejos de los datos | Reducir el acceso directo y el procesamiento manual |
| Prepararse para eventos de seguridad | Procesos de incidentes, simulaciones y automatización |

### 3. Fiabilidad (5)

| Principio | Idea |
|---|---|
| Recuperarse automáticamente de fallos | Monitorear KPI y activar la recuperación al superar un umbral |
| Probar los procedimientos de recuperación | Simular fallos antes de que ocurran |
| Escalar horizontalmente | Varios recursos pequeños en vez de uno grande, sin punto único de fallo común |
| Dejar de adivinar la capacidad | Monitorear la demanda y agregar o quitar recursos automáticamente |
| Gestionar los cambios con automatización | Cambios de infraestructura automatizados, rastreados y revisados |

### 4. Eficiencia del rendimiento (5)

| Principio | Idea |
|---|---|
| Democratizar tecnologías avanzadas | Consumirlas como servicio (NoSQL, transcodificación, machine learning) |
| Globalizarse en minutos | Desplegar en varias regiones para reducir la latencia |
| Usar arquitecturas sin servidor | Sin servidores físicos que operar |
| Experimentar con más frecuencia | Comparar tipos de instancia, almacenamiento o configuraciones |
| Considerar la simpatía mecánica | Elegir la tecnología según el objetivo y los patrones de acceso a datos |

### 5. Optimización de costos (5)

| Principio | Idea |
|---|---|
| Implementar gestión financiera en la nube | Dedicar tiempo y recursos a crear esa capacidad |
| Adoptar un modelo de consumo | Pagar lo que se usa; apagar desarrollo y pruebas fuera de horario ahorra hasta 75 % (40 h de 168 h) |
| Medir la eficiencia general | Comparar el resultado de negocio con su costo |
| Dejar de gastar en trabajo pesado indiferenciado | AWS opera los centros de datos; el cliente se enfoca en su negocio |
| Analizar y atribuir el gasto | Asignar costos a cada responsable para medir el ROI |

### 6. Sostenibilidad (6)

| Principio | Idea |
|---|---|
| Comprender el impacto | Medirlo y modelar el futuro, incluido el uso y el retiro |
| Establecer objetivos de sostenibilidad | Metas a largo plazo, ej.: menos recursos por transacción |
| Maximizar la utilización | Dimensionar bien: un host al 60 % es más eficiente que dos al 30 % |
| Anticipar y adoptar hardware y software más eficientes | Evaluarlos y diseñar con flexibilidad para adoptarlos |
| Usar servicios administrados | Compartir infraestructura reduce la necesaria (AWS Fargate, S3 Lifecycle, EC2 Auto Scaling) |
| Reducir el impacto posterior en los usuarios | Menos energía al usar el servicio y menos renovación de dispositivos |

## Cómo reconocer el pilar en una pregunta

| Pista | Pilar |
|---|---|
| Procedimientos, automatizar operaciones, observabilidad, aprender de incidentes | Excelencia operativa |
| Mínimo privilegio, cifrado, trazabilidad, respuesta a incidentes | Seguridad |
| Recuperarse de fallos, escalado horizontal, pruebas de recuperación | Fiabilidad |
| Elegir el tipo de recurso correcto, latencia global, sin servidor | Eficiencia del rendimiento |
| Modelo de consumo, eliminar gasto innecesario, atribuir costos | Optimización de costos |
| Energía, emisiones, maximizar la utilización | Sostenibilidad |

Relación con lo visto: la [infraestructura como código](m01-conceptos-nube.md#infraestructura-como-código-iac) aplica "automatizar de forma segura" (excelencia operativa) y "gestionar los cambios con automatización" (fiabilidad).

## Tarea 5: texto para escribir a mano

Formato: solo conceptos, máximo una hoja.

**AWS Well-Architected Framework y sus seis pilares**

El AWS Well-Architected Framework es una guía de buenas prácticas de AWS para diseñar y operar sistemas en la nube. Ayuda a entender las ventajas y desventajas de cada decisión de arquitectura y permite evaluar una carga de trabajo mediante preguntas para detectar qué se puede mejorar. Esta revisión es una conversación constructiva, no una auditoría. Se organiza en seis pilares:

1. **Excelencia operativa:** compromiso de construir software correctamente y entregar siempre una gran experiencia al cliente. Abarca cómo organizar los equipos, diseñar la carga de trabajo, operarla a escala y hacerla evolucionar.
2. **Seguridad:** capacidad de proteger datos, sistemas y activos aprovechando las tecnologías de la nube.
3. **Fiabilidad:** capacidad de una carga de trabajo para cumplir su función de forma correcta y consistente cuando se espera, durante todo su ciclo de vida.
4. **Eficiencia del rendimiento:** capacidad de usar los recursos de la nube de forma eficiente para cumplir los requisitos de rendimiento, y de mantener esa eficiencia cuando cambian la demanda y la tecnología.
5. **Optimización de costos:** capacidad de ejecutar sistemas que entreguen valor al negocio al menor precio posible.
6. **Sostenibilidad:** se enfoca en reducir el impacto ambiental de la nube, especialmente el consumo de energía, aprovechando al máximo los recursos y minimizando los necesarios.

Fuente: AWS, *AWS Well-Architected Framework* (documentación oficial).