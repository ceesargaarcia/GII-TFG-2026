# Modelo del Dominio

El sistema se compone de una aplicación web analítica conectada a la base de datos del ERP en modo lectura, cuya función principal es consumir los datos del ERP Odoo, estructurarlos y transformarlos en información útil para la toma de decisiones. Por ello, el modelo del dominio no introduce nuevas entidades de negocio, sino que se apoya en las ya existentes en Odoo, garantizando coherencia con la base de datos corporativa y evitando inconsistencias.

En las siguientes secciones se presentan los diferentes diagramas que conforman el modelo del dominio: el diagrama de clases, el diagrama de objetos, el diagrama de estados y el glosario de términos, proporcionando una visión completa de la estructura conceptual del sistema.

## Diagrama de Clases

En el contexto de Netkia, el diagrama de clases está directamente alineado con la estructura de datos del ERP Odoo v16, lo que garantiza la consistencia entre la información almacenada en el sistema de gestión y los indicadores generados por el módulo analítico. Cada clase representa una entidad real del entorno organizativo, como clientes, proyectos, tareas, empleados o departamentos, y refleja los atributos necesarios para calcular métricas de productividad, carga de trabajo y rentabilidad.

![Diagrama de clases](./imagenes/diagramaDeClases.png)

Las relaciones entre las clases permiten entender la jerarquía y dependencia entre los elementos del sistema. Por ejemplo, un cliente puede tener múltiples proyectos, cada proyecto puede contener varias tareas, y cada tarea puede estar asociada a diferentes empleados y partes de horas. Esta estructura refleja el flujo natural del trabajo dentro de la empresa y facilita la obtención de indicadores agregados.

## Diagrama de Objetos

El diagrama de objetos complementa al diagrama de clases mostrando una instancia concreta del modelo del dominio en un escenario realista dentro de Netkia SL. Mientras que el diagrama de clases describe la estructura general del sistema, el diagrama de objetos permite observar cómo se materializan esas clases en situaciones reales de uso.

![Diagrama de objetos](./imagenes/diagramaDeObjetos.png)

En este caso, se representa un proyecto real con sus correspondientes tareas, subtareas, empleados asignados y partes de horas registrados. Esta representación facilita la comprensión del funcionamiento del sistema, ya que muestra cómo los datos se relacionan en la práctica y cómo se organizan dentro del ERP.

El objetivo principal de este diagrama es ilustrar el flujo de información desde el cliente hasta las tareas y los registros de horas, permitiendo visualizar de forma clara la interacción entre los distintos elementos del dominio. De esta forma, se puede entender cómo el sistema es capaz de generar métricas a partir de los datos reales de trabajo, como la productividad, la carga de trabajo o la eficiencia de los proyectos.

## Diagrama de Estados

En este apartado se presentan dos diagramas de estados: el primero describe el comportamiento general del sistema de métricas y dashboards, mientras que el segundo representa el ciclo de vida de una tarea dentro del sistema de gestión de proyectos.

### Diagrama de estados del sistema
![Diagrama de estados](./imagenes/diagramaDeEstados.png)

Este diagrama representa el comportamiento general del sistema desde que se ejecuta el módulo hasta que el usuario navega por los distintos dashboards de métricas permitiendo comprender el flujo completo de funcionamiento del sistema, desde la conexión con la base de datos hasta la visualización de la información en los dashboards.

El sistema comienza en el estado SistemaIniciado, momento en el que se ejecuta el backend y el frontend. A continuación, el sistema pasa al estado ConectandoBD, donde se establece la conexión con la base de datos PostgreSQL. Si la conexión se realiza correctamente, el sistema pasa a BDConectada y queda en espera de peticiones del frontend en el estado EsperandoPeticionFront. En caso de error, se transita al estado ErrorConexion, desde donde se puede reintentar la conexión o cerrar el sistema.

Cuando el frontend solicita información mediante llamadas a los endpoints del backend, el sistema pasa al estado LlamadaEndpoint, donde se procesa la petición. Posteriormente, se accede a la base de datos en el estado ConsultandoBD, se calculan las métricas en GenerandoMetricas y finalmente se muestran los resultados en el estado MostrandoDashboard.

Dentro del estado MostrandoDashboard, el usuario puede navegar entre las distintas páginas del sistema mediante un desplegable, accediendo a los dashboards de métricas, empleados, departamentos, tareas, proyectos, rentabilidad y asistencia. Además, en cualquier momento se pueden refrescar los datos, lo que provoca una nueva llamada a los endpoints y una nueva consulta a la base de datos.

El sistema finaliza cuando el usuario cierra el módulo, pasando al estado final.


### Diagrama de estados de una tarea
![Diagrama de estados de tareas](./imagenes/diagramaDeEstadosDeTareas.png)
Este diagrama refleja las transiciones más comunes dentro de su ciclo de vida, como la creación de la tarea, su asignación a un empleado, el inicio del trabajo, su finalización o su cancelación. Además, contempla la posibilidad de reapertura de tareas cerradas, lo que permite modelar situaciones de retrabajo o correcciones dentro del sistema.

Aunque el módulo de métricas no gestiona directamente la creación o modificación de tareas, las métricas y dashboards dependen del estado en el que se encuentran, por lo que es necesario comprender su evolución.
Este diagrama permite interpretar correctamente los datos obtenidos de la base de datos y entender cómo influyen en los indicadores mostrados en el sistema.

## Requisitos del Sistema

### Requisitos Funcionales

| ID | Descripción | Actor | Prioridad |
|---|---|---|---|
| RF-01 | El sistema debe mostrar el número total de proyectos, empleados y tareas activas. | Director | Alta |
| RF-02 | El sistema debe mostrar las tareas creadas y completadas en los últimos 7 días. | Director | Alta |
| RF-03 | El sistema debe calcular y mostrar la carga de trabajo actual de cada empleado. | Resp. Dpto. | Alta |
| RF-04 | El sistema debe mostrar el número de tareas WIP de un empleado y emitir una recomendación. | Empleado | Alta |
| RF-05 | El sistema debe permitir filtrar la vista de tareas por estado, etapa, proyecto, departamento y rango de fechas de deadline. | Empleado | Alta |
| RF-06 | El sistema debe mostrar las tareas pendientes de un empleado con horas trabajadas, pendientes e indicador de vencimiento. | Empleado | Alta |
| RF-07 | El sistema debe mostrar las tareas completadas de un empleado con indicador de entrega a tiempo y productividad individual. | Empleado | Alta |
| RF-08 | El sistema debe calcular el índice de eficiencia de un proyecto (horas planificadas vs reales). | Resp. Dpto. | Alta |
| RF-09 | El sistema debe calcular el índice de riesgo de un proyecto (tareas vencidas o próximas a vencer). | Resp. Dpto. | Media |
| RF-10 | El sistema debe calcular la rentabilidad de un proyecto usando el coste por hora de los empleados. | Director | Media |
| RF-11 | El sistema debe calcular la tasa de retrabajo (tareas cerradas y reabiertas) a partir del historial de cambios de etapa. | Resp. Dpto. | Media |
| RF-12 | El sistema debe mostrar la distribución de horas facturables por cliente en un período seleccionable. | Director | Media |
| RF-13 | El sistema debe mostrar la evolución temporal de tareas (creadas, completadas, vencidas) agrupada por semana o mes. | Director | Media |
| RF-14 | El sistema debe mostrar la comparativa de horas estimadas vs reales agrupada por empleado o por proyecto. | Director | Media |
| RF-15 | El sistema debe mostrar las horas registradas agrupadas por departamento. | Resp. Dpto. | Media |
| RF-16 | El sistema debe permitir la búsqueda global de tareas, proyectos y empleados por nombre o código. | Todos | Baja |
| RF-17 | El sistema debe mostrar el detalle completo de una tarea, incluyendo subtareas, horas, empleados asignados y responsable. | Empleado | Alta |
| RF-18 | El sistema debe soportar paginación y ordenación en todas las listas de entidades. | Todos | Alta |

### Requisitos No Funcionales (Suplementarios)

| ID | Categoría | Descripción |
|---|---|---|
| RNF-01 | **Seguridad** | El módulo accede a la base de datos de Odoo únicamente en modo lectura. Ninguna operación de escritura, actualización o eliminación está permitida desde el módulo analítico. |
| RNF-02 | **Rendimiento** | Las consultas individuales de métricas deben responder en menos de 2 segundos en condiciones normales de carga (hasta 20 usuarios concurrentes). Las consultas de gráficos complejos pueden admitir hasta 5 segundos. |
| RNF-03 | **Disponibilidad** | El sistema debe estar disponible durante el horario laboral de Netkia SL (08:00–20:00 h, L–V). Se acepta mantenimiento programado fuera de este horario. |
| RNF-04 | **Mantenibilidad** | El backend sigue una arquitectura en capas (`routes → services → data_access`) que permite ampliar métricas sin modificar rutas ni esquemas existentes. |
| RNF-05 | **Extensibilidad** | El sistema debe permitir añadir nuevas métricas creando únicamente un nuevo servicio y registrando su ruta, sin modificar el código existente. |
| RNF-06 | **Compatibilidad** | El módulo es compatible con Odoo v16 Enterprise y PostgreSQL 14+. No se soportan versiones anteriores de Odoo. |
| RNF-07 | **Usabilidad** | La interfaz debe ser operable en navegadores modernos (Chrome, Firefox, Edge en sus dos últimas versiones) sin instalación de software adicional por parte del usuario. |
| RNF-08 | **Internacionalización** | Los nombres de proyectos y etapas almacenados como JSONB en Odoo (`{es_ES: ..., en_US: ...}`) se muestran en español por defecto, con fallback a inglés. |
| RNF-09 | **Trazabilidad** | El cálculo del retrabajo y los tiempos por estado se basa en el historial auditado de Odoo (`mail_tracking_value`), garantizando la inmutabilidad de los datos de origen. |
| RNF-10 | **Configuración** | La cadena de conexión a la base de datos y los parámetros del servidor se gestionan exclusivamente mediante variables de entorno (fichero `.env`), sin credenciales en código fuente. |

## Glosario

Con el objetivo de facilitar la comprensión del modelo del dominio, se presentan dos glosarios: uno general que describe los conceptos del sistema y del ERP, y otro específico que define las métricas utilizadas en los dashboards analíticos.


### Glosario general
| Término | Definición  |
| ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ERP Odoo** | Sistema de planificación de recursos empresariales utilizado por la empresa para gestionar proyectos, tareas, empleados, clientes y registros de horas. El módulo analítico consume sus datos en modo lectura. |
| **PostgreSQL**   | Sistema de gestión de base de datos utilizado por Odoo para almacenar toda la información del ERP.      |
| **Modo lectura** | Forma de acceso a la base de datos en la que el sistema solo consulta datos sin modificarlos, garantizando la integridad de la información del ERP. |
| **Proyecto**  | Conjunto de tareas asociadas a un cliente o área de trabajo que permite organizar y controlar el desarrollo de actividades.  |
| **Tarea**  | Unidad de trabajo dentro de un proyecto que puede ser asignada a uno o varios empleados y sobre la que se registran partes de horas.   |
| **Tarea raíz**                 | Tarea sin `parent_id`, que representa una unidad de trabajo principal dentro de un proyecto.               |
| **Subtarea**  | Tarea cuyo campo `parent_id` apunta a otra tarea, permitiendo descomponer el trabajo en actividades más pequeñas.   |
| **Empleado**  | Usuario del sistema que puede estar asignado a tareas y registrar partes de horas en el ERP. |
| **Departamento**               | Unidad organizativa que agrupa empleados y permite analizar la actividad por áreas de trabajo. |
| **Cliente** | Entidad externa para la cual se desarrollan proyectos o tareas dentro del sistema. |
| **Parte de horas (Timesheet)** | Registro del tiempo trabajado por un empleado en una tarea o proyecto en una fecha concreta, almacenado en `account_analytic_line`.|
| **Etapa**                      | Estado funcional de una tarea dentro del flujo de trabajo del proyecto (kanban). Puede marcarse como cerrada. |
| **Responsable**| Empleado encargado del seguimiento de una tarea o proyecto. |
| **Endpoint** | Punto de acceso del backend que permite al frontend solicitar información mediante peticiones HTTP.|
| **API REST** | Interfaz de comunicación entre frontend y backend para obtener datos del ERP y generar métricas. |
| **Dashboard** | Interfaz visual que agrupa indicadores y métricas para facilitar la supervisión y la toma de decisiones. |
| **KPI** | Indicador clave de rendimiento utilizado para medir la eficiencia de empleados, tareas o proyectos.  |
| **Métrica**  | Valor calculado a partir de los datos del ERP que permite analizar el rendimiento del sistema o de la organización. |

### Glosario de métricas
| Métrica  | Definición |
| --------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Asistencia (Attendance)** | Compara las horas fichadas por los empleados con las horas trabajadas en tareas para medir el nivel real de actividad y presencia en el sistema.|
| **Tareas canceladas (Cancelled Tasks)**  | Número o porcentaje de tareas que han sido canceladas en un periodo determinado, utilizado para analizar la calidad de la planificación y la estabilidad de los proyectos. |
| **Distribución por cliente (Client Distribution)**  | Muestra cómo se reparten los proyectos o tareas entre los distintos clientes, permitiendo analizar la carga de trabajo y la dependencia de determinados clientes.|
| **Cumplimiento de plazos (Compliance)** | Mide el grado en que las tareas o proyectos se completan dentro de los plazos establecidos. |
| **Precisión de estimaciones (Estimation Accuracy)** | Evalúa la diferencia entre las horas planificadas y las horas reales trabajadas para medir la exactitud de las estimaciones.|
| **Tiempo de ciclo (Lead Time)** | Tiempo transcurrido desde la asignación de una tarea hasta su cierre, utilizado para medir la velocidad de ejecución del trabajo. |
| **Tiempo por prioridad (Priority Time)** | Tiempo dedicado a tareas según su nivel de prioridad, permitiendo analizar cómo se distribuye el esfuerzo en función de la importancia de las tareas.|
| **Productividad (Productivity)**| Relación entre las horas planificadas y las horas reales trabajadas para medir la eficiencia del trabajo de empleados o proyectos.                                         |
| **Rentabilidad (Profitability)**| Indicador que mide la eficiencia económica de un proyecto o tarea en función del tiempo invertido y los recursos utilizados.                                               |
| **Eficiencia del proyecto (Project Efficiency)** | Métrica que evalúa el rendimiento global de un proyecto considerando tareas cerradas, tiempo de ejecución y productividad.|
| **Tasa de retrabajo (Rework Rate)** | Porcentaje de tareas que han sido reabiertas después de cerrarse, indicando la existencia de correcciones o problemas en la planificación.|
| **Índice de riesgo (Risk Index)**| Porcentaje de tareas abiertas que están vencidas o cercanas a su fecha límite dentro de un proyecto.|
| **Tiempo por estado (State Time)** | Tiempo que una tarea permanece en cada estado del flujo de trabajo (nuevo, en progreso, cerrada, etc.).|
| **Trabajo en curso (WIP)** | Número de tareas abiertas asignadas a un empleado o proyecto en un momento determinado. |
| **Carga de trabajo (Workload)**                     | Cantidad de trabajo pendiente de un empleado calculada a partir de las horas restantes en sus tareas abiertas.   |