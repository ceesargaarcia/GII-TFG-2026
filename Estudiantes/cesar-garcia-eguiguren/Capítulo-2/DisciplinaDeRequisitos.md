# Disciplina de Requisitos

## Índice

1. [Encontrar Actores y Casos de Uso](#1-encontrar-actores-y-casos-de-uso)
   - 1.1 [Identificación de Actores](#11-identificación-de-actores)
   - 1.2 [Criterio de diseño del modelo](#12-criterio-de-diseño-del-modelo)
   - 1.3 [Lista de Casos de Uso](#13-lista-de-casos-de-uso)
   - 1.4 [Diagramas de Actor](#14-diagramas-de-actor)
        - 1.4.1 [Diagrama de casos de uso – Director](#141-diagrama-de-casos-de-uso--director)
        - 1.4.2 [Diagrama de casos de uso – Responsable](#142-diagrama-de-casos-de-uso--responsable)
2. [Priorizar Casos de Uso](#2-priorizar-casos-de-uso)
3. [Detallar Casos de Uso](#3-detallar-casos-de-uso)
4. [Prototipar Casos de Uso](#4-prototipar-casos-de-uso)
5. [Estructurar el Modelo de Casos de Uso](#5-estructurar-el-modelo-de-casos-de-uso)

---

## 1. Encontrar Actores y Casos de Uso

### 1.1 Identificación de Actores

| Actor | Descripción | Identificación en Odoo | Alcance de datos |
|---|---|---|---|
| **Director** | Máximo responsable de la organización. Acceso irrestricto a todo el sistema, incluido el módulo de rentabilidad financiera. | `hr_employee.parent_id == hr_employee.id` (nodo raíz del árbol jerárquico) | Global — sin filtro |
| **Responsable** | Jefe de departamento o responsable de proyecto. Acceso filtrado a su ámbito organizativo calculado en el momento de la autenticación. | Gestiona ≥1 departamento (`hr_department.manager_id`) o proyecto (`project_project.user_id`) | `employee_ids` (CTE recursivo), `department_ids`, `project_ids` embebidos en JWT |

> Los empleados rasos (`role = "empleado"`) **no tienen acceso** al sistema; son redirigidos al login por `ProtectedRoute`.  
> La detección de rol y el cálculo del alcance se realizan en `auth_service.resolve_role_and_scope()` al emitir el JWT.

---

### 1.2 Criterio de diseño del modelo

El modelo se organiza alrededor de un principio de **simetría por entidad**: cada entidad principal del dominio expone exactamente dos casos de uso:

| Entidad | CU de listado/búsqueda | CU de resumen analítico |
|---|---|---|
| Empleado | CU-04 Listar empleados | CU-05 Resumen de empleado |
| Departamento | CU-06 Listar departamentos | CU-07 Resumen de departamento |
| Proyecto | CU-08 Listar proyectos | CU-09 Resumen de proyecto |
| Tarea | CU-10 Listar tareas | CU-11 Detalle de tarea |

Los CU de resumen son el "dashboard analítico" de cada entidad: agregan KPIs, métricas y accesos a recursos relacionados. No son páginas de formulario sino paneles de información. Las métricas operativas (P7) se consumen tanto desde los resúmenes de entidad como desde la página `/metrics`.

---

### 1.3 Identificación de Casos de Uso

A continuación se presenta la lista completa de los casos de uso identificados, organizados en paquetes para facilitar su comprensión. 
#### Paquete 1 – Autenticación

| ID | Nombre | Actor(es) |
|---|---|---|
| CU-01 | Autenticarse en el Sistema | Director, Responsable |

#### Paquete 2 – Panel de Control

| ID | Nombre | Actor(es) |
|---|---|---|
| CU-02 | Visualizar Overview del Sistema | Director, Responsable |
| CU-03 | Consultar Panel Manager | Director, Responsable |

#### Paquete 3 – Entidad: Empleado

| ID | Nombre | Actor(es) | Descripción resumida |
|---|---|---|---|
| CU-04 | Listar y Buscar Empleados | Director, Responsable | Listado paginado con búsqueda, filtro por departamento y estado. Ordenación global server-side. |
| CU-05 | Consultar Resumen de Empleado | Director, Responsable | Dashboard individual: KPIs de WIP, carga de trabajo, productividad 30d y tareas agrupadas por estado (pendientes, completadas, asignadas, responsable). |

#### Paquete 4 – Entidad: Departamento

| ID | Nombre | Actor(es) | Descripción resumida |
|---|---|---|---|
| CU-06 | Listar Departamentos | Director, Responsable | Cuadrícula de departamentos activos con nombre y responsable. |
| CU-07 | Consultar Resumen de Departamento | Director, Responsable | Dashboard de departamento: KPIs de carga (sobrecargados, subcargados, sin tareas), tabla de workload por empleado y nómina. |

#### Paquete 5 – Entidad: Proyecto

| ID | Nombre | Actor(es) | Descripción resumida |
|---|---|---|---|
| CU-08 | Listar Proyectos | Director, Responsable | Cuadrícula de proyectos activos con cliente y código. |
| CU-09 | Consultar Resumen de Proyecto | Director, Responsable | Dashboard de proyecto: KPIs de eficiencia, riesgo y rentabilidad por horas; gráfico est./real; listado de tareas y equipo. |

#### Paquete 6 – Entidad: Tarea

| ID | Nombre | Actor(es) | Descripción resumida |
|---|---|---|---|
| CU-10 | Listar Tareas | Director, Responsable | Punto de acceso único a tareas, con filtros combinables (estado, etapa, proyecto, empleado, fechas, solo padre). El mismo CU se ejecuta desde el contexto global (`/tasks`), desde un proyecto (CU-09) o desde un empleado (CU-05). |
| CU-11 | Consultar Detalle de Tarea | Director, Responsable | Ficha de tarea: info general, personas (responsable + asignados), horas (est./real/restantes con barra de progreso), subtareas. |


#### Paquete 7 – Métricas Operativas

| ID | Nombre | Actor(es) | Descripción resumida |
|---|---|---|---|
| CU-12 | Consultar Productividad | Director, Responsable | Ratio `(planificadas/reales)×100` para tareas cerradas, con ranking y promedio. |
| CU-13 | Consultar Cumplimiento de Plazos | Director, Responsable | Porcentaje de tareas cerradas en fecha (`date_end ≤ date_deadline`). |
| CU-14 | Consultar Carga de Trabajo y WIP | Director, Responsable | Workload `(horas_pend/40h)×100` con estado sobrecargado/normal/subcargado; WIP con umbral óptimo ≤3. Requiere empleado. |
| CU-15 | Consultar Riesgo de Proyecto | Director, Responsable | Índice de riesgo: tareas vencidas o con ≥80% plazo consumido sobre total abiertas. Requiere proyecto. |
| CU-16 | Consultar Tasa de Retrabajo | Director, Responsable | Porcentaje de tareas reabiertas tras cerrarse (análisis de `mail_tracking_value`). |
| CU-17 | Consultar Exactitud de Estimación | Director, Responsable | Ratio medio real/planificado y sesgo (subestima/sobreestima/preciso). Requiere empleado. |
| CU-18 | Consultar Lead Time | Director, Responsable | Días medios entre `date_assign` y `date_end` de tareas cerradas. |
| CU-19 | Consultar Tiempo por Estado | Director, Responsable | Tabla de etapas con horas medias de permanencia (calculado a partir de `mail_tracking_value`). |
| CU-20 | Consultar Tareas Canceladas | Director, Responsable | % de tareas cuya etapa se llama "Cancelado". |
| CU-21 | Consultar Tiempo por Prioridad | Director, Responsable | Horas medias invertidas por nivel de prioridad (Normal / Urgente). |

#### Paquete 8 – Análisis Visual

| ID | Nombre | Actor(es) | Descripción resumida |
|---|---|---|---|
| CU-22 | Visualizar Gráficos Analíticos | Director, Responsable | Cuatro gráficos interactivos: evolución de tareas, distribución por estado, horas por cliente y carga por departamento. |
| CU-23 | Consultar Asistencia vs Imputaciones | Director, Responsable | Comparativa fichadas (`hr_attendance`) vs imputadas (`account_analytic_line`) con serie diaria por empleado. |

#### Paquete 9 – Rentabilidad Financiera _(exclusivo para el director)_

| ID | Nombre | Actor(es) | Descripción resumida |
|---|---|---|---|
| CU-24 | Consultar Rentabilidad Financiera | Director | Análisis completo basado en `amount` de partes analíticos: resumen global, desglose por proyecto, por cliente, drill-down por responsable y panel de estado del equipo para asignación. Consolida la antigua distribución de clientes como sub-flujo. |
| CU-26 | Consultar Líneas Analíticas de Proyecto | Director | Drill-down desde CU-24: lista detallada de ingresos y gastos por proyecto, con fechas, nombres y montos individuales de `account_analytic_line`. |
| CU-27 | Consultar Líneas Analíticas de Cliente | Director | Drill-down desde CU-24: lista detallada de ingresos y gastos por cliente, agregando líneas de todos sus proyectos asociados. |

#### Paquete 10 – Utilidades

| ID | Nombre | Actor(es) | Descripción resumida |
|---|---|---|---|
| CU-25 | Realizar Búsqueda Global | Director, Responsable | Búsqueda en tiempo real (debounce 350 ms, ≥2 caracteres) de tareas, proyectos y empleados por nombre/código. |

---

### 1.4 Diagramas de Actor
#### 1.4.1 Diagrama de casos de uso – Director

![Diagrama de casos de uso - Director](./imagenes/actor_director.png)


#### 1.4.2 Diagrama de casos de uso – Responsable

![Diagrama de casos de uso - Responsable](./imagenes/actor_responsable.png)

Ambos actores comparten la mayoría de los casos de uso, pero el Director tiene acceso exclusivo a los relacionados con la rentabilidad financiera (CU-24, CU-26, CU-27). El Responsable tiene un alcance de datos filtrado en todos los casos de uso, mientras que el Director accede a datos globales sin restricciones.

**Resumen rápido:**
- **Director:** Acceso a los 27 CU sin restricciones (incluidos CU-24, CU-26, CU-27 de rentabilidad exclusivos).
- **Responsable:** Acceso a CU-01 hasta CU-23 con filtrado automático por scope (employee_ids, department_ids, project_ids desde el JWT); HTTP 403 para CU-24, CU-26, CU-27.

---

## 2. Priorizar Casos de Uso

### 2.1 Criterios

| Criterio | Descripción | Escala |
|---|---|---|
| **Criticidad** | ¿Bloquea la ejecución de otros CU? | 1–3 |
| **Valor de negocio** | ¿Apoya decisiones estratégicas o tácticas? | 1–3 |
| **Frecuencia de uso** | ¿Se ejecuta en cada sesión o esporádicamente? | 1–3 |
| **Riesgo técnico** | ¿Implica lógica compleja o integración crítica con Odoo? | 1–3 |

### 2.2 Tabla de Priorización

| ID | Nombre | Crit. | V.Neg. | Frec. | R.Téc. | **Prioridad** |
|---|---|:---:|:---:|:---:|:---:|:---:|
| CU-01 | Autenticarse | 3 | 3 | 3 | 3 | **Alta** |
| CU-02 | Visualizar Overview | 3 | 3 | 3 | 2 | **Alta** |
| CU-03 | Panel Manager | 3 | 3 | 3 | 2 | **Alta** |
| CU-04 | Listar Empleados | 2 | 3 | 3 | 1 | **Alta** |
| CU-05 | Resumen Empleado | 2 | 3 | 3 | 2 | **Alta** |
| CU-08 | Listar Proyectos | 2 | 3 | 3 | 1 | **Alta** |
| CU-09 | Resumen Proyecto | 2 | 3 | 2 | 2 | **Alta** |
| CU-10 | Listar Tareas | 3 | 3 | 3 | 2 | **Alta** |
| CU-24 | Rentabilidad Financiera | 3 | 3 | 2 | 3 | **Alta** |
| CU-26 | Líneas Analíticas Proyecto | 1 | 3 | 1 | 2 | **Alta** |
| CU-27 | Líneas Analíticas Cliente | 1 | 3 | 1 | 2 | **Alta** |
| CU-06 | Listar Departamentos | 2 | 2 | 2 | 1 | **Media** |
| CU-07 | Resumen Departamento | 2 | 3 | 2 | 1 | **Media** |
| CU-11 | Detalle de Tarea | 2 | 2 | 3 | 1 | **Media** |
| CU-12 | Productividad | 2 | 3 | 2 | 2 | **Media** |
| CU-13 | Cumplimiento | 2 | 3 | 2 | 1 | **Media** |
| CU-14 | Carga / WIP | 2 | 3 | 2 | 2 | **Media** |
| CU-15 | Riesgo Proyecto | 2 | 3 | 2 | 2 | **Media** |
| CU-16 | Retrabajo | 1 | 2 | 1 | 3 | **Media** |
| CU-17 | Exactitud Estimación | 1 | 2 | 1 | 2 | **Media** |
| CU-18 | Lead Time | 2 | 3 | 2 | 2 | **Media** |
| CU-19 | Tiempo por Estado | 2 | 3 | 2 | 2 | **Media** |
| CU-20 | Tareas Canceladas | 2 | 2 | 2 | 2 | **Media** |
| CU-21 | Tiempo por Prioridad | 2 | 2 | 2 | 2 | **Media** |
| CU-22 | Gráficos Analíticos | 2 | 3 | 2 | 2 | **Media** |
| CU-23 | Asistencia vs Imputaciones | 2 | 3 | 2 | 2 | **Media** |
| CU-25 | Búsqueda Global | 1 | 2 | 3 | 1 | **Media** |

> **Núcleo de uso diario:** CU-01 es prerrequisito universal. CU-02, CU-03, CU-05, CU-09 y CU-10 conforman el núcleo operativo. 
> 
> **Prioridad estratégica:** CU-24, CU-26 y CU-27 (rentabilidad) tienen alta prioridad aunque uso menos frecuente (Director exclusivo).
> 
> **Riesgo técnico:** CU-16 a CU-21 dependen de `mail_tracking_value` (análisis de etapas), lo que requiere integración cuidadosa con Odoo. CU-24-27 dependen de `account_analytic_line` (partes analíticos).

---

## 3. Detallar Casos de Uso

Todos los casos de uso existentes para esta solución están documentados aqui : [Disciplina de Requisitos – Casos de Uso Detallados](./docs/CasosDeUsoDetallados.md)

### CU-01 – Autenticarse en el Sistema

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | El `user_id` tiene un `hr_employee` activo vinculado en Odoo. |
| **Postcondición** | JWT almacenado en `localStorage`, cabecera `Authorization: Bearer` inyectada en el cliente Axios. Usuario redirigido a CU-02. |

**Flujo principal:**
1. El actor navega a `/login` e introduce su `res_users.id`.
2. `POST /auth/token {user_id}` → el sistema localiza el empleado activo.
3. `resolve_role_and_scope()` determina rol y calcula `employee_ids`, `department_ids`, `project_ids` mediante CTE recursivo.
4. Se emite JWT `HS256` (8 h) con `{user_id, employee_id, role, employee_ids, department_ids, project_ids}`.
5. El frontend almacena token y redirige a `/`.

**Flujos alternativos:**
- `FA-01` _(paso 2)_: Sin empleado activo → HTTP 404, usuario permanece en login.
- `FA-02` _(paso 3)_: Rol `"empleado"` → acceso denegado, mensaje informativo.
- `FA-03` _(sesión activa)_: Token expirado → interceptor Axios detecta HTTP 401, limpia `localStorage` y redirige a `/login`.

**Relaciones:** `<<include>>` por todos los demás CU (precondición universal).

---

### CU-02 – Visualizar Overview del Sistema

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor tiene una visión consolidada del estado del sistema y puede navegar a cualquier otro CU. |

**Flujo principal:**
1. El sistema carga en paralelo: `GET /dashboards/overview`, `/charts/task-distribution`, `/charts/productivity-trend?days=14`, `/dashboards/summary/manager`, `/metrics/compliance`, `/metrics/rework-rate`, `/metrics/lead-time`.
2. Se muestran 4 KPIs: proyectos activos, empleados activos, tareas abiertas (+creadas esta semana), tareas vencidas.
3. Si hay tareas vencidas → banner de alerta rojo con enlace a CU-10 (`status=overdue`).
4. Si hay empleados sobrecargados → banner amarillo con sus nombres y enlace a CU-03.
5. Gráfico de área de actividad (14 días) y panel de salud operativa (cumplimiento, retrabajo, lead time con barras de progreso y distribución del equipo).
6. Distribución de tareas por etapa (barras de progreso coloreadas) y acceso rápido a CU-03, CU-08, CU-04, CU-10, CU-20, CU-21.
7. El Responsable recibe los datos filtrados por su `project_ids` y `employee_ids`.

**Flujos alternativos:**
- `FA-01`: Alguna petición falla → `ErrorState` con botón Reintentar.
- `FA-02`: Sin tareas ni proyectos → KPIs a 0, estados vacíos con iconos.

**Relaciones:** `<<include>>` CU-01. Navega a CU-03, CU-04, CU-07, CU-08, CU-10, CU-13, CU-19, CU-20, CU-21.

---

### CU-03 – Consultar Panel Manager

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor conoce el estado operativo completo del equipo. |

**Flujo principal:**
1. El actor navega a `/manager`. Puede filtrar por departamento y alternar entre vista **Ejecutivo** y **Equipo**.
2. El sistema carga: `GET /dashboards/summary/manager`, `/charts/workload-by-dept`, `/metrics/compliance`, `/metrics/lead-time`, `/metrics/cancelled-tasks`, `/metrics/rework-rate`, `/dashboards/overview`.
3. 4 KPIs: total empleados, sobrecargados, subcargados, sin tareas. Banner de alerta si hay sobrecargados.
4. **Vista Ejecutivo:** radar de salud (5 dimensiones: Cumplimiento, Eficiencia, Velocidad, Calidad, Capacidad), panel de alertas operativas, top 5 más cargados con enlace a CU-05, distribución de estados (pills + bar chart), actividad por departamento con enlace a CU-07.
5. **Vista Equipo:** tabla de carga de trabajo ordenable por % de ocupación, con badge de estado y enlace a CU-05 por empleado.

**Flujos alternativos:**
- `FA-01`: Responsable → datos filtrados por `employee_ids` y `department_ids`.
- `FA-02`: Sin empleados en el departamento → tabla vacía.

**Relaciones:** `<<include>>` CU-01. `<<extend>>` CU-14 (carga equipo). Navega a CU-05, CU-07.

---

### CU-04 – Listar y Buscar Empleados

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor localiza el empleado y puede navegar a CU-05. |

**Flujo principal:**
1. `GET /employees/` con parámetros `page`, `page_size`, `search`, `department_id`, `active`, `sort_by`, `sort_order`.
2. Tabla paginada (25/página): nombre, departamento, cargo, email, coste/h, badge de estado.
3. Búsqueda con debounce 300 ms. Filtro por departamento (select). Toggle activos.
4. Ordenación global server-side por cualquier columna.
5. Click en fila → CU-05.
6. El Responsable solo ve empleados de su `employee_ids`.

**Relaciones:** `<<include>>` CU-01. Navega a CU-05.

---

### CU-05 – Consultar Resumen de Empleado

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. El empleado está en el alcance del actor. |
| **Postcondición** | El actor conoce el estado completo del empleado: carga, WIP, productividad y tareas. |

**Flujo principal:**
1. El actor accede a `/employees/{id}` (desde CU-04 o enlace en CU-03/CU-07).
2. El sistema verifica `verify_employee_scope`. Carga: `GET /employees/{id}`, `/dashboards/summary/employee/{id}`.
3. Cabecera con avatar inicial, nombre, cargo, departamento, coste/h y badge de estado de carga.
4. 4 KPIs: tareas pendientes + horas, vencidas sin cerrar, WIP actual, productividad 30d.
5. Sección "Hoy": tarjetas de _tareas asignadas hoy_ y _vencidas sin cerrar_, cada una clickable para abrir CU-10 con los filtros correspondientes pre-rellenados.
6. Sección de tareas en cuatro sub-flujos (pestañas), todos ejecutando CU-10 en modo contextual:
   - **Pendientes** (`status=pending`, `employee_id=X`, filtro de fechas opcional).
   - **Completadas** (`status=completed`, `employee_id=X`, filtro de fechas opcional).
   - **Asignadas** (`assigned_user=X`, toggle abierto/cerrado).
   - **Responsable** (`responsable_id=X`, toggle abierto/cerrado).
7. Click en tarea → CU-11.

**Flujos alternativos:**
- `FA-01`: El actor intenta acceder a un empleado fuera de su alcance → HTTP 403.
- `FA-02`: Empleado sin `user_id` vinculado → pestañas asignadas y pendientes vacías; workload y WIP a 0.

**Relaciones:** `<<include>>` CU-01. `<<extend>>` CU-10 (consulta contextual de tareas), CU-14 (workload/WIP).

### CU-08 – Listar Proyectos

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor localiza el proyecto y navega a CU-09. |

**Flujo principal:**
1. `GET /projects/` → cuadrícula de tarjetas con nombre, cliente y código.
2. El Responsable solo ve sus `project_ids`.
3. Click en tarjeta → CU-09.

**Relaciones:** `<<include>>` CU-01. Navega a CU-09.

---

### CU-09 – Consultar Resumen de Proyecto

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. El proyecto está en el alcance del actor. |
| **Postcondición** | El actor conoce el estado completo del proyecto (eficiencia, riesgo, rentabilidad, tareas y equipo). |

**Flujo principal:**
1. El actor accede a `/projects/{id}` (desde CU-08 o resultado de CU-22).
2. El sistema verifica `verify_project_scope`. Carga: `GET /projects/{id}`, `/dashboards/summary/project/{id}`, `/tasks/stages`.
3. Cabecera con nombre, cliente y badges de estado (Rentable/Pérdidas, Riesgo bajo/medio/alto).
4. 4 KPIs: índice de eficiencia, índice de riesgo, rentabilidad %, total tareas.
5. Gráfico de barras: horas estimadas vs reales.
6. **Pestaña Tareas:** ejecuta CU-10 en modo contextual (`project_id=X`). Filtros adicionales de estado y etapa disponibles dentro de la pestaña.
7. **Pestaña Equipo:** `GET /projects/{id}/employees` → empleados con horas imputadas, coste/h. Click en empleado → CU-05.

**Flujos alternativos:**
- `FA-01`: Sin horas imputadas → rentabilidad 0 %, gráfico de horas vacío.
- `FA-02`: Responsable sin acceso al proyecto → HTTP 403.

**Relaciones:** `<<include>>` CU-01. `<<extend>>` CU-10 (tareas del proyecto), CU-15 (riesgo calculado en `/dashboards/summary/project/{id}`). Navega a CU-05.

---

### CU-10 – Listar Tareas

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor localiza las tareas buscadas o accede al detalle (CU-11). |

**Flujo principal:**
1. Se ejecuta desde tres contextos posibles:
   - **Global** (`/tasks`): el actor elige filtros manualmente.
   - **Desde CU-09**: `project_id` pre-rellenado.
   - **Desde CU-05**: `employee_id` (y opcionalmente `status`) pre-rellenados.
2. `GET /tasks/filter` con parámetros combinables: `status`, `stage_id`, `project_id`, `employee_id`, `date_from`, `date_to`, `date_assign`, `root_only`, `sort_by`, `sort_order`, `page`, `page_size`.
3. Tabla paginada: nombre de la tarea, etapa (badge), horas estimadas, deadline, fecha cierre, estado (badge).
4. Ordenación global server-side. `stage_id` tiene prioridad sobre `status` cuando ambos están activos.
5. Click en tarea → CU-11.
6. El Responsable tiene sus tareas automáticamente restringidas a sus `project_ids`.

**Flujos alternativos:**
- `FA-01`: Sin tareas para los filtros aplicados → estado vacío con mensaje.
- `FA-02`: Responsable intentando filtrar por proyecto fuera de su alcance → HTTP 403.

**Relaciones:** `<<include>>` CU-01. `<<extend>>` desde CU-05 y CU-09. Navega a CU-11.

---

### CU-11 – Consultar Detalle de Tarea

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. La tarea pertenece a un proyecto en el alcance del actor. |
| **Postcondición** | El actor conoce todos los datos de la tarea. |

**Flujo principal:**
1. `GET /tasks/{id}` → ficha completa de la tarea.
2. **Sección info general:** proyecto (link a CU-09), deadline, fecha fin, fecha asignación.
3. **Sección personas:** responsable (link a CU-05) y empleados asignados (links a CU-05).
4. **Sección horas** _(si `planned_hours > 0`)_: KPIs de horas est./invertidas/restantes, barra de progreso (rojo si >100%), métrica de productividad.
5. **Sección subtareas** _(si las hay)_: lista clickable, cada una abre CU-11 recursivamente.
6. Si `parent_id` existe → enlace a la tarea padre (CU-11).

**Flujos alternativos:**
- `FA-01`: Tarea no encontrada → HTTP 404.
- `FA-02`: Responsable con tarea fuera de sus proyectos → HTTP 403.

**Relaciones:** `<<include>>` CU-01. `<<extend>>` desde CU-10. Navega a CU-05, CU-09, CU-11 (recursivo en subtareas).

---

### CU-12 – Consultar Productividad

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. |
| **Postcondición** | El actor conoce la eficiencia de ejecución frente a la estimación. |

**Flujo principal:**
1. El actor accede a la métrica en `/metrics` y opcionalmente selecciona empleado, proyecto y rango de fechas.
2. `GET /metrics/productivity` → promedio de `(planificadas/reales)×100` para tareas cerradas con horas > 0.
3. Gauge de % promedio + total tareas analizadas.
4. Gráfico de barras horizontal: top 8 tareas por productividad individual.
5. El Responsable sin filtros recibe el agregado ponderado de todos sus proyectos.

**Relaciones:** `<<include>>` CU-01. Accesible desde CU-05 (KPI de productividad 30d) y `/metrics`.

---

### CU-22 – Visualizar Gráficos Analíticos

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. |
| **Postcondición** | El actor analiza tendencias y distribuciones visualmente. |

**Flujo principal:**
1. El actor navega a `/graficos`. Selecciona rango de fechas y opcionalmente filtra por empleado, departamento o proyecto.
2. Se cargan en paralelo: `GET /charts/task-evolution` (líneas, semana/mes), `/charts/task-distribution` (donut), `/metrics/client-distribution` (barras horizontal), `/charts/workload-by-dept` (barras vertical).
3. Cuatro gráficos renderizados con Recharts. El actor puede cambiar la agrupación temporal semana/mes.

**Flujos alternativos:**
- `FA-01`: Sin datos → cada gráfico muestra "Sin datos en el período".
- `FA-02`: El Responsable no ve `client-distribution`; los demás filtran por su alcance.

**Relaciones:** `<<include>>` CU-01.

---

### CU-23 – Consultar Asistencia vs Imputaciones

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. |
| **Postcondición** | El actor detecta discrepancias entre presencia física e imputaciones en partes analíticos. |

**Flujo principal:**
1. El actor navega a `/asistencia`. Selecciona rango (por defecto: mes actual). Modo Equipo o Por Responsable (Director).
2. `GET /employees/attendance/comparison` → tabla por empleado con horas fichadas, imputadas, diferencia y `coverage_pct = (imputadas/fichadas)×100` con badge (OK ≥95 %, Revisar ≥80 %, Alerta <80 %).
3. 4 KPIs globales + gráfico de barras agrupadas (top 15 por fichadas).
4. Click en empleado → `GET /employees/attendance/{id}/daily` → serie diaria con barras agrupadas (fichadas/imputadas) y gráfico de línea de diferencia.
5. **Modo Por Responsable** _(Director)_: `GET /employees/attendance/manager/{id}` → equipo del responsable con CTE recursivo.

**Relaciones:** `<<include>>` CU-01.

---

### CU-24 – Consultar Rentabilidad Financiera

| Campo | Valor |
|---|---|
| **Actores** | **Director** (exclusivo) |
| **Precondición** | CU-01 con `role = "director"`. |
| **Postcondición** | El Director conoce la rentabilidad real por proyecto, cliente y responsable, y puede planificar la asignación de recursos. |

**Flujo principal:**
1. El actor navega a `/rentabilidad`. Selecciona rango de fechas y modo de filtro: Global, Por Proyecto, Por Responsable.
2. Base de datos: `account_analytic_line.amount` (positivo = ingreso, negativo = gasto).
3. `GET /metrics/profitability/summary` → 4 KPIs globales: ingresos, gastos, neto, rentabilidad %.
4. Gráfico de barras agrupadas (ingresos vs gastos por proyecto, top 12) y donut de estados (Ganancia/Neutro/Pérdida).
5. Tabla de proyectos (`/metrics/profitability/per-project`) con income, expense, net, horas y badge.
6. Tabla de clientes (`/metrics/profitability/per-client`) como sub-flujo (equivalente al antiguo CU-22).
7. Drill-down por responsable: seleccionar manager → botón "Ver detalle" → `/metrics/profitability/manager/{id}` → vista individual con sus proyectos.
8. Panel **Estado del equipo** (sub-flujo equivalente a CU-06 antiguo): `GET /dashboards/team-assignment` → tabla con carga actual de cada empleado para facilitar asignaciones. Filtrable por proyecto.

**Flujos alternativos:**
- `FA-01`: Sin partes analíticos en el período → todo a 0.
- `FA-02`: Un Responsable intenta acceder → HTTP 403, pantalla "Acceso restringido".

**Relaciones:** `<<include>>` CU-01. `<<extend>>` CU-10 (estado equipo/tareas).

---

### CU-25 – Realizar Búsqueda Global

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01. Mínimo 2 caracteres de entrada. |
| **Postcondición** | El actor localiza el recurso y navega a su resumen. |

**Flujo principal:**
1. El actor navega a `/search` (o atajo en sidebar).
2. Escribe ≥2 caracteres; debounce de 350 ms.
3. `GET /search/?q=…&entity=all` → resultados agrupados: tareas, proyectos, empleados (máx. 10 por tipo).
4. El actor puede filtrar por tipo de entidad con los botones de pestaña.
5. Click en tarea → CU-11; en proyecto → CU-09; en empleado → CU-05.
6. El Responsable recibe resultados filtrados por su alcance.

**Relaciones:** `<<include>>` CU-01. Navega a CU-05, CU-09, CU-11.

---

## 4. Prototipar Casos de Uso

### Prototipo CU-01 – Autenticarse

```
┌─────────────────────────────────────────────────────┐
│                                                     │
│              ┌──────────────────────────────┐      │
│              │  ⚡                           │      │
│              │  Netkia Analytics             │      │
│              │  Solo directores y responsables│     │
│              │                              │      │
│              │  ID de usuario               │      │
│              │  ┌──────────────────────────┐│      │
│              │  │  ej: 1                    ││      │
│              │  └──────────────────────────┘│      │
│              │                              │      │
│              │  [  ↑ Acceder               ]│      │
│              │                              │      │
│              │  [⚠ Error si procede]         │      │
│              └──────────────────────────────┘      │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

### Prototipo CU-02 – Overview del Sistema

```
┌─────────────────────────────────────────────────────────────────────┐
│  ● Sistema activo   Buenos días · lunes, 7 de abril                 │
│                                                                     │
│  ⚠ [rojo]   3 tareas vencidas sin cerrar                  Ver >    │
│  ⚠ [narj]  2 sobrecargados: Ana García, Pedro Ruiz       Ver >    │
│                                                                     │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌──────────┐  │
│  │ 📁 Proyectos │ │ 👥 Empleados │ │ ✅ Abiertas  │ │ ⚠ Venc. │  │
│  │      12      │ │      48      │ │     127      │ │    3     │  │
│  │              │ │              │ │ +5 esta sem  │ │          │  │
│  └──────────────┘ └──────────────┘ └──────────────┘ └──────────┘  │
│                                                                     │
│  ┌── Actividad 14 días ──────────────────┐  ┌── Salud ──────────┐ │
│  │        ___                           │  │ Cumplimiento  85% │ │
│  │    ___/   \___                       │  │ [████████──]      │ │
│  │ __/          \____                   │  │ Retrabajo      6% │ │
│  │ lun mar mié jue vie lun mar mié      │  │ [█──────────]     │ │
│  └──────────────────────────────────────┘  │ Lead Time   4.2d  │ │
│                                            │ Equipo: 35/2/5/3  │ │
│  ┌── Por estado ─────────────────────────┐ └───────────────────┘ │
│  │ ● En Progreso  ██████████████  72 57% │ ┌── Acceso rápido ───┐ │
│  │ ● Nueva        ██████          38 30% │ │ 🎯 Manager        │ │
│  │ ● Revisión     ████            12  9% │ │ 📁 Proyectos      │ │
│  │ Total: 127                            │ │ 🏢 Departamentos  │ │
│  └───────────────────────────────────────┘ └───────────────────┘ │
└─────────────────────────────────────────────────────────────────────┘
```

---

### Prototipo CU-05 – Resumen de Empleado

```
┌─────────────────────────────────────────────────────────────────────┐
│  ← Volver a empleados                                               │
│                                                                     │
│  ┌─┐  Ana García                         [Sobrecargado] 32.50 €/h  │
│  │A│  Sr. Developer · Desarrollo · ana@empresa.com                 │
│  └─┘                                                                │
│                                                                     │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐  │
│  │ 12 Pend.    │ │ 3 Vencidas  │ │ WIP: 6      │ │ Prod. 30d   │  │
│  │ 94.5h pend. │ │ sin cerrar  │ │ sobrecargado│ │   112.3%    │  │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘  │
│                                                                     │
│  ┌── Asignadas hoy ──────────────┐  ┌── Vencidas sin cerrar ────┐  │
│  │ ✅ API autenticación       4h │  │ ⚠ Diseño UX portal   2d  │  │
│  │ ✅ Tests unitarios backend  2h│  │ ⚠ Refactor DB         5d │  │
│  └───────────────────────────────┘  └──────────────────────────┘  │
│                                                                     │
│  [Pendientes (12)] [Completadas (28)] [Asignadas (15)] [Resp. (8)] │
│  Rango: [  fecha  ] → [  fecha  ]  [Todas][Abiertas][Cerradas]     │
│  ┌─────────────────────┬──────────┬──────┬──────┬────────────────┐ │
│  │ Tarea               │ Proyecto │ Est. │ Pend.│ Deadline       │ │
│  ├─────────────────────┼──────────┼──────┼──────┼────────────────┤ │
│  │ API autenticación   │ Portal   │  20h │  16h │ 10 abr 25 ⚠  │ │
│  │ Diseño UX dashboard │ ERP Int. │  40h │  22h │ 25 abr 25     │ │
│  └─────────────────────┴──────────┴──────┴──────┴────────────────┘ │
└─────────────────────────────────────────────────────────────────────┘
```

---

### Prototipo CU-07 – Resumen de Departamento

```
┌─────────────────────────────────────────────────────────────────────┐
│  ← Volver a departamentos                                           │
│                                                                     │
│  🏢 Desarrollo · Manager: Javier Torres                            │
│                                                                     │
│  ⚠ Sobrecargados: Ana García, Luis Martínez                        │
│                                                                     │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌──────────┐  │
│  │ 12 Empleados │ │ 2 Sobrecarg. │ │ 3 Subcargad. │ │ 1 S.Tar. │  │
│  └──────────────┘ └──────────────┘ └──────────────┘ └──────────┘  │
│                                                                     │
│  [Carga de trabajo]  [Empleados]                                    │
│  ┌─────────────────┬──────┬──────────┬───────────────┬───────────┐ │
│  │ Empleado        │ Pend.│ H.pend.  │ Carga         │ Estado    │ │
│  ├─────────────────┼──────┼──────────┼───────────────┼───────────┤ │
│  │ Ana García      │  12  │  94.5h   │ ████████ 165% │[Sobrecarg]│ │
│  │ Carlos Ruiz     │   5  │  38.0h   │ ████──── 95%  │[Normal]   │ │
│  │ María López     │   2  │  10.0h   │ ██────── 25%  │[Subcargad]│ │
│  └─────────────────┴──────┴──────────┴───────────────┴───────────┘ │
└─────────────────────────────────────────────────────────────────────┘
```

---

### Prototipo CU-09 – Resumen de Proyecto

```
┌─────────────────────────────────────────────────────────────────────┐
│  ← Volver a proyectos                                               │
│                                                                     │
│  📁 Portal Clientes · Cliente: ACME S.A.  [Rentable] [Riesgo Bajo] │
│                                                                     │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌──────────┐  │
│  │ Eficiencia   │ │ Riesgo       │ │ Rentabilidad │ │ 127 Tar. │  │
│  │   92.4%      │ │   18.5%      │ │   +12.3%     │ │          │  │
│  │ Desv. -8h    │ │ 4 en riesgo  │ │ Dif. +5.800€ │ │          │  │
│  └──────────────┘ └──────────────┘ └──────────────┘ └──────────┘  │
│                                                                     │
│  Horas estimadas vs reales:                                         │
│  Estimadas [████████████████████████████] 320h                     │
│  Reales    [████████████████████████████] 312h                     │
│                                                                     │
│  [Tareas (127)]  [Equipo (8)]                                       │
│  [Todas][Pendientes]  [Etapa ▾]                                     │
│  ┌─────────────────────┬──────────┬───────┬────────────┬──────────┐ │
│  │ Tarea               │ Etapa    │ Est.  │ Deadline   │ Estado   │ │
│  ├─────────────────────┼──────────┼───────┼────────────┼──────────┤ │
│  │ Diseño UX portal    │ Revisión │  40h  │ 15 abr 25  │[Abierta] │ │
│  │ API auth login      │ En Curso │  20h  │ 10 abr 25  │[Abierta]⚠│ │
│  └─────────────────────┴──────────┴───────┴────────────┴──────────┘ │
└─────────────────────────────────────────────────────────────────────┘
```

---

### Prototipo CU-10 – Listar Tareas

```
┌─────────────────────────────────────────────────────────────────────┐
│  Tareas · 127 tareas · pág. 1 de 6 · ordenado por Deadline ↑       │
│                                                                     │
│  [Todos los estados ▾] [Todas las etapas ▾] [Todos los proy. ▾]   │
│  Deadline desde [fecha] – [fecha]   ☐ Solo tareas padre            │
│  [Limpiar filtros ×]                                                │
│                                                                     │
│  ┌──────────────────────┬──────────┬───────┬────────────┬─────────┐ │
│  │ Tarea ↑              │ Etapa    │ H.est │ Deadline   │ Estado  │ │
│  ├──────────────────────┼──────────┼───────┼────────────┼─────────┤ │
│  │ API auth login       │ En Curso │  20h  │ 10 abr 25  │⚠Abierta│ │
│  │ Diseño UX portal     │ Revisión │  40h  │ 15 abr 25  │ Abierta │ │
│  │ Tests E2E            │ Cerrada  │  15h  │ 01 abr 25  │ Cerrada │ │
│  │ Migración base datos │ Nueva    │  60h  │ 30 abr 25  │ Abierta │ │
│  └──────────────────────┴──────────┴───────┴────────────┴─────────┘ │
│                   ‹ Anterior  [1] [2] [3]  Siguiente ›              │
└─────────────────────────────────────────────────────────────────────┘
```

---

### Prototipo CU-24 – Rentabilidad Financiera

```
┌─────────────────────────────────────────────────────────────────────┐
│  Rentabilidad · 2025-01-01 → 2025-06-30  [3m] [6m] [1a]           │
│  Filtro: [Global] [Por proyecto] [Por responsable]                  │
│                                                                     │
│  ┌──────────┐ ┌──────────┐ ┌──────────────────┐ ┌───────────────┐ │
│  │ 142.500€ │ │  98.200€ │ │    +44.300€       │ │    +31.1%     │ │
│  │ Ingresos │ │  Gastos  │ │       Neto        │ │ Rentabilidad  │ │
│  └──────────┘ └──────────┘ └──────────────────┘ └───────────────┘ │
│                                                                     │
│  [Por proyecto]  [Por cliente]                                      │
│  ┌──────────────────┬──────────┬──────────┬─────────┬─────────────┐ │
│  │ Proyecto         │ Ingr. €  │ Gasto €  │  Neto € │ Rentab.     │ │
│  ├──────────────────┼──────────┼──────────┼─────────┼─────────────┤ │
│  │ Portal Clientes  │  48.200  │  31.500  │ +16.700 │ [Ganancia]  │ │
│  │ ERP Interno      │  32.100  │  35.800  │  -3.700 │ [Pérdida]   │ │
│  └──────────────────┴──────────┴──────────┴─────────┴─────────────┘ │
│                                                                     │
│  Estado del equipo [Todo el equipo ▾]                               │
│  ┌──────────────┬─────────────┬──────┬────────────────────────────┐ │
│  │ Empleado     │ Cargo       │ €/h  │ Carga  Hoy                 │ │
│  ├──────────────┼─────────────┼──────┼────────────────────────────┤ │
│  │ Carlos Ruiz  │ Developer   │ 28€  │ 45%   [En progreso]        │ │
│  │ María López  │ Junior Dev  │ 22€  │ 25%   [Sin actividad]      │ │
│  └──────────────┴─────────────┴──────┴────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 5. Estructurar el Modelo de Casos de Uso


### 5.1 Diagrama de Contexto – Director

![Diagrama de Contexto - Director](./imagenes/contexto_director.png)

Aquí podemos observar que el Director tiene acceso a todos los casos de uso, sin restricciones de alcance, lo que se refleja en la ausencia de filtros específicos en las relaciones. Además, el Director es el único actor que puede acceder a CU-24 (Rentabilidad Financiera).

---

### 5.2 Diagrama de Contexto – Responsable

![Diagrama de Contexto - Responsable](./imagenes/contexto_responsable.png)

En este diagrama se muestra que el Responsable tiene acceso a la mayoría de los casos de uso, pero con restricciones de alcance basadas en sus `employee_ids`, `department_ids` y `project_ids`. Esto se indica mediante etiquetas en las relaciones, destacando que su acceso a ciertos casos de uso (como CU-09 Resumen de Proyecto) está condicionado por su rol y alcance específico dentro del sistema.

---

### 5.3 Relaciones Include / Extend del Modelo

Las relaciones `<<include>>` y `<<extend>>` se han detallado en cada caso de uso individualmente, indicando claramente qué casos de uso son precondiciones universales (`<<include>>`) y cuáles son sub-flujos opcionales o contextuales (`<<extend>>`).

---

Para la correcta interpretación del modelo, se han definido dos tipos de relaciones fundamentales que estructuran la interacción entre los módulos:

#### 1. Relaciones de Inclusión `<<include>>`
Representan comportamientos obligatorios que se insertan en un flujo para que este sea válido.
* **CU-01 (Autenticarse) como Precondición Universal:** Todos los casos de uso del sistema (`CU-02` al `CU-27`) **incluyen** obligatoriamente el `CU-01`. Sin una sesión activa y un JWT válido, el sistema deniega el acceso a los datos (HTTP 401/403).
* **Segmentación por Alcance:** En los casos de consulta (`CU-05`, `CU-07`, `CU-09`), la inclusión de `CU-01` no solo valida la sesión, sino que inyecta los filtros de seguridad (`employee_ids`, `project_ids`) que limitan la visibilidad de los datos según el rol.

#### 2. Relaciones de Extensión `<<extend>>`
Representan funcionalidades opcionales o saltos contextuales donde un caso de uso base se enriquece con la lógica de otro.

| Caso de Uso Base | Caso de Uso Extendido | Condición de Extensión |
| :--- | :--- | :--- |
| **CU-05** (Resumen Empleado) | **CU-10** (Listar Tareas) | Al seleccionar las pestañas de tareas (Pendientes/Completadas). |
| **CU-05** (Resumen Empleado) | **CU-14** (Carga/WIP) | Inserción de KPIs de ocupación y paralelismo en la ficha. |
| **CU-09** (Resumen Proyecto) | **CU-10** (Listar Tareas) | Al navegar a la pestaña "Tareas" filtrada por proyecto. |
| **CU-09** (Resumen Proyecto) | **CU-15** (Riesgo) | Cálculo automático del índice de riesgo del proyecto. |
| **CU-10** (Listar Tareas) | **CU-11** (Detalle Tarea) | Al hacer click en una fila específica de la tabla. |
| **CU-24** (Rentabilidad) | **CU-10** (Listar Tareas) | En el panel de "Estado del equipo" para ver tareas actuales. |
| **CU-24** (Rentabilidad) | **CU-26** / **CU-27** | Al solicitar el "Drill-down" de líneas analíticas por Proyecto o Cliente. |
