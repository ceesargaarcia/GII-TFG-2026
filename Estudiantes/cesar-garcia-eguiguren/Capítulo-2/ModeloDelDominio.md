# Modelo del Dominio

El sistema se compone de una aplicación web analítica conectada a la base de datos del ERP en modo lectura y de una base de datos documental propia para persistir las capturas históricas de los paneles calculados. Su función principal es consumir los datos operativos del ERP Odoo, estructurarlos y transformarlos en información útil para la toma de decisiones, permitiendo además guardar el estado de cualquier panel como una *snapshot* consultable posteriormente sin necesidad de recalcular sobre Odoo.

Por ello, el modelo del dominio se apoya en las entidades existentes en Odoo, garantizando coherencia con la base de datos corporativa y evitando inconsistencias. La única familia de entidades añadidas por el propio sistema es la de las **capturas históricas** (snapshots), que se guardan en una base documental independiente y son ajenas al ciclo de vida de las entidades de Odoo.

En las siguientes secciones se presentan los diferentes diagramas que conforman el modelo del dominio: el diagrama de clases, el diagrama de objetos, el diagrama de estados y el glosario de términos, proporcionando una visión completa de la estructura conceptual del sistema.

## Diagrama de Clases

En el contexto de Netkia, el diagrama de clases está directamente alineado con la estructura de datos del ERP Odoo v16, lo que garantiza la consistencia entre la información almacenada en el sistema de gestión y los indicadores generados por el módulo analítico. Cada clase del dominio operativo representa una entidad real del entorno organizativo —clientes, proyectos, tareas, empleados o departamentos— y refleja los atributos necesarios para calcular métricas de productividad, carga de trabajo y rentabilidad.

![Diagrama de clases](./imagenes/diagramaDeClases.png)

Las relaciones entre las clases permiten entender la jerarquía y dependencia entre los elementos del sistema. Por ejemplo, un cliente puede tener múltiples proyectos, cada proyecto puede contener varias tareas, y cada tarea puede estar asociada a diferentes empleados y partes de horas. Esta estructura refleja el flujo natural del trabajo dentro de la empresa y facilita la obtención de indicadores agregados.

## Diagrama de Objetos

El diagrama de objetos complementa al diagrama de clases mostrando una instancia concreta del modelo del dominio en un escenario realista dentro de Netkia SL. Mientras que el diagrama de clases describe la estructura general del sistema, el diagrama de objetos permite observar cómo se materializan esas clases en situaciones reales de uso.

![Diagrama de objetos](./imagenes/diagramaDeObjetos.png)

En este caso, se representa un proyecto real con sus correspondientes tareas, subtareas, empleados asignados y partes de horas registrados. Esta representación facilita la comprensión del funcionamiento del sistema, ya que muestra cómo los datos se relacionan en la práctica y cómo se organizan dentro del ERP.

El objetivo principal de este diagrama es ilustrar el flujo de información desde el cliente hasta las tareas y los registros de horas, permitiendo visualizar de forma clara la interacción entre los distintos elementos del dominio. De esta forma, se puede entender cómo el sistema es capaz de generar métricas a partir de los datos reales de trabajo, como la productividad, la carga de trabajo o la eficiencia de los proyectos.

## Diagrama de Estados

En este apartado se presentan dos diagramas de estados: el primero describe el comportamiento general del sistema desde el punto de vista de la sesión del usuario, y el segundo representa el ciclo de vida de una tarea dentro del sistema de gestión de proyectos.

### Diagrama de estados del sistema
![Diagrama de estados](./imagenes/diagramaDeEstados.png)

Este diagrama representa el comportamiento general del sistema desde el punto de vista de la sesión del usuario, describiendo cómo transita entre los estados de autenticación, navegación activa y cierre de sesión. El mismo ciclo aplica tanto al frontend principal como al visor de capturas: ambos reutilizan el contexto de autenticación y comparten el mismo esquema de sesión.

El sistema parte del estado **NoAutenticado**, que es el estado inicial siempre que no exista una sesión activa. Desde aquí se abren dos caminos: si el navegador detecta una sesión previa almacenada, se pasa automáticamente al estado **ValidandoToken**, donde se comprueba si la sesión sigue siendo válida; si en cambio el usuario introduce sus credenciales y pulsa Acceder, se transita al estado **Autenticando**.

Si la autenticación falla —por credenciales incorrectas, usuario inexistente o rol sin acceso— el sistema pasa al estado **ErrorAuth**, donde se muestra el mensaje de error en el formulario de login sin perder los datos introducidos, permitiendo al usuario reintentar directamente. Si la autenticación es correcta, o si la sesión almacenada se valida con éxito, el sistema transita a **SesionActiva**.

Dentro de **SesionActiva** se modelan los estados propios de la navegación en la aplicación. Cuando el usuario navega a cualquier ruta, el sistema pasa a **CargandoPagina**. Si alguna petición devuelve un error, se transita a **ErrorCarga**, desde donde el usuario puede reintentar la carga. Si las peticiones se resuelven correctamente, el sistema pasa a **VistaActiva**, estado en el que el usuario puede interactuar con los datos. Desde cualquier vista activa, navegar a otra sección reinicia el ciclo de carga.

Las capturas, al ser documentos inmutables por día, no modifican el diagrama de estados del sistema: la operación de guardar una captura (CU-17) se resuelve dentro de **VistaActiva** como una petición puntual al backend y no genera un estado propio de navegación. Desde el visor de capturas, la navegación entre la home, los listados de colecciones y la ficha de detalle sigue el mismo ciclo `CargandoPagina → VistaActiva`.

La sesión activa finaliza por cierre de sesión voluntario (botón "Cerrar sesión") o por expiración automática de la misma.

### Diagrama de estados de una tarea
![Diagrama de estados de tareas](./imagenes/diagramaDeEstadosDeTareas.png)
Este diagrama refleja las transiciones más comunes dentro del ciclo de vida de una tarea, como la creación, su asignación a un empleado, el inicio del trabajo, su finalización o su cancelación. Además, contempla la posibilidad de reapertura de tareas cerradas, lo que permite modelar situaciones de retrabajo o correcciones dentro del sistema.


## Requisitos del Sistema

### Requisitos Funcionales

| RF | Descripción | CU asociado |
|---|---|---|
| RF-01 | Autenticar al usuario, determinar su rol y calcular el ámbito organizativo que le corresponde | CU-01 |
| RF-02 | Listar empleados paginados con búsqueda por nombre y filtros por departamento y estado | CU-02 |
| RF-03 | Obtener el resumen detallado de un empleado con indicadores de carga, WIP, productividad y tareas | CU-03 |
| RF-04 | Listar los departamentos del ámbito del actor | CU-04 |
| RF-05 | Obtener el resumen de un departamento con su plantilla y carga agregada | CU-05 |
| RF-06 | Listar los proyectos del ámbito con filtros básicos | CU-06 |
| RF-07 | Obtener el resumen de un proyecto con indicadores de eficiencia, riesgo, rentabilidad y equipo | CU-07 |
| RF-08 | Listar tareas con filtros combinables por proyecto, responsable, estado y prioridad | CU-08 |
| RF-09 | Consultar el detalle de una tarea con historial, imputaciones y subtareas | CU-09 |
| RF-10 | Consultar cualquiera de las métricas operativas con parámetros dinámicos | CU-10 |
| RF-11 | Calcular la métrica de carga de trabajo en modo agregado de equipo cuando no se especifica un empleado concreto | CU-10 (variante equipo) |
| RF-12 | Visualizar gráficos de evolución y distribución de tareas | CU-11 |
| RF-13 | Visualizar el gráfico de horas por cliente (solo Director) | CU-11 |
| RF-14 | Comparar asistencia fichada frente a horas imputadas por empleado y período | CU-12 |
| RF-15 | Analizar rentabilidad financiera por período con descomposición por proyecto, cliente y responsable (solo Director) | CU-13 |
| RF-16 | Consultar líneas analíticas de desglose parametrizado por ámbito (proyecto o cliente) | CU-14 |
| RF-17 | Realizar una búsqueda global en tareas, proyectos y empleados con filtrado de ámbito | CU-15 |
| RF-18 | Cerrar sesión e invalidar credenciales locales | CU-16 |
| RF-19 | Guardar una captura de una vista calculada (métrica, gráfico o entidad) con semántica de actualización diaria | CU-17 |
| RF-20 | Listar capturas con paginación y filtros por tipo y rango de fechas | CU-18 |
| RF-21 | Consultar el detalle de una captura reconstruyendo la vista a partir de los datos guardados | CU-19 |
| RF-22 | Eliminar una captura de forma permanente | CU-20 |

---

### Requisitos No Funcionales (Suplementarios)

| ID | Categoría | Descripción |
|---|---|---|
| RNF-01 | **Seguridad — Solo lectura sobre el ERP** | El módulo accede a la base de datos del ERP únicamente en modo lectura. Ninguna operación de escritura, actualización o eliminación está permitida sobre esa base. |
| RNF-02 | **Seguridad — Autenticación por sesión** | Todos los recursos del sistema están protegidos mediante un mecanismo de sesión firmada con rol y ámbito embebidos. La sesión caduca tras una jornada laboral; cuando caduca, el frontend detecta la expiración y redirige al inicio de sesión. |
| RNF-03 | **Seguridad — Control de acceso por roles** | Los recursos del Director están vedados al resto de roles. Los recursos del Responsable aplican automáticamente el filtro de ámbito que le corresponde, sin que el actor pueda alterarlo. |
| RNF-04 | **Rendimiento** | Consultas individuales de métricas por debajo de 2 segundos para hasta 20 usuarios concurrentes. Los gráficos complejos admiten hasta 5 segundos. |
| RNF-05 | **Disponibilidad** | Sistema disponible en horario laboral (08:00–20:00 h, de lunes a viernes). Mantenimiento fuera de horario. |
| RNF-06 | **Mantenibilidad — Arquitectura en capas** | El backend se organiza en cuatro capas separadas: rutas, servicios, acceso a datos y modelos. Las rutas validan y delegan; los servicios contienen la lógica de negocio; la capa de datos contiene las consultas. |
| RNF-07 | **Extensibilidad** | Añadir una nueva métrica consiste en crear un servicio nuevo y registrar una ruta adicional, sin modificar el código existente. El mismo criterio aplica al subsistema de capturas: añadir un nuevo subtipo implica una nueva colección documental con su índice único y un nuevo renderizador en el visor. |
| RNF-08 | **Compatibilidad con el ERP** | Compatible con el ERP corporativo sin necesidad de módulos adicionales ni modificación de su esquema. |
| RNF-09 | **Compatibilidad con navegadores** | Chrome, Firefox y Edge en sus dos últimas versiones. |
| RNF-10 | **Internacionalización** | Los nombres multilingües procedentes del ERP se muestran en español, con fallback automático a inglés cuando no existe traducción disponible. |
| RNF-11 | **Trazabilidad de datos** | Las métricas de retrabajo y los tiempos por estado se basan en el historial inmutable de cambios que mantiene el propio ERP, lo que garantiza la trazabilidad. |
| RNF-12 | **Configuración por entorno** | La conexión con las bases de datos, la clave de firma de sesiones y los parámetros del servidor se establecen mediante variables de entorno. Sin credenciales en código. |
| RNF-13 | **Usabilidad — Tiempo de respuesta percibido** | Indicadores de carga durante las peticiones, estados de error con opción de reintento y paginación en listados largos. |
| RNF-14 | **Usabilidad — Adaptación al rol** | La navegación y las opciones disponibles se adaptan automáticamente al rol del actor. |
| RNF-15 | **Escalabilidad** | Pool de conexiones gestionado y paginación en servidor para listados masivos. |
| RNF-16 | **Persistencia documental de capturas** | El subsistema de capturas utiliza una base de datos documental independiente de la base relacional del ERP. Cada colección de capturas mantiene un índice único sobre su clave compuesta, lo que garantiza a nivel de base de datos la restricción "una captura por tipo, parámetros y día". El acceso a esta base es de lectura y escritura, a diferencia del acceso al ERP, que es exclusivamente de lectura. |
| RNF-17 | **Dos frontends, un mismo esquema de autenticación** | El sistema expone dos aplicaciones independientes —el frontend principal y el visor de capturas— que consumen el mismo backend y comparten el mecanismo de autenticación. La separación es intencional: el principal resuelve el caso de uso operativo sobre datos en vivo; el visor resuelve el caso de uso histórico sobre las capturas guardadas. |