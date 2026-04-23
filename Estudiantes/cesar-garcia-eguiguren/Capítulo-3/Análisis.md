# Disciplina de Análisis

## Índice

1. [Análisis de la Arquitectura](#1-análisis-de-la-arquitectura)
   - 1.1 [Visión general del módulo](#11-visión-general-del-módulo)
   - 1.2 [Diagrama de arquitectura del sistema](#12-diagrama-de-arquitectura-del-sistema)
   - 1.3 [Restricción fundamental: solo lectura sobre Odoo](#13-restricción-fundamental-solo-lectura-sobre-odoo)
2. [Análisis de Casos de Uso](#2-análisis-de-casos-de-uso)
   - 2.1 [Actores](#21-actores)
   - 2.2 [Casos de uso del sistema](#22-casos-de-uso-del-sistema)
3. [Análisis de Clases](#3-análisis-de-clases)
   - 3.1 [Capas de la solución](#31-capas-de-la-solución)
   - 3.2 [Identificación de clases de análisis](#32-identificación-de-clases-de-análisis)
   - 3.3 [Relaciones entre clases del dominio](#33-relaciones-entre-clases-del-dominio)
4. [Análisis de Paquetes](#4-análisis-de-paquetes)
   - 4.1 [Estructura de paquetes del backend](#41-estructura-de-paquetes-del-backend)
   - 4.2 [Justificación de la estructura](#42-justificación-de-la-estructura)
   - 4.3 [Paquetes del frontend principal y del visor](#43-paquetes-del-frontend-principal-y-del-visor)


---

## 1. Análisis de la Arquitectura

### 1.1 Visión general del módulo

Netkia Analytics es un módulo externo de analítica construido sobre Odoo v16 Enterprise. Consta de **tres capas perfectamente desacopladas** y dos bases de datos (la relacional del ERP y la documental de capturas) que se consultan/modifican exclusivamente a través del backend:

- **Backend único** en FastAPI y Python, que accede al ERP a través de su base relacional en **solo lectura** y persiste las capturas históricas en una base documental independiente en modo lectura/escritura.
- **Frontend principal** en React (puerto 3000), que calcula todos los paneles en tiempo real sobre los datos del ERP y permite *generar* capturas desde cualquier vista calculada (CU-17).
- **Visor de capturas** en React (puerto 3001), aplicación independiente de solo lectura que lista, visualiza y elimina capturas (CU-18, CU-19, CU-20). Reutiliza el mismo esquema de autenticación que el frontend principal.

La separación entre los dos frontends es intencional: el principal resuelve el caso de uso *operativo* (decisiones hoy sobre datos vivos); el visor resuelve el caso de uso *histórico* (consultar el estado de un panel en un día pasado).


### 1.2 Diagrama de arquitectura del sistema

El diagrama de componentes muestra la arquitectura física desplegada: el frontend principal y el visor se comunican con el mismo backend a través de HTTP; el backend accede a la base de datos del ERP en modo solo lectura y, adicionalmente, a la base documental para las colecciones de capturas en modo lectura/escritura. El backend se organiza en capas (rutas, servicios, repositorios y modelos) que siguen principios de separación de responsabilidades y una única razón para cambiar.

![Diagrama de Componentes](./imagenes/paquetesSistema.png)

### 1.3 Restricción fundamental: solo lectura sobre Odoo

El acceso exclusivo de lectura a la base de datos del ERP determina varias decisiones de arquitectura: no se utilizan patrones de escritura ni transacciones de modificación sobre la base relacional; todos los modelos de datos mapean tablas existentes sin añadir columnas; y la lógica del módulo operativo se concentra en optimizar consultas y calcular métricas, no en persistir estado propio.

La única excepción a esta restricción es el subsistema de capturas, que persiste sus propios documentos en la base documental. Esa base queda por completo fuera del espacio de Odoo: no existe integridad referencial real entre ambas, y la correspondencia entre una captura de entidad y la entidad operativa que retrata se mantiene de forma lógica mediante un campo de tipo y un identificador. De este modo, la restricción de solo lectura sobre el ERP se preserva íntegramente y el almacenamiento de capturas queda encapsulado en un subsistema independiente.

---

## 2. Análisis de Casos de Uso

Esta sección describe qué sucede en cada caso de uso desde el punto de vista del actor. Para cada CU se indica el actor que lo inicia, los datos de entrada que proporciona y la información que el sistema devuelve. Siguiendo el principio RUP de trazabilidad entre disciplinas, en esta fase se profundiza en el flujo principal del caso de uso, preparando el terreno para el diseño detallado que se abordará en el capítulo siguiente.

Tras la aplicación de los principios de consolidación RUP documentados en el Capítulo 2, el sistema cuenta con **20 casos de uso** organizados en 10 paquetes funcionales.

### 2.1 Actores

| Actor | CUs disponibles | Descripción |
|---|---|---|
| **Director** | 20 | Acceso total. Ve datos de toda la organización: todos los empleados, proyectos, departamentos y métricas financieras. |
| **Responsable** | 17 | Acceso restringido a su ámbito (empleados de su equipo, proyectos que gestiona, departamentos a su cargo). No puede eliminar snapshots ni acceder a los casos de uso exclusivos de Rentabilidad Financiera (CU-13 y CU-14). |

### 2.2 Casos de uso del sistema

**CU-01 — Autenticarse**
- Actor: Director, Responsable
- Entrada: credenciales de usuario (login y contraseña del ERP)
- Salida: sesión activa con rol y ámbito organizativo embebidos; perfil del usuario. El sistema calcula en este momento la lista de empleados, departamentos y proyectos accesibles para el Responsable, y la conserva durante toda la sesión.

**CU-02 — Listar empleados**
- Actor: Director, Responsable
- Entrada: filtros opcionales (departamento, búsqueda por nombre, estado activo/inactivo, ordenación, paginación)
- Salida: lista paginada de empleados con nombre, cargo, departamento, correo y coste por hora. El sistema aplica automáticamente el filtro de ámbito del Responsable antes de devolver los resultados.

**CU-03 — Ver resumen de empleado**
- Actor: Director, Responsable
- Entrada: identificador del empleado
- Salida: ficha del empleado más indicadores de carga de trabajo (porcentaje de ocupación, horas pendientes), trabajo en curso (tareas en paralelo), productividad de los últimos 30 días y contadores de tareas por estado. Antes de calcular, el sistema verifica que el empleado pertenece al ámbito del actor.

**CU-04 — Listar departamentos**
- Actor: Director, Responsable
- Entrada: filtros opcionales de búsqueda y ordenación
- Salida: lista de departamentos activos con nombre, responsable y jerarquía, filtrada por ámbito si el actor es Responsable.

**CU-05 — Ver detalle de departamento**
- Actor: Director, Responsable
- Entrada: identificador del departamento
- Salida: ficha del departamento junto con la carga de trabajo del equipo (porcentaje por empleado, sobrecargados, subcargados, sin tareas) y listado de empleados.

**CU-06 — Listar proyectos**
- Actor: Director, Responsable
- Entrada: ninguna
- Salida: lista de proyectos activos con nombre, cliente y código. El Responsable solo ve los proyectos que gestiona.

**CU-07 — Ver detalle de proyecto**
- Actor: Director, Responsable
- Entrada: identificador del proyecto
- Salida: ficha del proyecto con métricas de eficiencia, riesgo y rentabilidad por horas, listado de tareas y equipo asignado. El sistema compone estas métricas invocando los servicios de cálculo correspondientes.

**CU-08 — Listar tareas**
- Actor: Director, Responsable
- Entrada: filtros combinables (estado, etapa, proyecto, departamento, fechas, empleado, responsable, solo tareas raíz, paginación, ordenación)
- Salida: lista paginada de tareas con nombre, etapa, horas estimadas, fecha límite y estado. Las tareas filtradas por empleado incluyen horas trabajadas, pendientes y productividad, adaptadas al modo (pendiente, completada o asignada).

**CU-09 — Ver detalle de tarea**
- Actor: Director, Responsable
- Entrada: identificador de la tarea
- Salida: toda la información de la tarea (fechas, horas, etapa, responsable, empleados asignados, subtareas, progreso de horas y productividad).

**CU-10 — Consultar métrica operativa**
- Actor: Director, Responsable
- Entrada: nombre de la métrica (productividad, cumplimiento, WIP, carga de trabajo, riesgo, retrabajo, estimación, lead time, tiempo por estado, tareas canceladas, tiempo por prioridad, eficiencia de proyecto, distribución por cliente…) y los parámetros contextuales que esa métrica requiera (empleado, proyecto, departamento, rango de fechas, modo solo tareas raíz)
- Salida: indicador calculado con su representación y gráficos de apoyo específicos de la métrica seleccionada. El modo carga de trabajo admite parámetros individuales (empleado concreto) o agregados de equipo (departamento o sin parámetro). El sistema verifica el ámbito del actor sobre los parámetros antes de calcular.

**CU-11 — Consultar gráficos analíticos**
- Actor: Director, Responsable
- Entrada: rango de fechas, agrupación temporal (semanal o mensual), filtros de entidad (empleado, departamento o proyecto)
- Salida: gráficos de evolución temporal (tareas abiertas, cerradas y vencidas), distribución de tareas por estado y, solo para el Director, distribución de horas por cliente.

**CU-12 — Comparar asistencia vs partes**
- Actor: Director, Responsable (solo modo equipo global)
- Entrada: rango de fechas, filtros opcionales (departamento, empleado o responsable)
- Salida: horas fichadas frente a horas imputadas por empleado, diferencia, porcentaje de cobertura y estado (correcto, revisar o alerta). Al seleccionar un empleado concreto, el sistema muestra la serie diaria correspondiente.

**CU-13 — Consultar rentabilidad financiera ★**
- Actor: Director (exclusivo)
- Entrada: rango de fechas y modo de análisis (global, por proyecto o por responsable)
- Salida: ingresos totales, gastos totales, resultado neto, rentabilidad en porcentaje, estado por proyecto y descomposición por cliente o responsable cuando aplica. El sistema rechaza el acceso al resto de roles antes de calcular.

**CU-14 — Consultar líneas analíticas ★**
- Actor: Director (exclusivo)
- Entrada: ámbito (proyecto o cliente), identificador del objetivo y rango de fechas
- Salida: desglose de líneas analíticas en ingresos y gastos. Caso de uso único que unifica el desglose por proyecto (líneas de un proyecto concreto) y por cliente (líneas agregadas de todos los proyectos del cliente), invocable exclusivamente vía `<<extend>>` desde CU-13.

**CU-15 — Buscar globalmente**
- Actor: Director, Responsable
- Entrada: término de búsqueda (mínimo 2 caracteres), tipo de entidad (todos, tareas, proyectos o empleados), límite
- Salida: coincidencias de tareas, proyectos y empleados con nombre, código y metadatos, filtradas por el ámbito del actor.

**CU-16 — Cerrar sesión**
- Actor: Director, Responsable
- Entrada: ninguna
- Salida: sesión invalidada. El frontend borra la sesión localmente y redirige al login.

**CU-17 — Guardar snapshot**
- Actor: Director, Responsable
- Entrada: tipo de captura (métrica, gráfico o entidad), parámetros actuales de la vista calculada y datos ya calculados en pantalla
- Salida: identificador de la captura persistida e indicador de si se ha creado o actualizado. Si ya existe una captura para el mismo tipo, parámetros y día, se actualiza en lugar de crearse una nueva (semántica de actualización diaria). El sistema registra al actor responsable junto con la fecha de creación o actualización.

**CU-18 — Listar snapshots**
- Actor: Director, Responsable
- Entrada: colección (métricas, gráficos o entidades), filtros opcionales por tipo y rango de fechas, parámetros de paginación
- Salida: tabla paginada con tipo, fecha, parámetros resumidos, última actualización y actor responsable.

**CU-19 — Consultar detalle de snapshot**
- Actor: Director, Responsable
- Entrada: identificador de la captura y tipo de colección
- Salida: ficha completa con metadatos (fecha, autor de creación y última actualización), parámetros usados y la vista reconstruida por el renderizador correspondiente al subtipo de la captura.

**CU-20 — Eliminar snapshot**
- Actor: Director, Responsable
- Entrada: identificador de la captura y tipo de colección
- Salida: confirmación de eliminación permanente del documento.

> **Nota sobre consolidaciones.** CU-10 absorbe las métricas operativas y el panel de supervisión de equipo; CU-14 unifica la consulta de líneas de proyecto y líneas de cliente; y CU-17 absorbe la semántica de actualización mediante semántica de actualización diaria.

---

## 3. Análisis de Clases

### 3.1 Capas de la solución

La solución sigue una **arquitectura por capas** en el backend y dos aplicaciones independientes (frontend principal y visor) sobre el mismo backend. Las dependencias fluyen exclusivamente hacia abajo (nunca ascienden).

| Capa | Responsabilidad | Cambios por |
|------|-----------------|-------------|
| **Rutas** | Enrutamiento HTTP FastAPI | Nuevos endpoints, nueva interfaz HTTP |
| **Servicios** | Lógica de negocio, cálculos, orquestación | Reglas de negocio, fórmulas, algoritmos |
| **Repositorios** | Acceso a datos, queries SQL o MongoDB | Cambios en estructura de tablas Odoo o colecciones Mongo |
| **Modelos** | Mapeo de tablas Odoo con SQLAlchemy | Presencia/ausencia de columnas en Odoo |
| **Schemas** | Contratos JSON Pydantic (incluidos los de snapshot) | Nuevos campos en respuestas API |
| **Core** | Configuración, autenticación, conexiones a PostgreSQL y MongoDB | Cambios globales de configuración |
| **Utils** | Constantes y utilidades compartidas | Nuevas constantes, nuevos helpers |

Cada capa tiene **una única razón para cambiar** (Principio de Responsabilidad Única).
Además, el backend se conecta a dos bases de datos distintas: la relacional del ERP en modo solo lectura, y la documental de capturas en modo lectura/escritura. El acceso a ambas se encapsula en la capa de repositorios, que es la única que interactúa directamente con las bases de datos.
Aunque el subsistema de capturas persiste en una base de datos distinta, respeta la misma arquitectura: tiene su propia ruta, su servicio, su repositorio y su esquema de datos. No introduce un modelo ORM porque la base documental no requiere mapeo relacional; la serialización y deserialización de los documentos se realiza directamente a través del esquema correspondiente.

### 3.2 Identificación de clases de análisis

Siguiendo la metodología RUP, las clases se clasifican en tres estereotipos:

**Clases Modelo (Entidad) — tablas del ERP:**

| Clase | Tabla del ERP | Datos principales |
|---|---|---|
| Tarea | `project_task` | id, horas planificadas, cerrada, etapa, proyecto, responsable, fecha límite |
| Etapa | `project_task_type` | id, nombre, cerrada |
| Empleado | `hr_employee` | id, nombre, coste por hora, departamento, usuario |
| Departamento | `hr_department` | id, nombre, responsable, jerarquía |
| Proyecto | `project_project` | id, nombre, cliente, fecha de inicio |
| Parte de horas | `account_analytic_line` | id, cantidad, importe, tarea, empleado, fecha |
| Asistencia | `hr_attendance` | id, empleado, entrada, salida, horas trabajadas |
| Asignación de tarea | `project_task_user_rel` | tarea, usuario |
| Usuario | `res_users` | id, login |
| Cliente | `res_partner` | id, nombre |
| Mensaje de registro | `mail_message` | id, modelo, registro, fecha |
| Cambio registrado | `mail_tracking_value` | mensaje asociado, valor anterior, valor nuevo |

**Clases Modelo (Entidad) — colecciones documentales:**

| Clase | Colección | Datos principales |
|---|---|---|
| Captura de Métrica | colección de métricas | identificador, nombre de métrica, parámetros, fecha, datos, fechas de creación/actualización, autores |
| Captura de Gráfico | colección de gráficos | identificador, nombre de gráfico, parámetros, fecha, datos, fechas de creación/actualización, autores |
| Captura de Entidad | colección de entidades | identificador, tipo y id de entidad, fecha, datos, fechas de creación/actualización, autores |
| Autor de Captura | *referencia embebida* | id de usuario, id de empleado, nombre, rol |

**Clases Vista (Interfaz) — páginas de las aplicaciones:**

| Aplicación | Clase | Actor | CU asociados |
|---|---|---|---|
| Principal | `Login` | Ambos | CU-01 |
| Principal | `Overview` | Ambos | Panel de inicio |
| Principal | `Employees` / `EmployeeDetail` | Ambos | CU-02, CU-03 |
| Principal | `Departments` / `DepartmentDetail` | Ambos | CU-04, CU-05 |
| Principal | `Projects` / `ProjectDetail` | Ambos | CU-06, CU-07 |
| Principal | `Tasks` / `TaskDetail` | Ambos | CU-08, CU-09 |
| Principal | `Metrics` / `MetricDetail` | Ambos | CU-10 (modo individual) |
| Principal | `Manager` | Ambos | CU-10 (modo agregado de equipo) |
| Principal | `Attendance` | Ambos | CU-12 |
| Principal | `Rentability` | Director | CU-13, CU-14 |
| Principal | `Charts` | Ambos | CU-11 |
| Principal | `Search` | Ambos | CU-15 |
| Visor | `Login` | Ambos | CU-01 (reutiliza el mismo esquema) |
| Visor | `Home` | Ambos | Resumen global y atajo a las últimas capturas |
| Visor | `MetricSnapshots` | Ambos | CU-18 (colección de métricas) |
| Visor | `ChartSnapshots` | Ambos | CU-18 (colección de gráficos) |
| Visor | `EntitySnapshots` | Ambos | CU-18 (colección de entidades) |
| Visor | `SnapshotDetail` | Ambos | CU-19 y CU-20 |

**Clases Controlador — rutas del backend:**

| Clase | CU coordinados | Servicio al que delega |
|---|---|---|
| `auth.router` | CU-01, CU-16 | Servicio de autenticación y servicio de ámbito |
| `employee.router` | CU-02, CU-03 | Servicio de empleados |
| `department.router` | CU-04, CU-05 | Servicio de departamentos |
| `project.router` | CU-06, CU-07 | Servicio de proyectos |
| `task.router` | CU-08, CU-09 | Servicio de tareas |
| `metrics.router` | CU-10 y CU-12 | Servicios de métricas y servicio de asistencia |
| `dashboards.router` | CU-03, CU-05, CU-07, CU-10 en modo equipo, CU-13 y CU-14 | Servicio de dashboards y servicio de rentabilidad |
| `charts.router` | CU-11 | Servicio de gráficos |
| `search.router` | CU-15 | Servicio de búsqueda |
| `snapshots.router` | CU-17, CU-18, CU-19, CU-20 | Servicio de capturas |


### 3.3 Relaciones entre clases del dominio

| Relación | Tipo | Cardinalidad |
|---|---|---|
| Proyecto → Tarea | Composición | 1 a 0..* |
| Tarea → Etapa | Asociación | * a 1 |
| Tarea → Tarea | Auto-referencia (subtareas) | * a 0..1 |
| Empleado → Departamento | Agregación | * a 0..1 |
| Tarea → Empleado (responsable) | Asociación | * a 0..1 |
| Tarea ↔ Usuario | Asociación M:N a través de Asignación | * a * |
| Parte de horas → Tarea | Asociación | * a 0..1 |
| Parte de horas → Empleado | Asociación | * a 1 |
| Proyecto → Cliente | Asociación | * a 0..1 |
| Cambio registrado → Mensaje de registro | Asociación | * a 1 |
| Captura → Autor de Captura | Agregación (referencia embebida) | 1 a 0..1 (creación y actualización) |
| Captura de Entidad → Empleado / Departamento / Proyecto / Tarea | Referencia blanda mediante tipo e identificador | * a 1 |

La referencia blanda entre la captura de entidad y las entidades operativas del ERP es intencional: la base documental y la base relacional son independientes, por lo que no existe una clave foránea real. La unicidad diaria se garantiza mediante los índices únicos sobre la clave compuesta de cada colección.

---

## 4. Análisis de Paquetes

### 4.1 Estructura de paquetes del backend

El backend se distribuye en carpetas siguiendo el criterio de **cohesión funcional**: cada paquete agrupa módulos con la misma naturaleza de responsabilidad. La estructura relevante es:

```
app/
├── core/
│   ├── config.py        → Configuración por entorno
│   ├── database.py      → Conexión a la base relacional del ERP (solo lectura)
│   ├── mongo.py         → Cliente de la base documental (lectura/escritura)
│   └── security.py      → Generación y validación de sesiones + guards de rol
├── models/              → Clases del dominio operativo (una por entidad del ERP)
├── schemas/             → Contratos de entrada/salida del backend
│   └── snapshot.py      → Esquemas de las capturas de métrica, gráfico y entidad
├── repositories/
│   ├── employee.py      → Acceso al dominio de empleados
│   ├── project.py       → Acceso al dominio de proyectos
│   ├── department.py    → Acceso al dominio de departamentos
│   ├── task.py          → Acceso al dominio de tareas
│   ├── tracking.py      → Acceso al dominio de partes de horas
│   ├── attendance.py    → Acceso al dominio de fichajes
│   ├── snapshot.py      → Acceso a las tres colecciones documentales
│   └── metrics/         → Sub-paquete: una función por métrica operativa
├── services/
│   ├── metric/          → Implementaciones por métrica
│   ├── dashboard.py     → Composición para los resúmenes de entidad
│   ├── chart.py         → Datos para los gráficos analíticos
│   ├── search.py        → Búsqueda global
│   ├── auth.py          → Autenticación y construcción del ámbito
│   └── snapshot.py      → Orquestación del subsistema de capturas
├── routes/
│   ├── auth.py
│   ├── resources/       → Endpoints de las entidades operativas
│   ├── metrics/         → Endpoints de las métricas operativas
│   ├── charts/          → Endpoints de los gráficos analíticos
│   ├── dashboards/      → Endpoints de los resúmenes y de rentabilidad
│   └── snapshots.py     → Endpoints para guardar, listar, consultar y eliminar capturas
└── utils/               → Paginación, traducción de nombres multilingües y validaciones de ámbito
```

### 4.2 Justificación de la estructura

**`core/`** agrupa todo lo que es infraestructura transversal: configuración, generación y validación de sesiones, pool de conexiones a la base del ERP y cliente a la base documental, y guardas de rol. Cambia solo cuando cambia la infraestructura, no el negocio. La adición del módulo de conexión documental respeta este criterio: aísla el cliente en un único punto, independiente del acceso a la base relacional.

**`models/`** contiene exclusivamente el mapeo sobre el dominio operativo del ERP. Ningún modelo ejecuta lógica; solo declara columnas y relaciones. No se añaden modelos para el subsistema de capturas porque la base documental no requiere tal mapeo: la serialización se delega a la capa de esquemas.

**`repositories/`** encapsula cada consulta como una función pura que recibe una sesión y devuelve datos crudos. El sub-paquete `repositories/metrics/` extiende esta idea con un submódulo por métrica, evitando que un único fichero acumule consultas heterogéneas. El repositorio de capturas es el único que no opera sobre la base relacional: ejecuta directamente operaciones de inserción, lectura, actualización, listado paginado y borrado sobre las tres colecciones documentales a través del cliente de infraestructura.

**`services/`** aplica las reglas de negocio sobre los datos recuperados por los repositorios. El sub-paquete `services/metrics/` sigue la misma granularidad que `repositories/metrics/`: una clase, una métrica, una razón de cambio. El servicio de capturas añade responsabilidades propias del subsistema: normalizar parámetros, calcular el resumen único que identifica a la captura, construir el autor a partir de la sesión del usuario, fijar la fecha y decidir entre insertar o actualizar (semántica de actualización diaria) según la existencia previa.

**`routes/`** actúa como capa de entrada HTTP. Las rutas no contienen lógica de negocio: validan parámetros, comprueban la autenticación y delegan en el servicio correspondiente. La ruta de capturas sigue esta norma y expone tres familias de endpoints homogéneas (una por colección) sobre la misma estructura de operaciones.

**`utils/`** recoge funciones reutilizables que no pertenecen a ningún dominio: paginación, extracción de nombres multilingües, ordenación de diccionarios y validaciones de rango de fechas.

### 4.3 Paquetes del frontend principal y del visor

Aunque el diseño de calidad recae principalmente sobre el backend, los frontends también siguen una estructura orientada a la separación de responsabilidades. El frontend principal y el visor son **dos aplicaciones independientes** que comparten contexto de autenticación pero no código de presentación.

**Frontend principal (`frontend/`):**

```
src/
├── api/            → Un módulo por dominio (empleados, tareas, métricas,gráficos, rentabilidad, capturas, búsqueda, autenticación)
├── components/     → Componentes reutilizables (Card, Table, KpiCard, Sidebar,botón de guardado de captura...)
├── context/        → Estado global de autenticación
├── hooks/          → Lógica reutilizable
├── pages/          → Una página por recurso o funcionalidad
├── styles/         → Variables CSS globales y tokens de diseño
└── utils/          → Formateadores y helpers
```

Todas las páginas calculadas (detalle de métrica, gráficos, rentabilidad y las fichas de entidad) integran el botón de guardado de captura, que llama al módulo de API correspondiente para disparar CU-17 desde el contexto actual de la vista.

**Visor de capturas:**

```
src/
├── api/
│   ├── auth.js             → Mismo contrato que el frontend principal
│   └── snapshots.js        → Lectura y borrado de las tres colecciones
├── components/             → Tabla paginada, filtros, panel expandible con el contenido
├── context/                → Contexto de autenticación reutilizado
├── pages/
│   ├── Login.jsx
│   ├── Home.jsx            → Resumen global más últimas capturas guardadas
│   ├── MetricSnapshots.jsx → Listado de capturas de métricas (CU-18)
│   ├── ChartSnapshots.jsx  → Listado de capturas de gráficos (CU-18)
│   ├── EntitySnapshots.jsx → Listado de capturas de entidades (CU-18)
│   └── SnapshotDetail.jsx  → Detalle reconstruido y eliminación (CU-19, CU-20)
├── renderers/
│   ├── MetricView.jsx      → Gauge, gráfico e indicadores según la métrica
│   ├── ChartView.jsx       → Gráfico interactivo según el tipo
│   └── EntityView.jsx      → Ficha con avatar, campos y barra de progreso
└── vite.config.js          → Puerto 3001, proxy al backend
```

El visor no duplica los componentes de cálculo del frontend principal: sus renderizadores operan exclusivamente sobre los datos guardados en la base documental, sin llamar a ningún endpoint de cálculo sobre el ERP. Este desacoplamiento entre cálculo y visualización garantiza que una captura mantenga su representación original aunque los cálculos del frontend principal evolucionen en el futuro.

---