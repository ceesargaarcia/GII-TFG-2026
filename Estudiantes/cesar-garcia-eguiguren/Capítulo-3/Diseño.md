# Disciplina de Diseño

## Índice

5. [Diseño de la Arquitectura](#5-diseño-de-la-arquitectura)
   - 5.1 [Patrón arquitectónico](#51-patrón-arquitectónico)
   - 5.2 [Stack tecnológico](#52-stack-tecnológico)
   - 5.3 [Seguridad: JWT y RBAC](#53-seguridad-jwt-y-rbac)
   - 5.4 [Arquitectura física desplegada](#54-arquitectura-física-desplegada)
6. [Diseño de Casos de Uso](#6-diseño-de-casos-de-uso)
   - 6.1 [CU-02 — Listar empleados](#61-cu-02--listar-empleados)
   - 6.2 [CU-03 — Ver resumen de empleado](#62-cu-03--ver-resumen-de-empleado)
   - 6.3 [CU-04 — Listar departamentos](#63-cu-04--listar-departamentos)
   - 6.4 [CU-08 — Listar tareas](#64-cu-08--listar-tareas)
   - 6.5 [CU-13 — Consultar rentabilidad financiera](#65-cu-13--consultar-rentabilidad-financiera)
   - 6.6 [CU-17 — Guardar snapshot (upsert diario)](#66-cu-17--guardar-snapshot-upsert-diario)
   - 6.7 [CU-19 — Consultar detalle de snapshot](#67-cu-19--consultar-detalle-de-snapshot)
7. [Diseño de Clases](#7-diseño-de-clases)
   - 7.1 [Mapa general de clases por capa](#71-mapa-general-de-clases-por-capa)
   - 7.2a [Repositorios base ↔ Modelos](#72a-repositorios-base-fuera-de-metrics--modelos)
   - 7.2b [Repositorios de métricas ↔ Modelos](#72b-repositorios-de-métricas-dentro-de-metrics--modelos)
   - 7.3 [Arquitectura de métricas: reutilización de subqueries](#73-arquitectura-de-métricas-reutilización-de-subqueries)
   - 7.4 [Servicios de métricas ↔ Repositorios](#74-servicios-de-métricas--repositorios-de-métricas-correspondencia-11)
   - 7.5 [DashboardService: orquestación](#75-dashboardservice-orquestación-por-composición)
   - 7.6 [Servicios de dominio ↔ Repositorios base](#76-servicios-de-dominio--repositorios-base)
   - 7.7 [Subsistema de snapshots](#77-subsistema-de-snapshots-snapshotservice--snapshotrepository--mongodb)
   - 7.8 [Capa de presentación: Frontend ↔ Routes ↔ Services](#78-capa-de-presentación-frontend--routes--services)
8. [Diseño de Paquetes](#8-diseño-de-paquetes)
   - 8.1 [Principios aplicados en la organización de paquetes](#81-principios-aplicados-en-la-organización-de-paquetes)
   - 8.2 [Métricas de calidad](#82-métricas-de-calidad)
9. [Prototipos de Interfaz](#9-prototipos-de-interfaz)


## 5. Diseño de la Arquitectura

### 5.1 Patrón arquitectónico

El sistema materializa una **arquitectura por capas de cuatro niveles** en el backend, complementada con dos aplicaciones independientes (frontend principal y visor de capturas) que consumen el mismo backend. Aunque la terminología MVC es utilizada como herramienta de análisis para clasificar las clases, la estructura de implementación va más allá del MVC clásico de tres niveles por razones de complejidad del dominio analítico.

| Concepto MVC | Materialización en la solución |
|---|---|
| **Modelo** | `models/` (ORM de Odoo) + `schemas/snapshot.py` (Pydantic sobre MongoDB) + `repositories/` (acceso a datos relacional y documental) |
| **Vista** | React SPA (principal y visor) + `schemas/` (contratos JSON) |
| **Controlador** | `routes/` (HTTP) + `services/` (lógica de negocio) |

La capa de servicios no existe en el MVC puro de tres capas. Se introduce porque los cálculos de métricas operativas requieren orquestar múltiples consultas y aplicar fórmulas complejas; concentrar esa lógica en los controladores produciría módulos de cientos de líneas con baja cohesión.

### 5.2 Stack tecnológico

| Capa | Tecnología | Justificación |
|---|---|---|
| Frontend principal | React 18 + Vite 5 (puerto 3000) | SPA reactiva; Recharts para gráficos, React Router para navegación |
| Visor de snapshots | React 18 + Vite 5 (puerto 3001) | SPA independiente de solo lectura; reutiliza el mismo `AuthContext` que el principal |
| Backend | FastAPI 0.109 + Python 3.11 | Framework asíncrono con validación automática (Pydantic) y documentación OpenAPI |
| ORM | SQLAlchemy 2.0 | Mapeo directo de tablas Odoo sin migraciones |
| Base operativa | PostgreSQL 14+ (Odoo v16) | BD existente del ERP; CTEs recursivas para jerarquía organizativa |
| Base de snapshots | MongoDB 6+ | Almacén documental para capturas históricas; `pymongo` como cliente. Tres colecciones con índices únicos sobre clave compuesta: `metric_snapshots`, `chart_snapshots`, `entity_snapshots` |
| Autenticación | JWT HS256 (python-jose) | Token stateless con scope embebido; passlib para hash Odoo |
| Comunicación | REST + JSON | Proxy Vite en desarrollo para ambos frontends; CORS configurado para producción |

### 5.3 Seguridad: JWT y RBAC

El control de acceso se resuelve en dos momentos:

1. **Login:** el servicio de autenticación calcula el rol del usuario (`director` / `responsable` / `empleado`) y su ámbito organizativo (`employee_ids`, `department_ids`, `project_ids`) consultando las jerarquías de Odoo. Este ámbito se embebe en el token JWT.

2. **Cada petición:** el middleware de autenticación decodifica el token, reconstruye el objeto `CurrentUser` y los guards (`require_manager_or_above`, `require_director`) bloquean el acceso según el rol. Los servicios aplican filtros de ámbito sobre las consultas usando los identificadores embebidos.

#### Dónde se verifica y aplica el scope

Hay tres capas distintas donde el scope del JWT actúa:

**Capa 1 — Guard de rol** (`core/auth.py`): `require_manager_or_above` / `require_director`. Bloquea por rol antes de que la petición llegue al servicio. Es lo primero que se ejecuta.

**Capa 2 — Verificación de acceso individual** (`utils/validation.py`): `verify_employee_scope`, `verify_project_scope`, `verify_department_scope`. Se llama desde la ruta o el servicio cuando el actor pide una entidad concreta por ID. Comprueba si ese ID está en la lista del JWT.

**Capa 3 — Filtrado de listados** (capa de servicio): Cuando el actor lista entidades sin especificar un ID, el servicio extrae `cu.employee_ids`, `cu.department_ids` o `cu.project_ids` del token y los pasa al repositorio como filtro.

| CU | Capa 1 | Capa 2 | Capa 3 — filtrado de scope |
|---|---|---|---|
| CU-02 Listar empleados | `require_manager_or_above` | — | `emp_ids = cu.employee_ids if cu.is_responsable` → `get_filtered_employees_query(emp_ids)` |
| CU-03 Ver resumen empleado | `require_manager_or_above` | `verify_employee_scope(cu, id)` en `EmployeeService` y en `DashboardService` | — |
| CU-04 Listar departamentos | `require_manager_or_above` | — | `dept_ids = cu.department_ids if cu.is_responsable` → `get_filtered_departments_query(dept_ids)` |
| CU-05 Ver detalle departamento | `require_manager_or_above` | `verify_department_scope(cu, id)` en `DepartmentService` | — |
| CU-06 Listar proyectos | `require_manager_or_above` | — | `[p for p in projects if p.id in cu.project_ids]` en `ProjectService.list_projects` |
| CU-07 Ver detalle proyecto | `require_manager_or_above` | `verify_project_scope(cu, id)` en `ProjectService` | — |
| CU-08 Listar tareas | `require_manager_or_above` | `verify_employee_scope` si se filtra por empleado; `verify_project_scope` si se filtra por proyecto | `effective_project_ids = cu.project_ids if cu.is_responsable` en `TaskService.filter_tasks` |
| CU-10 Consultar métrica operativa | `require_manager_or_above` | `verify_employee_scope` / `verify_project_scope` / `verify_department_scope` según el parámetro presente | Modo Workload de equipo: `get_filtered_employees_query(employee_ids=cu.employee_ids)` en `DashboardService` |
| CU-13 Consultar rentabilidad financiera ★ | `require_director` | — | — (dato global exclusivo del Director) |
| CU-14 Consultar líneas analíticas ★ | `require_director` | — | — (drill-down desde CU-13 sobre datos ya autorizados por rol) |
| CU-17 Guardar snapshot | `require_manager_or_above` | — | No aplica filtrado por scope: la snapshot captura el resultado ya mostrado en pantalla, que ya pasó los controles de scope de su CU de origen |
| CU-18 Listar snapshots | `require_manager_or_above` | — | **Decisión consciente:** el listado no aplica filtrado por scope (véase justificación más abajo) |
| CU-19 Detalle de snapshot | `require_manager_or_above` | — | Mismo criterio que CU-18 |
| CU-20 Eliminar snapshot | `require_manager_or_above` | — | Mismo criterio que CU-18 |

**Justificación de la ausencia de filtrado por scope en los CU de snapshot (CU-18/19/20).** Las snapshots son lecturas inmutables de momentos pasados y no exponen información distinta a la que ya es visible en la capa operativa filtrada por scope. Además, las snapshots de rentabilidad (CU-13, CU-14) solo pueden ser generadas por el Director (Capa 1), por lo que un Responsable nunca podrá consultarlas en detalle salvo que hayan sido previamente creadas por un Director dentro de la misma organización. El sistema acepta esta exposición limitada como compromiso razonable frente a la complejidad de aplicar scope retroactivamente sobre documentos almacenados.


### 5.4 Arquitectura física desplegada

```
┌──────────────────────┐   ┌──────────────────────┐
│ Frontend principal   │   │ Visor de snapshots   │
│ React · Vite · :3000 │   │ React · Vite · :3001 │
└─────────┬────────────┘   └──────────┬───────────┘
          │   HTTP + JWT              │   HTTP + JWT
          └───────────┬───────────────┘
                      ▼
           ┌─────────────────────┐
           │   Backend FastAPI   │
           │   uvicorn · :8000   │
           └──┬───────────────┬──┘
              │               │
        SQL :5432       BSON :27017
              │               │
   ┌──────────▼────────┐   ┌──▼──────────────┐
   │ PostgreSQL         │   │ MongoDB          │
   │ (Odoo v16 RO)     │   │ (snapshots RW)   │
   │ pool_size=5       │   │ pymongo client   │
   └───────────────────┘   └──────────────────┘
```

En desarrollo, cada frontend corre con su propio servidor Vite y un proxy inverso hacia `:8000/api`. En producción ambos se sirven como bundles estáticos detrás del mismo reverse proxy (Nginx / Caddy). El acceso a Odoo permanece en modo solo lectura mediante un usuario de base de datos con privilegios `SELECT` únicamente sobre las tablas relevantes; el acceso a MongoDB es de lectura/escritura restringido a las tres colecciones de snapshots.

---

## 6. Diseño de Casos de Uso

Esta sección especifica el flujo detallado de los casos de uso más representativos, trazando la interacción a través de todas las capas hasta que se devuelve la respuesta final. En todos los casos se aplica el mismo patrón de control de acceso en tres capas: la ruta aplica el guard de rol (Capa 1), el servicio verifica el acceso a entidades concretas (Capa 2) y filtra los listados al ámbito del JWT (Capa 3), y el repositorio ejecuta la consulta sin conocer nada de roles ni permisos.

---

### 6.1 CU-02 — Listar empleados

**Actor:** Director o Responsable
**Precondición:** el actor está autenticado.

| Paso | Capa | Clase / Función | Acción |
|---|---|---|---|
| 1 | Frontend | `Employees.jsx` → `useApi` | Envía `GET /employees/` con filtros opcionales: `department_id`, `search`, `active`, `sort_by`, `sort_order`, `page`, `page_size` |
| 2 | Routes | `employee.router` → `require_manager_or_above` | Decodifica el JWT y rechaza con 403 si el rol es `empleado`. Inyecta `CurrentUser` con `role`, `employee_ids` y `department_ids`. No aplica scope aquí: delega al servicio |
| 3 | Services | `EmployeeService.list_employees(cu, ...)` | Si se proporciona `department_id`, llama a `verify_department_scope(cu, department_id)` y lanza 403 si el responsable no gestiona ese departamento (**Capa 2**) |
| 4 | Services | | Calcula `emp_ids`: si el actor es responsable y no hay `department_id` concreto, `emp_ids = cu.employee_ids`; si es director, `emp_ids = None` sin restricción (**Capa 3**) |
| 5 | Repositories | `employee.py → get_filtered_employees_query()` | Construye la query con `WHERE id IN (:emp_ids)` si hay scope, más filtros de departamento, búsqueda por nombre y ordenación |
| 6 | Utils | `pagination.py → paginate()` | Ejecuta `COUNT(*)` para el total y aplica `OFFSET`/`LIMIT` según página |
| 7 | Routes | `employee.router` | Devuelve `200 OK` + `PaginatedResponse[EmployeeDetail]` |

**Datos de salida:** `PaginatedResponse[EmployeeDetail]` con `id`, `name`, `department_name`, `job_title`, `work_email`, `hourly_cost` y `active` por cada empleado visible para el actor.

![Diagrama de secuencia detallado de CU-02](./imagenes/CdU/CU-02.png)

---

### 6.2 CU-03 — Ver resumen de empleado

**Actor:** Director o Responsable
**Precondición:** el actor está autenticado; el `employee_id` pertenece al scope del actor.

| Paso | Capa | Clase / Función | Acción |
|---|---|---|---|
| 1 | Frontend | `EmployeeDetail.jsx` → `useApi` | Envía `GET /employees/{id}` para obtener la ficha básica del empleado |
| 2 | Routes | `employee.router` → `require_manager_or_above` | Decodifica el JWT y rechaza con 403 si el rol es `empleado`. Inyecta `CurrentUser`. Delega al servicio sin aplicar scope |
| 3 | Services | `EmployeeService.get_employee(cu, employee_id)` | Llama a `verify_employee_scope(cu, employee_id)`: 403 si el responsable no tiene ese empleado en `cu.employee_ids` (**Capa 2**) |
| 4 | Repositories | `employee.py → get_employee_by_id()` | `SELECT hr_employee JOIN hr_department WHERE id = :id`. El empleado ya está validado; el repositorio solo recupera datos |
| 5 | Frontend | `EmployeeDetail.jsx` → `useApi` | Envía `GET /dashboards/summary/employee/{id}` para cargar los KPIs del dashboard |
| 6 | Routes | `dashboards.router` → `require_manager_or_above` | Decodifica el JWT. Delega al servicio sin aplicar scope |
| 7 | Services | `DashboardService.get_employee_summary(cu, employee_id)` | Vuelve a llamar a `verify_employee_scope(cu, employee_id)` para impedir el acceso directo a la ruta del dashboard sin pasar por `/employees/{id}` (**Capa 2**) |
| 8 | Services | `WorkloadService.calculate(employee_id, detailed=True)` | Consulta las tareas abiertas asignadas al empleado y las tareas cerradas en los últimos 30 días. Calcula `workload_percentage` y `pending_hours` |
| 9 | Services | `WIPService.calculate(employee_id)` | Cuenta las tareas abiertas asignadas al usuario y clasifica el estado: `optimo`, `aceptable` o `sobrecargado` |
| 10 | Services | `ProductivityService.calculate(employee_id, date_from, date_to)` | Calcula el ratio `planned / actual × 100` para las tareas cerradas en los últimos 30 días |
| 11 | Repositories | `workload.py`, `wip.py`, `productivity.py` | Ejecutan las queries sobre `project_task`, `project_task_user_rel` y `account_analytic_line` filtrando por el `user_id` del empleado concreto |
| 12 | Services | `DashboardService` | Ensambla `EmployeeSummaryResponse` con los resultados de los tres sub-cálculos y construye `quick_stats` |
| 13 | Routes | `dashboards.router` | Devuelve `200 OK` + JSON |
| 14 | Frontend | `EmployeeDetail.jsx` | Renderiza las KpiCards de carga, WIP y productividad, y la sección de pestañas de tareas |
| 15 | Frontend | Pestañas (`Pendientes`, `Completadas`, `Asignadas`, `Responsable`) | Cada pestaña lanza `GET /tasks/filter` bajo demanda al seleccionarse, reutilizando el mismo endpoint de CU-08 con el parámetro `status` o `responsable` correspondiente |

**Datos de salida:** `EmployeeSummaryResponse` con `workload`, `wip`, `productivity_last_30_days` y `quick_stats`, incluyendo `workload_percentage`, `pending_hours`, `wip_count`, `avg_productivity` y `completed_last_30_days`.

![Diagrama de secuencia detallado de CU-03](./imagenes/CdU/CU-03.png)

---

### 6.3 CU-04 — Listar departamentos

**Actor:** Director o Responsable
**Precondición:** el actor está autenticado.

| Paso | Capa | Clase / Función | Acción |
|---|---|---|---|
| 1 | Frontend | `Departments.jsx` → `useApi` | Envía `GET /departments/` con filtros opcionales: `search`, `sort_by`, `sort_order`, `page`, `page_size` |
| 2 | Routes | `department.router` → `require_manager_or_above` | Decodifica el JWT y rechaza con 403 si el rol es `empleado`. Inyecta `CurrentUser` con `role` y `department_ids`. Delega al servicio sin aplicar scope |
| 3 | Services | `DepartmentService.list_departments(cu, ...)` | Calcula `dept_ids`: si el actor es responsable, `dept_ids = cu.department_ids`; si es director, `dept_ids = None` sin restricción (**Capa 3**). No hay Capa 2 porque no se pide un departamento concreto por ID |
| 4 | Repositories | `department.py → get_filtered_departments_query()` | Construye la query con `WHERE id IN (:dept_ids)` si hay scope, más filtro de búsqueda por nombre y ordenación. Siempre añade `WHERE active = True` |
| 5 | Utils | `pagination.py → paginate()` | Ejecuta `COUNT(*)` y aplica `OFFSET`/`LIMIT` |
| 6 | Services | `DepartmentService` | Convierte cada fila en `DepartmentDetail` con `id`, `name`, `manager_name` y `parent_id` |
| 7 | Routes | `department.router` | Devuelve `200 OK` + `PaginatedResponse[DepartmentDetail]` |

**Datos de salida:** `PaginatedResponse[DepartmentDetail]` con los departamentos visibles para el actor, ordenados según el criterio solicitado.

![Diagrama de secuencia detallado de CU-04](./imagenes/CdU/CU-04.png)

---

### 6.4 CU-08 — Listar tareas

**Actor:** Director o Responsable
**Precondición:** el actor está autenticado.

| Paso | Capa | Clase / Función | Acción |
|---|---|---|---|
| 1 | Frontend | `Tasks.jsx` | Lee los parámetros de la URL y envía `GET /tasks/filter` con filtros combinados: `status`, `stage_id`, `project_id`, `employee_id`, `date_from`, `date_to`, `date_assign`, `root_only`, `sort_by`, `sort_order`, `page`, `page_size` |
| 2 | Routes | `task.router` → `require_manager_or_above` | Decodifica el JWT y rechaza con 403 si el rol es `empleado`. Inyecta `CurrentUser`. Delega al servicio sin aplicar scope |
| 3 | Services | `TaskService.filter_tasks(cu, ...)` | Si se proporciona `employee_id`, llama a `verify_employee_scope(cu, employee_id)` → 403 si no está en `cu.employee_ids`. Si se proporciona `project_id`, llama a `verify_project_scope(cu, project_id)` (**Capa 2**) |
| 4 | Services | | Calcula `effective_project_ids`: si el actor es responsable y no hay `employee_id` ni `project_id` concreto, `effective_project_ids = cu.project_ids`; en caso contrario, `None` (**Capa 3**) |
| 5 | Repositories | `task.py → build_filtered_query()` | Construye la query con `WHERE project_id IN (:scope_ids)` si hay scope, más todos los filtros activos: estado de etapa, fechas, asignación de empleado, búsqueda por nombre y ordenación |
| 6 | Utils | `pagination.py → paginate()` | Ejecuta `COUNT(*)` y aplica `OFFSET`/`LIMIT` |
| 7 | Repositories | `timesheet.py → get_worked_hours_batch()` | Calcula las horas reales trabajadas por tarea en una única query agrupada sobre `account_analytic_line` |
| 8 | Services | `TaskService._build_items()` | Selecciona el constructor según el modo activo: `_to_pending`, `_to_completed`, `_to_assigned` o `_to_default`, construyendo el tipo de ítem correspondiente |
| 9 | Routes | `task.router` | Devuelve `200 OK` + `PaginatedResponse` con los ítems tipados según el modo |

**Datos de salida:** `PaginatedResponse[PendingTaskItem | CompletedTaskItem | AssignedTaskItem | TaskResponse]` según los filtros activos.

**Decisión de diseño clave:** el mismo endpoint `GET /tasks/filter` es reutilizado tanto por la página global de tareas como por las pestañas de `EmployeeDetail` (CU-03), que lo invocan bajo demanda al seleccionar cada pestaña.

![Diagrama de secuencia detallado de CU-08](./imagenes/CdU/CU-08.png)


---

### 6.5 CU-13 — Consultar rentabilidad financiera

**Actor:** Director
**Precondición:** el actor está autenticado con rol `director`.

| Paso | Capa | Clase / Función | Acción |
|---|---|---|---|
| 1 | Frontend | `Rentability.jsx` → `useApi` | Envía `GET /metrics/profitability/summary?date_from=X&date_to=Y` |
| 2 | Routes | `metrics.router` → `require_director` | Decodifica el JWT y rechaza con 403 si el rol no es `director`. No hay Capa 2 ni Capa 3: la rentabilidad financiera es un dato global exclusivo del director |
| 3 | Services | `RentabilityService.get_summary(date_from, date_to)` | Orquesta dos consultas: totales globales y desglose por proyecto. Opera sin restricción de scope |
| 4 | Repositories | `rentability.py → get_global_totals()` | `SELECT SUM(amount) income/expense/net, SUM(unit_amount) hours FROM account_analytic_line WHERE project_id IS NOT NULL AND date BETWEEN :from AND :to` |
| 5 | Repositories | `rentability.py → get_profitability_by_project()` | Misma tabla agrupada por `project_id` para obtener el desglose por proyecto |
| 6 | Repositories | `rentability.py → get_project_meta()` | `SELECT project_project JOIN res_partner` para enriquecer los resultados con nombre de proyecto y cliente |
| 7 | Services | `RentabilityService` | Para cada proyecto calcula `profitability_pct = net / income × 100` y clasifica el estado: `ganancia`, `neutro` o `perdida`. Cuenta proyectos por estado para el resumen |
| 8 | Routes | `metrics.router` | Devuelve `200 OK` + JSON con el resumen global y el conteo de proyectos por estado |
| 9 | Frontend | `Rentability.jsx` | Renderiza los KPIs de ingresos, gastos, neto y rentabilidad, el gráfico de barras por proyecto y el gráfico de tarta por estado |

**Datos de salida:** `{ income, expense, net, total_hours, profitability_pct, status, projects_summary: { ganancia, neutro, perdida, total } }`

Al pulsar "Ver detalles" sobre una fila de la tabla por proyecto o por cliente, el frontend dispara el flujo de `CU-14 Consultar Líneas Analíticas` parametrizado por `scope ∈ {proyecto, cliente}` según la pestaña de origen. CU-14 es un caso de uso único que absorbe el antiguo drill-down dual por proyecto y por cliente.

![Diagrama de secuencia detallado de CU-13](./imagenes/CdU/CU-13.png)

---

### 6.6 CU-17 — Guardar snapshot (upsert diario)

**Actor:** Director o Responsable
**Precondición:** el actor está en una vista calculada del frontend principal con un resultado ya mostrado en pantalla (métrica, gráfico, rentabilidad o ficha de entidad).

Este caso de uso absorbe también la semántica de actualización: si ya existe una snapshot con la misma clave compuesta `(tipo, params_hash, snapshot_date)`, la operación la sobrescribe en lugar de crear un documento nuevo. No existe un caso de uso separado "Actualizar snapshot".

| Paso | Capa | Clase / Función | Acción |
|---|---|---|---|
| 1 | Frontend | Componente `SaveSnapshotButton` | El actor pulsa "Guardar snapshot" sobre la vista calculada actual |
| 2 | Frontend | `api/snapshots.js → save(type, payload)` | Construye el payload con `tipo`, `params`, `data` y envía `POST /snapshots/{metrics\|charts\|entities}` con `Authorization: Bearer JWT` |
| 3 | Routes | `snapshots.router` → `require_manager_or_above` | Decodifica el JWT, rechaza con 401/403 si el rol es `empleado`. Inyecta `CurrentUser` |
| 4 | Services | `SnapshotService.save_{metric\|chart\|entity}(payload, cu)` | Normaliza `params` mediante ordenación alfabética y serialización canónica |
| 5 | Services | | Calcula `params_hash = SHA-256(params_normalizados)` (solo aplicable a `MetricSnapshot` y `ChartSnapshot`) |
| 6 | Services | | Fija `snapshot_date = datetime.utcnow().date()` |
| 7 | Services | | Construye `SnapshotActor` con `user_id`, `employee_id`, `employee_name` y `role` extraídos de los claims del JWT |
| 8 | Repositories | `snapshot.py → find_one(tipo, clave compuesta)` | `find_one({metric_name, params_hash, snapshot_date})` o equivalente según el subtipo |
| 9 | Repositories | `snapshot.py → update_one` o `insert_one` | Si existe → sobrescribe `data`, `updated_at`, `updated_by` (FA-01 Upsert). Si no existe → inserta un documento nuevo con `created_at`, `created_by` |
| 10 | Services | | Devuelve `{id, created: True\|False}` según el camino seguido |
| 11 | Routes | `snapshots.router` | Devuelve `200 OK` + JSON con el identificador y el indicador de creación |
| 12 | Frontend | `SaveSnapshotButton` | Muestra notificación "Snapshot creada" o "Snapshot actualizada" según corresponda |

**Datos de salida:** `{id, created: boolean}`.

**Decisión de diseño clave:** la política de upsert está anclada a **índices únicos sobre la clave compuesta** en cada colección MongoDB (RNF-16). Cualquier intento de insertar un duplicado fallaría incluso en condiciones de concurrencia. El `find_one` previo evita el error mediante control en capa de servicio; el índice de MongoDB actúa como red de seguridad definitiva.

![Diagrama de secuencia detallado de CU-17](./imagenes/CdU/CU-17.png)

---

### 6.7 CU-19 — Consultar detalle de snapshot

**Actor:** Director o Responsable
**Precondición:** el actor está autenticado en el visor (frontend2) y ha seleccionado una snapshot concreta desde CU-18 o desde la home del visor.

| Paso | Capa | Clase / Función | Acción |
|---|---|---|---|
| 1 | Frontend (visor) | `SnapshotDetail.jsx` | Recibe `{type, id}` como parámetros de la ruta y lanza la petición al backend |
| 2 | Frontend (visor) | `api/snapshots.js → get(type, id)` | Envía `GET /snapshots/{type}/{id}` con `Authorization: Bearer JWT` |
| 3 | Routes | `snapshots.router` → `require_manager_or_above` | Valida el JWT. Delega al servicio sin aplicar scope |
| 4 | Services | `SnapshotService.get_by_id(type, id)` | Delega directamente en el repositorio; no aplica lógica adicional |
| 5 | Repositories | `snapshot.py → find_by_id(type, id)` | `findOne({_id: ObjectId(id)})` sobre la colección correspondiente |
| 6 | Routes | `snapshots.router` | Devuelve `200 OK` con el documento completo o `404 Not Found` si no existe |
| 7 | Frontend (visor) | `SnapshotDetail.jsx` | Elige el renderer según el subtipo de la snapshot: `MetricView`, `ChartView` o `EntityView` |
| 8 | Frontend (visor) | Renderer seleccionado | Reconstruye la vista *como si fuera en vivo* a partir del campo `data` del documento: gauge + gráfico + KPIs para métricas, chart interactivo para gráficos, ficha con avatar y barra de progreso para entidades |
| 9 | Frontend (visor) | `SnapshotDetail.jsx` | Muestra los metadatos (fecha, hash, creado/actualizado por), los parámetros usados, el panel JSON expandible y el botón "Eliminar" que dispara CU-20 |

**Datos de salida:** documento completo de la snapshot (metadatos + `data`) y la vista reconstruida en pantalla.

**Decisión de diseño clave:** el visor reconstruye la visualización exactamente como se guardó el día de la snapshot. No se dispara cálculo alguno sobre PostgreSQL en este flujo. El estado del día de captura queda íntegramente preservado en MongoDB y los renderers operan exclusivamente sobre el JSON persistido. Este desacoplamiento entre cálculo y visualización garantiza que el visor siga funcionando aunque la lógica del frontend principal evolucione en el futuro.

![Diagrama de secuencia detallado de CU-19](./imagenes/CdU/CU-19.png)

---

# 7. Diseño de Clases

Con 13 modelos ORM, 11 repositorios base (incluido `snapshot.py`), 16 repositorios de métricas, 16 servicios de métricas, 10 servicios de dominio (incluido `SnapshotService`), 10 routers (incluido `snapshots.py`), 12 páginas del frontend principal y 6 páginas del visor, intentar representar todas las dependencias en un único diagrama produce un resultado ilegible. La estrategia seguida aquí es **presentar la arquitectura en ocho diagramas pequeños, cada uno respondiendo a una pregunta concreta**.

En aquellos diagramas donde varias clases-fuente emiten flechas que se cruzan, se aplica un **código cromático consistente**: cada clase-fuente recibe un color único que se hereda por sus flechas de dependencia salientes. El lector puede seguir cualquier flecha hasta su origen con un único movimiento visual.

---

## 7.1 Mapa general de clases por capa

El primer diagrama es un **mapa de ubicación**: muestra qué vive en cada capa sin trazar flechas entre clases. Las flechas discontinuas verticales solo indican la dirección del flujo de dependencias (siempre hacia abajo). El subsistema de snapshots se integra en el mismo esquema añadiendo `snapshots.router` en la capa de rutas, `SnapshotService` en la capa de servicios y `snapshot.py` en la capa de repositorios, este último apuntando a MongoDB en lugar de a los modelos SQLAlchemy.

![Diagrama de clases](./imagenes/diseño/clasesGeneral.png)

---

## 7.2a Repositorios base (fuera de `/metrics`) ↔ Modelos

Este diagrama cubre los **11 repositorios base**, es decir, los que viven directamente en `app/repositories/` sin entrar en el subpaquete `metrics/`. Son los proveedores de datos transversales del sistema: READ sobre las entidades de negocio (tareas, empleados, proyectos, departamentos, timesheets), autenticación, control de alcance (RBAC), búsqueda global, agregación para dashboards, gráficos y trazabilidad de cambios de Odoo. A ellos se añade `snapshot.py`, que opera en lectura/escritura sobre las tres colecciones MongoDB.

Cada repositorio recibe un color único que se hereda por sus flechas salientes: basta seguir el color para reconstruir de qué modelos o colecciones depende.

![Diagrama de repositorios base y modelos](./imagenes/diseño/repositorios-modelos.png)

**Observaciones de diseño**

- `auth.py` es un repositorio minúsculo y tiene una única razón para cambiar: la política de autenticación contra Odoo (tabla `res_users`). Su aislamiento respeta el SRP.
- `task.py` y `timesheet.py` no solo exponen consultas READ; también publican las subqueries reutilizables que comparten con la capa de métricas (§7.3). Esto justifica que sean los únicos repositorios base con doble rol.
- `scope.py` concentra toda la lógica de resolución RBAC (director / responsable / empleado) y es la única fuente autorizada para traducir un `user_id` a su ámbito (`employee_ids`, `department_ids`, `project_ids`).
- `tracking.py` es el único repositorio que accede a las tablas `mail_message`, `mail_tracking_value` e `ir_model_fields`. Encapsula por completo la dependencia con el motor de auditoría de Odoo.
- `snapshot.py` es el único repositorio del sistema que no accede a SQLAlchemy: opera sobre las tres colecciones MongoDB a través del cliente de `core/mongo.py`. Su inclusión aquí, junto al resto de repositorios base, refleja que es un proveedor de datos transversal al mismo nivel que los demás, no un caso especial.

---

## 7.2b Repositorios de métricas (dentro de `/metrics`) ↔ Modelos

Este segundo diagrama cubre los **16 repositorios especializados** de `app/repositories/metrics/`. Cada uno implementa el acceso a datos necesario para calcular una métrica concreta (productividad, cumplimiento, WIP, lead time, rentabilidad, etc.).

La mayoría de estos repositorios tienen una **huella estrecha sobre el modelo ORM**: acceden solo a 1–3 tablas directamente. El resto de la información la obtienen **reutilizando las subqueries publicadas por `task.py` y `timesheet.py`** (§7.3), lo que evita duplicar la lógica de "etapas abiertas/cerradas" y "horas imputadas" en cada métrica.

Para evitar saturar el diagrama con 16 colores distintos se aplica un **código cromático por familia de métrica**:

| Color    | Familia                                                    |
|----------|------------------------------------------------------------|
| Violeta  | Métricas de tarea individual (productividad, compliance, etc.) |
| Cian     | Métricas de proyecto (eficiencia, rentabilidad, riesgo)       |
| Verde    | Métricas de tiempo y flujo (lead time, state time, priority)  |
| Naranja  | Métricas de equipo (workload, WIP)                            |
| Coral    | Métricas financieras y de cliente (profitability, rentability, distribución) |
| Azul     | Métricas de asistencia (attendance)                           |

![Diagrama de metricas y modelos](./imagenes/diseño/repoMetricas-modelos.png)

**Observaciones de diseño**

- Los repositorios de métricas tienen una huella intencionadamente estrecha sobre el modelo: el 80 % accede a 1–3 tablas directamente.
- `workload.py` y `rentability.py` son los más amplios porque su resultado es inherentemente agregado (tareas + horas + proyecto + empleado).
- `attendance.py` es la única métrica que accede a la tabla `hr_attendance`; está aislado del resto del modelo para reflejar que su origen de datos es el módulo de control de fichajes de Odoo, no el de gestión de proyectos.
- Ningún repositorio de métricas importa otro repositorio de métricas: la composición se deja a la capa de servicios (§7.5).

---

## 7.3 Arquitectura de métricas: reutilización de subqueries

Este diagrama muestra que los repositorios de métricas no vuelven a escribir cada uno su propia consulta de "etapas abiertas" ni "horas imputadas"; consumen tres subqueries reutilizables publicadas por `task.py` (violeta), `timesheet.py` (verde) y `scope.py` (azul). El código cromático permite ver a simple vista qué repositorio de métricas depende de cuál proveedor: si un repo tiene una única flecha violeta es que solo consulta estados de tarea; si tiene dos colores es que combina ambas fuentes.

![Diagrama de reutilización de subqueries](./imagenes/diseño/reutilizacionMetricas.png)
---

## 7.4 Servicios de métricas ↔ Repositorios de métricas (correspondencia 1:1)

Una vez establecido el patrón anterior, la capa de servicios de métricas es trivialmente regular: **cada servicio consume exactamente un repositorio de métricas**. Aquí no se aplican colores: la correspondencia 1:1 hace que las flechas vayan en paralelo sin cruzarse, y añadir 14 colores distintos sería decorativo más que informativo.

![Diagrama de servicios de métricas y repositorios de métricas](./imagenes/diseño/serviciosMetricas-repositorios.png)

---

## 7.5 DashboardService: orquestación por composición

El `DashboardService` no duplica lógica de métricas; las **compone**. Este diagrama hace visible la relación "consumer-of-services" que permite que un endpoint de dashboard (CU-03, CU-05, CU-07, CU-10 en modo equipo) devuelva en una sola llamada lo equivalente a invocar seis endpoints de métricas distintos. Las flechas desde `DashboardService` van en cian (es el protagonista) y las de `DepartmentService` en naranja para distinguir sus respectivas dependencias sobre `WorkloadService`.

En particular, `DashboardService.get_manager_overview()` representa la variante del caso de uso CU-10 en modo agregado de equipo, y `WorkloadService` soporta tanto el modo individual (`employee_id` presente) como el modo equipo (sin `employee_id`) compartiendo la primitiva `get_pending_hours_per_employee()` del repositorio. Esta reutilización justifica la consolidación documentada en el Capítulo 2.

![Diagrama de DashboardService y su composición de servicios de métricas](./imagenes/diseño/dashboardService.png)

---

## 7.6 Servicios de dominio ↔ Repositorios base

Diagrama "simétrico" al 7.4 pero para la capa de dominio. Aquí la correspondencia ya no es 1:1 (un `ProjectService` puede depender de tres repositorios distintos para componer la respuesta completa) y **las flechas sí se cruzan**, porque varios servicios comparten repositorios como `employee.py` o `task.py`. El código cromático asigna un color único a cada servicio de dominio: para trazar sus dependencias basta seguir las flechas de ese color.

![Diagrama de servicios de dominio y repositorios base](./imagenes/diseño/servicios-Repositorios.png)

---

## 7.7 Subsistema de snapshots: Frontend ↔ Routes ↔ Services ↔ Repositories ↔ MongoDB

![Diagrama del subsistema de snapshots](./imagenes/diseño/subsistemaSnapshots.png)

Este diagrama aísla el subsistema de snapshots, que por su naturaleza (persistencia documental en lugar de relacional) merece una vista dedicada. `SnapshotService` concentra todas las responsabilidades transversales al subsistema: normalización de parámetros, cálculo de `params_hash` mediante SHA-256, construcción del `SnapshotActor` a partir de los claims del JWT, fijación de `snapshot_date` y la decisión de upsert (insert si la clave compuesta no existe, update si ya existe).

`SnapshotRepository` encapsula la totalidad de las operaciones sobre las tres colecciones MongoDB. Es el único repositorio del sistema que no se asienta sobre SQLAlchemy y ofrece una API homogénea para los tres subtipos (`insert_one`, `find_one`, `update_one`, `find_many` con paginación, `delete_one`). La clave compuesta de cada colección está protegida por un índice único en MongoDB, lo que garantiza a nivel de base de datos la restricción RNF-16 "una snapshot por tipo, parámetros y día".

La decisión deliberada de **no aplicar filtrado por scope** en la lectura de snapshots se documenta en §5.3: las snapshots son lecturas inmutables de momentos pasados y las snapshots de rentabilidad (CU-13, CU-14) solo pueden ser generadas por el Director, lo que limita implícitamente su exposición.

---

## 7.8 Capa de presentación: Frontend ↔ Routes ↔ Services

El último diagrama cierra el ciclo: muestra qué páginas de los dos frontends disparan qué endpoints, y a qué servicios delegan esos endpoints. El frontend principal alimenta todos los CUs operativos y dispara el guardado de snapshots (CU-17) desde cualquier vista calculada. El visor, por el contrario, solo consume los endpoints de lectura y borrado de snapshots (CU-18, CU-19, CU-20) y nunca dispara cálculos sobre Odoo.

El diagrama se apoya en la **disposición espacial estricta por columnas**: páginas a la izquierda, routers en el centro, servicios a la derecha, con correspondencia mayoritariamente horizontal.

![Diagrama de frontend, routes y servicios](./imagenes/diseño/frontend-Routes-Services.png)

---

## 8. Diseño de Paquetes

### 8.1 Principios aplicados en la organización de paquetes

**Jerarquización por capas (Principio de jerarquización)**

Las dependencias entre paquetes son exclusivamente descendentes. Ningún import apunta hacia arriba. Las rutas no importan modelos ORM ni repositorios directamente. El subsistema de snapshots respeta el mismo criterio: `snapshots.router` importa `SnapshotService`, que a su vez importa `SnapshotRepository`, que a su vez importa el cliente de `core/mongo.py`.

**SRP — Una responsabilidad por módulo**

Cada fichero de `repositories/metrics/` tiene una única razón para cambiar: la estructura de la tabla Odoo relevante para esa métrica. Cada fichero de `services/metrics/` tiene también una única razón: la fórmula de cálculo de esa métrica. `SnapshotService` tiene una única razón para cambiar: las reglas de persistencia (normalización, hash, upsert). El añadir una nueva métrica implica crear exactamente dos ficheros nuevos y un endpoint, sin tocar ningún módulo existente; añadir un nuevo subtipo de snapshot implica una nueva colección, un nuevo schema Pydantic y un nuevo renderer en el visor.

**OCP — Abierto para extensión**

El registro `_ITEM_BUILDERS` de `TaskService` permite añadir un nuevo tipo de ítem (por ejemplo, `"subtask"`) sin modificar el método `_build_items`: basta con añadir una entrada al diccionario y un método `_to_subtask()`. El bucle de construcción no contiene ningún `if/elif`. De forma análoga, el visor selecciona el renderer de CU-19 por clave de subtipo, sin ramificar en `if/elif`: añadir un nuevo renderer no requiere modificar el código existente.

**ISP — Interfaces pequeñas y específicas**

Cada servicio de métricas importa únicamente las funciones del submódulo concreto que necesita (`from app.repositories.metrics.wip import count_open_assigned_tasks`), no el paquete completo. En los frontends, cada página importa solo su módulo de API (`employees.js`, `metrics.js`, `snapshots.js`, etc.).

**DIP — Inversión de dependencias**

Los servicios (módulos de alto nivel) no dependen de los modelos ORM ni del cliente MongoDB directamente (módulos de bajo nivel). Acceden a los datos exclusivamente a través de las funciones de repositorio, que actúan como abstracción. Cero imports `from app.models` en `routes/` o `services/`; cero imports de `pymongo` fuera de `core/mongo.py` y `repositories/snapshot.py`.

**DRY — No te repitas**

Las subqueries `open_stage_ids_subq()`, `closed_stage_ids_subq()` y `worked_hours_subq()` están definidas una sola vez en `task.py` y `timesheet.py` respectivamente. Todos los repositorios de métricas las importan y reutilizan. Las constantes de dominio (umbrales, etiquetas, ventanas temporales) están centralizadas en `core/constants.py`. La normalización de parámetros y el cálculo de `params_hash` están definidos una única vez en `SnapshotService`, y son reutilizados por los tres métodos `save_metric`, `save_chart` y `save_entity`.

**Cohesión funcional**

Cada repositorio agrupa únicamente consultas de un mismo dominio de datos. No existe ningún módulo con funciones heterogéneas: `task.py` solo consulta tareas, `employee.py` solo consulta empleados, `rentability.py` solo consulta datos financieros, `snapshot.py` solo opera sobre MongoDB.

**Bajo acoplamiento**

Los dos únicos casos de dependencia cruzada entre repositorios del mismo nivel (`repositories/metrics/` → `repositories/task.py` y `repositories/timesheet.py`) son de **acoplamiento por datos**: se comparten subqueries puras sin estado ni efectos secundarios. Este es el nivel más bajo de la escala de acoplamiento. `snapshot.py` no tiene ninguna dependencia cruzada con otros repositorios: su aislamiento es total.

### 8.2 Métricas de calidad

| Principio | Indicador | Valor | Estado |
|---|---|---|---|
| Jerarquización | Imports ascendentes (violaciones de capa) | 0 | ✅ |
| SRP | Módulos de repositorio con más de un dominio | 0 | ✅ |
| OCP | Bifurcaciones if/elif en `_build_items` | 0 | ✅ |
| DIP | Imports directos de Models en Routes | 0 | ✅ |
| DIP | Imports de `pymongo` fuera de `core/mongo.py` y `repositories/snapshot.py` | 0 | ✅ |
| ISP | Servicios que importan el módulo de repo completo | 0 | ✅ |
| DRY | Subqueries duplicadas entre repositorios | 0 | ✅ |
| Cohesión funcional | Repos con funciones de múltiples dominios | 0 | ✅ |
| Bajo acoplamiento | Dependencias cruzadas entre repos (acoplamiento por datos) | 2 justificadas | ✅ |

---

## 9. Prototipos de Interfaz

Los prototipos presentados en esta sección fueron elaborados en la **Disciplina de Requisitos (Capítulo 2)** como parte del modelado de casos de uso. Se incluyen aquí como referencia visual para el diseño de la interfaz, dado que la implementación real del frontend se ajusta fielmente a ellos.

---

### P-01 — Vista de inicio (Overview)

Panel de bienvenida con alertas activas, indicadores globales (proyectos, empleados, tareas) y accesos rápidos. Sirve de punto de entrada tras la autenticación (CU-01).

![Vista de inicio — Overview](../Capítulo-2/imagenes/prototipado/Vista-Overview.png)

---

### P-02 — Resumen de Empleado (CU-03)

Panel individual con cabecera de perfil, cuatro KPI cards (carga, vencidas, WIP, productividad), tarjetas de tareas asignadas hoy y vencidas sin cerrar, y tabla de tareas paginada con pestañas.

![Prototipo CU-03 — Resumen de Empleado](../Capítulo-2/imagenes/prototipado/CU-05.png)

**Decisiones de diseño implementadas:**
- Las pestañas de tareas cargan bajo demanda invocando `GET /tasks/filter`.
- Las tarjetas de alerta son clicables y navegan a la vista de tareas con filtros preseleccionados.

---

### P-03 — Listado y filtrado de tareas (CU-08)

Tabla paginada con barra de filtros combinables: estado, etapa exacta (mutuamente excluyente con estado), proyecto, rango de fechas de deadline y opción de solo tareas padre.

![Prototipo CU-08 — Listado de Tareas](../Capítulo-2/imagenes/prototipado/CU-10.png)

**Decisiones de diseño implementadas:**
- El selector de etapa tiene prioridad sobre el de estado cuando ambos están activos.
- Las cabeceras de columna son clicables para cambiar el criterio de ordenación server-side.

---

### P-04 — Panel de métricas (CU-10)

Cuadrícula de tarjetas métricas a la izquierda con gauge de preview. Al seleccionar una tarjeta, el panel derecho muestra el detalle completo con gráficos y KPIs. Panel de parámetros expandible en la parte superior.

![Prototipo Métricas — CU-10](../Capítulo-2/imagenes/prototipado/CU-P7.png)

**Decisiones de diseño implementadas:**
- Las métricas que requieren parámetros (empleado, proyecto) muestran un estado vacío hasta que se rellenan.
- El panel de detalle es sticky para no perder de vista los KPIs al hacer scroll en la cuadrícula.
- Todas las vistas de detalle incluyen el botón "Guardar snapshot" que dispara CU-17.

---

### P-05 — Panel de Workload en modo equipo (CU-10 variante agregada)

Cinco tarjetas numéricas clicables (total, sobrecargado, normal, subcargado, sin tareas), panel de empleados más cargados con barra de progreso, gráfico de distribución por estado y tabla paginada que aparece al seleccionar un estado.

![Prototipo CU-10 equipo — Panel de Workload agregado](../Capítulo-2/imagenes/prototipado/CU-28.png)

**Decisiones de diseño implementadas:**
- Las tarjetas de estado y las barras del gráfico desencadenan el mismo efecto: filtran la tabla inferior y hacen scroll automático hasta ella.
- El nombre del empleado en la tabla enlaza directamente a su resumen (CU-03).
- Esta vista corresponde a la variante de CU-10 con `WorkloadService` invocado sin `employee_id`; en el código todavía se mantiene la página `Manager.jsx` como superficie específica.

---

### P-06 — Rentabilidad Financiera (CU-13)

Panel exclusivo del Director con filtros de fecha y modo de análisis (global / por proyecto / por responsable), KPIs financieros, gráfico comparativo de ingresos vs. gastos y pestañas de desglose por proyecto y por cliente con opción de drill-down a líneas analíticas.

![Prototipo CU-13 — Rentabilidad](../Capítulo-2/imagenes/prototipado/CU-24.png)

**Decisiones de diseño implementadas:**
- El acceso al módulo devuelve una pantalla de acceso restringido para cualquier rol distinto de Director (HTTP 403 en backend + redirección en frontend).
- El drill-down de líneas analíticas se activa bajo demanda, sin cargar los datos hasta que el usuario lo solicita explícitamente. La misma vista de drill-down sirve tanto para líneas de proyecto como para líneas de cliente, parametrizada por el `scope` de la pestaña de origen (CU-14).

---

### P-07 — Visor de snapshots (CU-18, CU-19, CU-20)

Aplicación independiente en el puerto 3001 compuesta por cuatro vistas principales:

- **Home del visor:** resumen global con contadores por colección (métricas, gráficos, entidades) y acceso rápido a las últimas snapshots guardadas.
- **Listado por colección:** tabla paginada con filtros por tipo y rango de fechas (CU-18).
- **Detalle de snapshot:** ficha con metadatos, vista reconstruida por el renderer específico y panel JSON expandible (CU-19).
- **Diálogo de eliminación:** confirmación previa a un hard delete sobre MongoDB (CU-20).

**Decisiones de diseño implementadas:**
- El visor reutiliza el mismo `AuthContext` y esquema de login que el frontend principal; el token JWT es intercambiable entre ambas aplicaciones.
- Los renderers del visor (`MetricView`, `ChartView`, `EntityView`) son clones ligeros de los componentes de cálculo del frontend principal, pero operan exclusivamente sobre el JSON persistido y nunca llaman al backend para recalcular.