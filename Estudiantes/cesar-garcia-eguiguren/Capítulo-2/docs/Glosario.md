# Glosario

## Glosario General

| Término | Definición |
|---|---|
| **ERP Odoo** | Sistema de planificación de recursos empresariales utilizado por la empresa para gestionar proyectos, tareas, empleados, clientes y registros de horas. El módulo analítico consume sus datos en modo lectura. |
| **PostgreSQL** | Sistema de gestión de base de datos utilizado por Odoo para almacenar toda la información del ERP. |
| **Modo lectura** | Forma de acceso a la base de datos en la que el sistema solo consulta datos sin modificarlos, garantizando la integridad de la información del ERP. |
| **Proyecto** | Conjunto de tareas asociadas a un cliente o área de trabajo que permite organizar y controlar el desarrollo de actividades. |
| **Tarea** | Unidad de trabajo dentro de un proyecto que puede ser asignada a uno o varios empleados y sobre la que se registran partes de horas. |
| **Tarea raíz** | Tarea sin `parent_id`, que representa una unidad de trabajo principal dentro de un proyecto. |
| **Subtarea** | Tarea cuyo campo `parent_id` apunta a otra tarea, permitiendo descomponer el trabajo en actividades más pequeñas. |
| **Empleado** | Usuario del sistema que puede estar asignado a tareas y registrar partes de horas en el ERP. |
| **Departamento** | Unidad organizativa que agrupa empleados y permite analizar la actividad por áreas de trabajo. |
| **Cliente** | Entidad externa para la cual se desarrollan proyectos o tareas dentro del sistema. |
| **Parte de horas (Timesheet)** | Registro del tiempo trabajado por un empleado en una tarea o proyecto en una fecha concreta, almacenado en `account_analytic_line`. |
| **Etapa** | Estado funcional de una tarea dentro del flujo de trabajo del proyecto (kanban). Puede marcarse como cerrada. |
| **Responsable** | Empleado encargado del seguimiento de una tarea o proyecto. |
| **Endpoint** | Punto de acceso del backend que permite al frontend solicitar información mediante peticiones HTTP. |
| **API REST** | Interfaz de comunicación entre frontend y backend para obtener datos del ERP y generar métricas. |
| **Dashboard** | Interfaz visual que agrupa indicadores y métricas para facilitar la supervisión y la toma de decisiones. |
| **KPI** | Indicador clave de rendimiento utilizado para medir la eficiencia de empleados, tareas o proyectos. |
| **Métrica** | Valor calculado a partir de los datos del ERP que permite analizar el rendimiento del sistema o de la organización. |
| **JWT** | JSON Web Token. Token firmado con algoritmo HS256 que incluye el rol y el scope del usuario autenticado para controlar el acceso a los endpoints del sistema. |
| **Scope** | Conjunto de identificadores de empleados (`employee_ids`), departamentos (`department_ids`) y proyectos (`project_ids`) sobre los que el Responsable tiene acceso, calculado en el momento de la autenticación y embebido en el JWT. |
| **CTE recursivo** | Common Table Expression recursiva de PostgreSQL utilizada para calcular la jerarquía completa de empleados subordinados a partir del campo `hr_employee.parent_id`. |
| **MongoDB** | Base de datos NoSQL utilizada para almacenar snapshots históricos de métricas y configuraciones de gráficos, permitiendo análisis temporales y comparativos. |
| **Snapshot** | Copia inmutable del resultado calculado de una vista (métrica, gráfico o ficha de entidad) en una fecha concreta. Se almacena en MongoDB y puede reconstruirse tal cual se generó, sin necesidad de recalcular contra Odoo. |
| **Upsert** | Operación de base de datos que inserta un nuevo documento si no existe o actualiza el existente si ya hay uno con la misma clave, utilizada para garantizar que solo haya un snapshot por tipo, parámetros y día. |

---

## Glosario de Métricas

| Métrica | Definición |
|---|---|
| **Asistencia (Attendance)** | Compara las horas fichadas (`hr_attendance.worked_hours`) con las horas imputadas en partes analíticos (`account_analytic_line.unit_amount`) para medir la presencia real. Cobertura = `(imputadas / fichadas) × 100`. |
| **Tareas canceladas (Cancelled Tasks)** | Número o porcentaje de tareas cuya etapa se denomina «Cancelado», utilizado para analizar la estabilidad de la planificación y la calidad del proceso. |
| **Distribución por cliente (Client Distribution)** | Muestra cómo se reparten las horas registradas entre los distintos clientes, permitiendo analizar la dependencia de determinados clientes y la distribución de carga. |
| **Cumplimiento de plazos (Compliance)** | Porcentaje de tareas cerradas en las que `date_end ≤ date_deadline` sobre el total de tareas cerradas con ambas fechas informadas. |
| **Precisión de estimaciones (Estimation Accuracy)** | Ratio medio `(actual / planificado)` de las tareas cerradas de un responsable. Sesgo derivado: `subestima` (>110 %), `sobreestima` (<90 %), `preciso` (90–110 %). |
| **Tiempo de ciclo (Lead Time)** | Días medios transcurridos entre `date_assign` y `date_end` de las tareas cerradas, utilizado para medir la velocidad de ejecución del trabajo. |
| **Tiempo por prioridad (Priority Time)** | Horas medias invertidas en tareas cerradas agrupadas por nivel de prioridad (`priority = "0"` Normal / `priority = "1"` Urgente). |
| **Productividad (Productivity)** | Ratio `(planned_hours / actual_hours) × 100` para cada tarea cerrada con ambos valores mayores de cero. Valores >100 % indican que la tarea se completó en menos tiempo del estimado. |
| **Rentabilidad por horas (Profitability)** | Diferencia entre el coste estimado (`planned_hours × hourly_cost`) y el coste real (`worked_hours × hourly_cost`) de un proyecto. Porcentaje = `(diferencia / estimado) × 100`. |
| **Rentabilidad financiera** | Análisis basado en `account_analytic_line.amount`: suma de importes positivos (ingresos) menos importes negativos (gastos), agrupados por proyecto, cliente o responsable. Exclusivo del Director. |
| **Eficiencia del proyecto (Project Efficiency)** | Ratio `(total_planned_hours / total_actual_hours) × 100` a nivel de proyecto. Desviación en horas = `actual − planificado`; desviación porcentual = `(desviación / planificado) × 100`. |
| **Tasa de retrabajo (Rework Rate)** | Porcentaje de tareas cerradas que fueron reabiertas posteriormente, detectado mediante el análisis del historial de cambios de etapa en `mail_tracking_value`. |
| **Índice de riesgo (Risk Index)** | Porcentaje de tareas abiertas con `date_deadline` que están vencidas (`date_deadline < hoy`) o que han consumido ≥80 % del tiempo entre `date_assign` y `date_deadline`. |
| **Tiempo por estado (State Time)** | Horas medias que una tarea permanece en cada etapa del flujo Kanban, calculado a partir de los cambios consecutivos registrados en `mail_tracking_value`. |
| **Trabajo en curso (WIP)** | Número de tareas abiertas asignadas a un empleado en un momento dado. Umbrales: óptimo ≤3, aceptable ≤5, sobrecargado >5. |
| **Carga de trabajo (Workload)** | `(Σ horas_pendientes / 40 h referencia) × 100`, donde las horas pendientes son `max(planned_hours − worked_hours, 0)` por tarea abierta asignada. Estados: sobrecargado >120 %, normal 70–120 %, subcargado <70 %. |