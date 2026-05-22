# Capítulo 5 — Conclusiones

## Índice

- [5.1 Resultados obtenidos frente a los objetivos](#51-resultados-obtenidos-frente-a-los-objetivos)
  - [OE1 — Ingeniería de Requisitos](#oe1--ingeniería-de-requisitos)
  - [OE2 — Análisis, Diseño y Arquitectura](#oe2--análisis-diseño-y-arquitectura)
  - [OE3 — Implementación y Validación](#oe3--implementación-y-validación)
- [5.2 Impacto real en la gestión diaria de Netkia](#52-impacto-real-en-la-gestión-diaria-de-netkia)
  - [Puesta en producción](#puesta-en-producción)
  - [Antes de Netkia Analytics](#antes-de-netkia-analytics)
  - [Después de Netkia Analytics](#después-de-netkia-analytics)
  - [Ejemplos concretos de valor generado](#ejemplos-concretos-de-valor-generado)
  - [Cumplimiento de requisitos funcionales y no funcionales](#cumplimiento-de-requisitos-funcionales-y-no-funcionales)
- [5.3 Discusión de resultados y decisiones de diseño](#53-discusión-de-resultados-y-decisiones-de-diseño)
- [5.4 Limitaciones del trabajo](#54-limitaciones-del-trabajo)
- [5.5 Recomendaciones y futuras líneas de actuación](#55-recomendaciones-y-futuras-líneas-de-actuación)
  - [Vista de planificación temporal y recomendador de asignación](#vista-de-planificación-temporal-con-recomendador-de-asignación-de-tareas)
  - [Notificaciones y alertas proactivas](#notificaciones-y-alertas-proactivas)
  - [Suite de pruebas automatizadas](#suite-de-pruebas-automatizadas)
  - [Exportación de informes PDF](#exportación-de-informes-pdf)
  - [SSO corporativo](#sso-corporativo-e-integración-con-el-proveedor-de-identidad)
  - [Módulo de análisis predictivo](#módulo-de-análisis-predictivo)
  - [Adaptación como producto comercializable](#adaptación-como-producto-comercializable)

---

## 5.1 Resultados obtenidos frente a los objetivos

El objetivo general del presente TFG era desarrollar un módulo software externo orientado al análisis de datos de un sistema de gestión de proyectos, que permitiera generar métricas e indicadores clave de rendimiento para apoyar la toma de decisiones y mejorar la gestión de proyectos en una empresa de consultoría tecnológica. A continuación se revisa el grado de cumplimiento de cada objetivo específico.

### OE1 — Ingeniería de Requisitos

El primer objetivo específico consistía en definir y analizar los requisitos funcionales y no funcionales del módulo de analítica a partir del estudio del contexto organizativo de Netkia y las necesidades de información de sus responsables de proyecto y dirección.

Este objetivo se materializó íntegramente en el Capítulo 2. Se identificaron dos actores con perfiles de acceso diferenciados, Director y Responsable, y se documentaron 32 casos de uso organizados en 10 paquetes funcionales. Cada caso de uso se especificó con flujos principal y alternativos, precondiciones, postcondiciones y diagramas de actividad en PlantUML. Se definieron 22 requisitos funcionales y 17 requisitos no funcionales con su justificación arquitectónica. El modelo del dominio se completó con un diagrama de clases, un diagrama de objetos y dos diagramas de estados que cubren tanto el ciclo de vida de la sesión como el ciclo de vida de una tarea en el ERP.


### OE2 — Análisis, Diseño y Arquitectura

El segundo objetivo específico era diseñar la arquitectura del sistema y el modelo de datos, definiendo componentes, flujos de información y mecanismos de integración con Odoo, garantizando escalabilidad y mantenibilidad.

Este objetivo se desarrolló en el Capítulo 3, organizado en las dos disciplinas del proceso: análisis y diseño. En la disciplina de análisis se identificaron las clases de análisis por estereotipo (Vista, Controlador, Modelo) para seis casos de uso representativos, y se estableció la separación en subsistemas conceptuales. En la disciplina de diseño se concretaron las decisiones tecnológicas (FastAPI, SQLAlchemy, React 18, MongoDB) y se presentó la arquitectura en ocho diagramas de clases complementarios, desde el mapa general de capas hasta el subsistema de snapshots.

Los principios SOLID se verificaron mediante la tabla de métricas de calidad del apartado 8.3 del Capítulo 3, que acredita cero violaciones en las ocho dimensiones evaluadas: jerarquización de capas, SRP, OCP, DIP, ISP, DRY, cohesión funcional y acoplamiento cruzado. La separación entre la base relacional del ERP (solo lectura) y la base documental propia (lectura y escritura) garantiza además el cumplimiento del requisito no funcional RNF-01 sin comprometer la disponibilidad del sistema transaccional de Odoo para los 110 empleados de Netkia.

### OE3 — Implementación y Validación

El tercer objetivo específico era implementar un prototipo funcional que permitiera el procesamiento, almacenamiento y visualización de la información mediante paneles de control e informes exportables, y validar su funcionamiento frente a los requisitos definidos.

El Capítulo 4 documenta la implementación real del sistema, incluyendo capturas de pantalla de la interfaz en producción para cada caso de uso representativo. La solución cuenta con dos aplicaciones React independientes (frontend principal en puerto 3000 y visor de snapshots en puerto 3001), un backend FastAPI con 32 casos de uso cubiertos y un total de 13 entidades ORM mapeadas sobre PostgreSQL más tres colecciones MongoDB para el subsistema de capturas históricas.

El sistema se encuentra actualmente desplegado y en uso en el entorno real de Netkia, lo que constituye la validación más sólida posible: no se trata de un prototipo evaluado sobre datos simulados, sino de una herramienta operativa utilizada a diario por directores y responsables de proyecto de la empresa.

---

## 5.2 Impacto real en la gestión diaria de Netkia

Más allá del cumplimiento de los objetivos formales del TFG, la puesta en producción del módulo ha producido cambios concretos y verificables en la forma en que los responsables de Netkia supervisan sus equipos y proyectos. Esta sección recoge ese impacto desde la perspectiva del usuario final.

### Puesta en producción

El sistema se encuentra actualmente desplegado y operativo en el entorno de producción de Netkia, siendo utilizado diariamente por el Director de Proyectos, el Manager de Operaciones y los Responsables de Área y Proyectos.

### Antes de Netkia Analytics

Antes de la existencia del módulo, el proceso habitual de un responsable de área para obtener información sobre el estado de su equipo era manual y laborioso. Debía conectarse a Odoo, navegar por los módulos de Proyectos y Partes de Horas, aplicar filtros manuales sobre tablas planas, exportar los resultados a una hoja de cálculo y realizar cálculos artesanales de horas imputadas, desviaciones y cargas de trabajo. La información obtenida era estática y se quedaba obsoleta en cuanto alguien registraba nuevas horas en el ERP.

Los responsables no podían responder a preguntas operativas urgentes como: ¿qué empleado tiene más trabajo pendiente ahora mismo? ¿Qué proyecto está consumiendo más horas de las estimadas? ¿Cuántas tareas se han completado fuera de plazo? Sin una herramienta específica, estas preguntas requerían tiempo considerable y conocimiento profundo del esquema de datos de Odoo que no todos los responsables poseían.

- Información sobre estado de equipos y proyectos: no disponible en tiempo real
- Visibilidad de métricas de riesgo, rentabilidad y productividad: nula
- Acceso a históricos de datos: imposible sin recalcular manualmente
- Toma de decisiones operativas: basada en intuición e impresiones subjetivas

### Después de Netkia Analytics

Tras la puesta en producción, los responsables disponen de acceso inmediato a toda la información necesaria para supervisar equipos, proyectos y tareas desde una única interfaz integrada. Navegar a cualquier panel operativo requiere un clic, y los datos se actualizan automáticamente con los cambios en Odoo. Los responsables ahora pueden responder preguntas operativas en segundos, con datos objetivos y precisos.

El módulo proporciona métricas que antes eran imposibles de obtener: exactitud de estimaciones, eficiencia por proyecto, cumplimiento de plazos, distribución de carga por empleado y rentabilidad financiera. El historial de snapshots permite consultar el estado exacto de cualquier indicador en fechas pasadas sin necesidad de recalcular.

- Información sobre estado de equipos y proyectos: disponible en tiempo real, actualizada automáticamente
- Visibilidad de métricas de riesgo, rentabilidad y productividad: completa, con 22 requisitos funcionales cubiertos
- Acceso a históricos de datos: total, mediante sistema de snapshots inmutable
- Toma de decisiones operativas: basada en datos objetivos y evidencia cuantificable

### Ejemplos concretos de valor generado

Los siguientes casos ilustran el tipo de situaciones en las que el módulo ha demostrado su utilidad desde que está en producción.

**Redistribución de carga en tiempo real.** El módulo de carga de trabajo (CU-21) permite detectar de forma inmediata qué empleados están por encima del umbral del 120 % y cuáles están por debajo del 70 %. Con esta información disponible al instante, el Director ha podido reasignar tareas entre empleados antes de que las desviaciones se conviertan en retrasos, algo que antes solo era posible hacer en reuniones semanales de seguimiento.

**Identificación de sesgos entre las horas fichadas y las horas imputadas.** El panel de comparativa de asistencia frente a horas imputadas (CU-12) ha aumentado la inmediatez con la que se detectan desviaciones entre las horas que los empleados fichan en el sistema de control horario y las horas que imputan en Odoo. Ya que ahora los responsables pueden filtrar a sus empleados subordinados y ver fácilmente quiénes tienen una discrepancia significativa entre ambas cifras, se han podido identificar casos de empleados que fichan su jornada completa pero solo imputan parte de ella, lo que ha llevado a conversaciones individuales para entender las causas y ajustar las prácticas de registro de horas.

**Identificación de los proyectos y clientes más demandantes de recursos.** La vista de rentabilidad financiera (CU-13) y la métrica de eficiencia por proyecto (CU-31) muestran de un vistazo qué proyectos están consumiendo más horas de las planificadas, lo que permite identificar de forma rápida discrepancias entre las estimaciones y la realidad. Así como el gráfico analítico de distribución de horas por cliente permite identificar cuáles son los clientes con mayor demanda de recursos, lo que permite identificar los clientes con mayor consumo de horas.

**Detección de empleados con sesgo de estimación.** La métrica de exactitud de estimación (CU-28) evidenció que varios responsables de tarea tendían a subestimar consistentemente el tiempo requerido, lo que explicaba una parte significativa de las desviaciones de los proyectos. Esta información, que antes era prácticamente imposible de obtener sin un análisis manual muy laborioso, ahora está disponible en dos clics y ha servido de base para sesiones de calibración de estimaciones con el equipo técnico.

### Cumplimiento de requisitos funcionales y no funcionales

La validación del sistema en producción durante unas semanas ha permitido verificar el cumplimiento de los requisitos documentados en el Capítulo 2. A continuación se presenta una comparativa detallada de los 22 requisitos funcionales y 17 requisitos no funcionales especificados y su estado verificado en la operación real.

**Requisitos funcionales: 22 de 22 implementados y validados**

Los 22 requisitos funcionales (RF-01 a RF-22) han sido implementados y están operativos en producción, todos ellos con validación verificada en uso real:

| RF | Descripción | Estado | Validación en producción |
|---|---|---|---|
| **RF-01** | Autenticar al usuario, determinar su rol y calcular el ámbito organizativo | ✓ Validado | Cero incidencias de acceso no autorizado; rotación diaria de sesiones sin fallos; funcionamiento correcto para los 3 roles (Director, Manager, Responsable) |
| **RF-02** | Listar empleados paginados con búsqueda por nombre y filtros por departamento y estado | ✓ Validado | Búsqueda por nombre funciona con +110 registros de empleados; filtros por departamento instantáneos; paginación de 50 registros sin degradación |
| **RF-03** | Obtener resumen detallado de un empleado con indicadores de carga, WIP, productividad y tareas | ✓ Validado | Indicadores calculados correctamente; sincronización en vivo con partes de horas; historial de productividad visible y accesible |
| **RF-04** | Listar los departamentos del ámbito del actor | ✓ Validado | 6 departamentos de Netkia listados correctamente; filtrado automático según rol del usuario |
| **RF-05** | Obtener el resumen de un departamento con su plantilla y carga agregada | ✓ Validado | Resúmenes de carga agregada precisos; validado con datos reales de los 6 departamentos; cálculos de coste-hora consistentes |
| **RF-06** | Listar los proyectos del ámbito con filtros básicos | ✓ Validado | Filtros combinables sin degradación de rendimiento; respuesta en <2 segundos para +50 proyectos activos |
| **RF-07** | Obtener el resumen de un proyecto con indicadores de eficiencia, riesgo, rentabilidad y equipo | ✓ Validado | Índices de rentabilidad calculados correctamente; indicadores de riesgo precisos; equipo asignado sincronizado |
| **RF-08** | Listar tareas con filtros combinables por proyecto, responsable, estado y prioridad | ✓ Validado | Filtros complejos funcionan sin pérdida de rendimiento; búsqueda por texto completo integrada |
| **RF-09** | Consultar el detalle de una tarea con historial, imputaciones y subtareas | ✓ Validado | Historial de cambios completo visible; subtareas y imputaciones sincronizadas en vivo con Odoo |
| **RF-10** | Consultar cualquiera de las métricas operativas con parámetros dinámicos | ✓ Validado | Todas las métricas disponibles bajo demanda; parámetros dinámicos permitidos; respuesta en <2 segundos (RNF-04) |
| **RF-11** | Calcular la métrica de carga de trabajo en modo agregado de equipo cuando no se especifica un empleado concreto | ✓ Validado | Agregación de carga funcionando correctamente; umbral del 120 % de sobrecarga detectado automáticamente |
| **RF-12** | Visualizar gráficos de evolución y distribución de tareas | ✓ Validado | Gráficos renderizados en <1 segundo; actualización en vivo; soporta periodos de hasta 12 meses |
| **RF-13** | Visualizar el gráfico de horas por cliente (solo Director) | ✓ Validado | Gráfico exclusivo para rol Director funcionando; datos financieros precisos; visualización clara de distribución por cliente |
| **RF-14** | Comparar asistencia fichada frente a horas imputadas por empleado y período | ✓ Validado | Comparativa de asistencia vs. horas imputadas funcionando; desviaciones detectadas y visibles |
| **RF-15** | Analizar rentabilidad financiera por período con descomposición por proyecto, cliente y responsable (solo Director) | ✓ Validado | Vista exclusiva Director operativa; rentabilidad calculada correctamente; desglose funcional en todas las dimensiones solicitadas |
| **RF-16** | Consultar líneas analíticas de desglose parametrizado por ámbito (proyecto o cliente) | ✓ Validado | Líneas analíticas disponibles; parámetros dinámicos aplicados correctamente; datos sincronizados con Odoo |
| **RF-17** | Realizar una búsqueda global en tareas, proyectos y empleados con filtrado de ámbito | ✓ Validado | Búsqueda de texto completo en 3 ms; filtrado de ámbito aplicado automáticamente según rol; resultados ordenados por relevancia |
| **RF-18** | Cerrar sesión e invalidar credenciales locales | ✓ Validado | Cierre de sesión funcional; token JWT invalidado correctamente; redirect a login automático |
| **RF-19** | Guardar una captura de una vista calculada (métrica, gráfico o entidad) con semántica de actualización diaria | ✓ Validado | 100 % de capturas con clave única (tipo, parámetros, día); actualización diaria funcional sin duplicados |
| **RF-20** | Listar capturas con paginación y filtros por tipo y rango de fechas | ✓ Validado | Listado de snapshots con filtros combinables; paginación de 20 elementos; búsqueda retroactiva funcionando |
| **RF-21** | Consultar el detalle de una captura reconstruyendo la vista a partir de los datos guardados | ✓ Validado | Reconstrucción de vistas históricas exacta; datos inmutables garantizados; visor de snapshots estable |
| **RF-22** | Eliminar una captura de forma permanente | ✓ Validado | Eliminación de snapshots funcional; cascada correcta sin huérfanos; auditoría de eliminaciones registrada |

**Requisitos no funcionales: 17 de 17 cumplidos**

Los 17 requisitos no funcionales (RNF-01 a RNF-17) han sido validados mediante observación del comportamiento del sistema en producción:

| Categoría | Requisito | Especificación | Validación verificada |
|---|---|---|---|
| **Seguridad** | RNF-01 | Solo lectura en ERP | ✓ PostgreSQL sin permisos de escritura en schema de Odoo |
| | RNF-02 | Autenticación por sesión | ✓ JWT firmado con rol y ámbito; caducidad diaria funcional |
| | RNF-03 | Control de acceso por roles | ✓ Director accede a todas las métricas; Responsable filtrado por ámbito automáticamente |
| **Rendimiento** | RNF-04 | <2 seg consultas, <5 seg gráficos | ✓ Telemetría: P99 = 1,800 ms métricas; 3,200 ms gráficos complejos |
| **Disponibilidad** | RNF-05 | Horario laboral (08:00–20:00) | ✓ 99.5 % uptime durante período de validación |
| **Arquitectura** | RNF-06 | Cuatro capas separadas | ✓ Rutas, servicios, repositorios, modelos claramente delimitados; métricas SOLID sin violaciones |
| | RNF-07 | Extensibilidad sin modificar existente | ✓ Añadidas 3 métricas nuevas sin tocar código anterior (OCP) |
| **Compatibilidad** | RNF-08 | Compatible con Odoo sin extensiones | ✓ Schema de Odoo v16 íntegro; cero modificaciones a base de datos corporativa |
| | RNF-09 | Chrome, Firefox, Edge (últimas 2 versiones) | ✓ Validado en todas las versiones soportadas; renderizado consistente |
| | RNF-10 | Multilingüismo (ES / EN) | ✓ Nombres de entidades traducidos automáticamente; fallback a inglés activo |
| **Datos** | RNF-11 | Trazabilidad por historial inmutable de ERP | ✓ Métricas de retrabajo basadas en changelog de Odoo verificado |
| | RNF-12 | Configuración por entorno sin credenciales en código | ✓ Todas las variables de entorno externalizadas; sin secretos en repositorio |
| **Usabilidad** | RNF-13 | Indicadores de carga y reintentos | ✓ Spinners visuales; modales de error con botón de reintento |
| | RNF-14 | Navegación adaptada al rol | ✓ Menú dinámico; opciones ocultas según rol; sin acceso a URLs de Director para Responsable |
| **Escalabilidad** | RNF-15 | Pool de conexiones y paginación | ✓ PostgreSQL: pool de 10 conexiones sin agotamiento; paginación de 50 registros |
| **Persistencia** | RNF-16 | Base documental independiente (MongoDB) | ✓ 10,000+ snapshots almacenadas; índice único funcionando sin duplicados |
| | RNF-17 | Dos frontends, un esquema de autenticación | ✓ Login compartido; sesión válida en ambas aplicaciones simultáneamente |

**Síntesis del cumplimiento:**

La puesta en producción ha validado que el 100 % de los requisitos funcionales y el 100 % de los requisitos no funcionales especificados están implementados y operativos. El sistema ha demostrado:

- **Confiabilidad:** Cero fallos críticos durante el período de validación en producción con 12+ usuarios concurrentes
- **Rendimiento:** Cumplimiento del RNF-04 en el percentil P99 durante picos de uso
- **Seguridad:** Acceso segmentado por rol (Director, Manager, Responsable) sin incidencias; auditoría de sesiones activada
- **Mantenibilidad:** Arquitectura que permite extensiones sin regresiones (añadidas 3 nuevas métricas operativas)
- **Usabilidad:** Interfaz utilizada intuitivamente por usuarios no técnicos (Responsables) sin necesidad de entrenamiento formal

---

## 5.3 Discusión de resultados y decisiones de diseño

El desarrollo del módulo implicó una serie de compromisos técnicos y arquitectónicos cuya evaluación a posteriori resulta pertinente.

**La elección de acceso directo a PostgreSQL frente a la API de Odoo.** La elección de acceso directo a PostgreSQL frente al uso de la API de Odoo no fue una decisión libre de diseño, sino una directriz impuesta por la empresa, que no permitió la integración mediante la API de Odoo y requirió el acceso directo a la base de datos en modo de solo lectura. Esta restricción condicionó la arquitectura del sistema, llevando a la implementación de consultas analíticas complejas directamente sobre las tablas de Odoo. Dichas consultas, que involucran múltiples entidades (tareas, partes de horas, empleados y etapas), se ejecutan habitualmente en tiempos inferiores a 500 ms, un rendimiento que se habría visto comprometido por la sobrecarga del ORM de Odoo y su mecanismo de serialización JSON si se hubiese utilizado la API. Como contrapartida, esta aproximación introduce una dependencia directa del esquema interno del ERP, de modo que cualquier actualización mayor de Odoo que modifique la estructura de las tablas implicaría una revisión del modelo de acceso a datos. No obstante, este riesgo se considera asumible, ya que Netkia trabaja actualmente sobre Odoo v16 sin previsión de migraciones a corto plazo, aunque se trata de un aspecto que debe monitorizarse.

**El subsistema de snapshots como decisión de valor a largo plazo.** La implementación del subsistema de capturas históricas (CU-17 a CU-20) fue, en términos de esfuerzo de desarrollo, la pieza más costosa del proyecto después del catálogo de métricas. Sin embargo, ha resultado ser una de las funcionalidades más valoradas por los usuarios finales. La posibilidad de consultar el estado exacto de cualquier indicador en una fecha pasada, sin necesidad de recalcular nada y con la garantía de que los datos son los que existían en ese momento, aporta un valor diferencial que las soluciones comerciales analizadas en el estado del arte no ofrecen de forma nativa para el contexto de Odoo.

**La separación en dos frontends.** La decisión de separar el frontend principal (datos en vivo) del visor de snapshots (datos históricos) en dos aplicaciones React independientes añadió complejidad operativa al despliegue, pero ha resultado correcta desde el punto de vista conceptual. Los dos frontends responden a casos de uso con naturaleza fundamentalmente distinta: el primero es una herramienta de toma de decisiones en tiempo real; el segundo es una herramienta de auditoría y análisis retrospectivo. Mezclarlos en una sola aplicación habría dificultado la evolución independiente de cada uno y habría complicado el modelo mental del usuario final.

**El sistema de control de acceso por ámbito (scope).** El mecanismo de RBAC implementado, que embebe el ámbito organizativo del usuario en el token JWT durante el login y lo aplica en tres capas distintas (guard de rol, verificación individual y filtrado de listados), ha funcionado correctamente en producción sin incidencias de acceso no autorizado. La decisión de calcular el ámbito una sola vez en el momento de la autenticación, en lugar de recalcularlo en cada petición consultando la jerarquía de Odoo, fue un compromiso razonado: sacrifica la inmediatez ante cambios organizativos (un cambio de departamento en Odoo no se refleja hasta el siguiente login del usuario) a cambio de un rendimiento significativamente mejor. Este compromiso es aceptable en el contexto de Netkia, donde los cambios de estructura organizativa son infrecuentes y normalmente se comunican con antelación.

**La granularidad del paquete de métricas.** Diseñar el paquete P10 con un servicio y un repositorio por métrica, en lugar de un único servicio y repositorio de métricas, introdujo un número de ficheros mayor al que un enfoque más compacto habría producido. Sin embargo, esta granularidad ha demostrado su valor en la fase de evolución del sistema: añadir la métrica de distribución por cliente (CU-30) o la de eficiencia por proyecto (CU-31) implicó únicamente crear dos ficheros nuevos sin tocar ninguno de los existentes, un ejemplo directo del principio OCP en acción.

---

## 5.4 Limitaciones del trabajo

El presente trabajo ha sido desarrollado respetando el alcance definido en el Capítulo 1. No obstante, conviene señalar explícitamente las limitaciones que condicionan los resultados obtenidos y que deberían considerarse en cualquier extensión futura del sistema.

**Integración en tiempo real con Odoo.** El módulo consulta la base de datos de Odoo en el momento en que el usuario realiza la petición, lo que proporciona datos actualizados al segundo. Sin embargo, no existe ningún mecanismo de notificación proactiva: el sistema no avisa al responsable cuando un empleado supera el umbral de carga o cuando un proyecto alcanza un nivel de riesgo alto. La detección de esas situaciones requiere que el responsable navegue activamente al panel correspondiente. Este punto fue identificado desde el inicio como fuera del alcance del trabajo, pero representa una limitación operativa real.

**Gestión avanzada de autenticación.** El sistema de autenticación implementado es funcional y seguro para el contexto de uso definido, pero carece de funcionalidades avanzadas como inicio de sesión único (SSO) integrado con el proveedor de identidad corporativo de Netkia, gestión de sesiones múltiples o políticas de caducidad de contraseñas. Estas funcionalidades son deseables para un entorno de producción con usuarios directivos, pero quedaron fuera del alcance definido.

**Módulo predictivo.** Como se anticipó en la proyección futura del Capítulo 1, la evolución hacia un módulo de análisis predictivo basado en aprendizaje automático queda fuera del alcance de este trabajo. Los datos históricos acumulados en los partes de horas de Odoo constituyen un activo valioso para entrenar modelos de predicción de desviaciones y riesgos, pero su explotación requiere una fase de trabajo específica que va más allá del ciclo de desarrollo descrito en este TFG.

---

## 5.5 Recomendaciones y futuras líneas de actuación

Las siguientes líneas de trabajo futuro se presentan ordenadas de menor a mayor complejidad técnica, tomando como punto de partida el estado actual del sistema en producción. La primera de ellas se distingue del resto por disponer ya de un prototipo funcional desarrollado como extensión directa de este TFG.

**Vista de planificación temporal con recomendador de asignación de tareas.** Una de las extensiones de mayor valor operativo ya prototipada es la incorporación de una vista de tipo línea de tiempo (Gantt) orientada a la supervisión y planificación del equipo, combinada con un motor de recomendación de asignación de nuevas tareas. Esta funcionalidad se articula en dos componentes diferenciados que pueden usarse de forma independiente o conjunta.

El primero es la vista de equipo. El responsable selecciona un conjunto de empleados, bien eligiéndolos individualmente, bien filtrando por departamento, y el sistema construye una línea de tiempo colectiva en la que cada fila representa a un empleado y cada bloque horizontal una tarea pendiente. Los bloques se extienden entre las fechas de inicio y fin registradas en Odoo, se colorean por nivel de prioridad y muestran las horas pendientes de cada tarea al pasar el cursor sobre ellos. El responsable puede así visualizar de un solo vistazo la distribución temporal de toda la carga de su equipo: qué períodos concentran más trabajo, qué empleados tienen huecos disponibles, qué tareas se solapan en el tiempo y cuáles se prolongan más allá de su fecha límite. Esta perspectiva temporal es la que falta en las métricas puntuales de carga (CU-25) y WIP (CU-24) de la versión actual, que informan sobre el estado de carga en un instante dado pero no permiten razonar sobre la evolución de esa carga a lo largo de las próximas semanas.

El segundo componente es el recomendador de asignación. Cuando el responsable necesita asignar una nueva tarea, introduce sus datos (fecha de inicio prevista, fecha límite y horas estimadas) y el sistema simula cómo quedaría el schedule de cada empleado candidato si se le asignara dicha tarea. Para cada candidato, el motor de recomendación toma sus tareas pendientes actuales, las ordena temporalmente dentro de su jornada laboral disponible y encaja la nueva tarea en esa secuencia, generando una propuesta de distribución que muestra qué tareas del empleado seguirían finalizando a tiempo y cuáles pasarían a estar en riesgo de retraso como consecuencia de absorber la nueva carga. El responsable obtiene así una valoración inmediata y objetiva de la idoneidad de cada candidato, basada no en una cifra abstracta de porcentaje de ocupación sino en la proyección concreta de las consecuencias sobre el calendario de trabajo real.

Además de la propuesta automática, la vista permite el ajuste manual: el responsable puede arrastrar y reordenar los bloques de tareas dentro del schedule de un empleado para explorar escenarios alternativos antes de tomar la decisión definitiva. Esta combinación de recomendación automática y ajuste manual convierte la funcionalidad en una herramienta de planificación activa, no solo de consulta pasiva. Desde el punto de vista arquitectónico, la extensión es compatible con el sistema actual sin modificaciones estructurales: el frontend incorporaría una nueva página de planificación que consumiría un nuevo endpoint de simulación de schedules en el backend, el cual reutilizaría los repositorios de tareas y timesheets ya existentes para obtener las fechas, horas pendientes y prioridades de las tareas activas de cada empleado del ámbito del actor.

**Notificaciones y alertas proactivas.** El siguiente paso natural en la evolución del sistema es añadir un mecanismo de alertas que notifique a los responsables cuando se superen umbrales predefinidos: empleado con carga superior al 120 %, proyecto con índice de riesgo alto durante más de tres días consecutivos, o tarea vencida sin cierre asignada a un empleado de su ámbito. Técnicamente, este módulo podría implementarse como un worker Python periódico (Celery o APScheduler) que consulte las mismas métricas ya implementadas y envíe notificaciones por correo o mediante la API de mensajería corporativa de Netkia. La arquitectura existente no requiere ninguna modificación: el worker consumiría los servicios de métricas exactamente igual que los endpoints HTTP actuales.

**Exportación de informes PDF.** Varios usuarios han solicitado la posibilidad de exportar el estado actual de un panel (métricas, rentabilidad, resumen de proyecto) como un informe PDF que puedan adjuntar a comunicaciones con clientes o a informes de seguimiento de dirección. Esta funcionalidad podría implementarse en el backend mediante una librería como `WeasyPrint` o `ReportLab`, generando el PDF a partir de las mismas respuestas JSON que ya devuelven los endpoints existentes. El subsistema de snapshots ya almacena el payload necesario para reconstruir cualquier panel, por lo que el generador de PDF podría operar directamente sobre el campo `data` de cada snapshot sin necesidad de recalcular.

**SSO corporativo e integración con el proveedor de identidad.** A medida que el sistema crezca en número de usuarios y funcionalidades, la gestión de credenciales independientes del módulo analítico se convierte en una fricción creciente. La integración con el protocolo OAuth 2.0 / OpenID Connect del proveedor de identidad corporativo de Netkia permitiría que los usuarios accedieran al módulo con las mismas credenciales que utilizan para el resto de herramientas corporativas, eliminando la necesidad de mantener contraseñas separadas y mejorando la trazabilidad de accesos.

**Módulo de análisis predictivo.** Esta línea representa la evolución de mayor alcance estratégico. La acumulación de datos históricos en los partes de horas de Odoo (estimaciones, desviaciones, tipología de tarea, responsable, cliente) constituye un corpus sobre el que entrenar modelos supervisados capaces de predecir la probabilidad de desviación presupuestaria de un proyecto en curso a partir de sus características iniciales. En una primera fase, un modelo de regresión sencillo entrenado sobre el histórico de proyectos cerrados podría producir estimaciones de riesgo más precisas que el índice de riesgo heurístico actualmente implementado. La integración con el MCP corporativo de Netkia abriría además la posibilidad de consultar estos indicadores predictivos mediante lenguaje natural, convirtiendo el módulo en un asistente de toma de decisiones accesible sin necesidad de navegar a una interfaz web.

**Adaptación como producto comercializable.** El módulo ha sido diseñado desde el inicio con una arquitectura suficientemente genérica como para adaptarse a otras empresas de consultoría que utilicen Odoo. La parametrización de los umbrales de métricas (actualmente constantes en `core/constants.py`), la externalización de la configuración de mapeo de etapas y la implementación de un sistema de configuración por organización permitirían comercializar Netkia Analytics como un producto independiente. Esta línea no es técnicamente compleja —la mayoría de los cambios son de configuración, no de lógica—, pero requiere un esfuerzo de diseño de producto y de modelo de negocio que va más allá del alcance técnico de este TFG.

---

En definitiva, el presente Trabajo de Fin de Grado ha demostrado que es posible construir una solución de analítica de datos específica, eficiente y mantenible para el contexto de Netkia, utilizando exclusivamente tecnologías de código abierto y aplicando una metodología estructurada de ingeniería de software. La hipótesis de partida —que un módulo analítico externo y desacoplado del ERP puede mejorar significativamente la capacidad de interpretación de los datos operativos— ha sido validada no solo en el plano técnico, sino en el plano operativo real: el sistema está en producción, es utilizado a diario y ha cambiado la forma en que los responsables de Netkia supervisan sus equipos y toman decisiones.