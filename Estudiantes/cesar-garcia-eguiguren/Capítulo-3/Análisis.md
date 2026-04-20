# Disciplina de Análisis

## Índice

1. [Análisis de la Arquitectura](#1-análisis-de-la-arquitectura)
   - 1.1 [Visión general del módulo](#11-visión-general-del-módulo)
   - 1.2 [Diagrama de arquitectura del sistema](#12-diagrama-de-arquitectura-del-sistema)
   - 1.3 [Restricción fundamental: modo solo lectura](#13-restricción-fundamental-modo-solo-lectura)
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
   - 4.3 [Paquetes del frontend](#43-paquetes-del-frontend)


---

## 1. Análisis de la Arquitectura

### 1.1 Visión general del módulo

Netkia Analytics es un **módulo de análisis operativo** que se conecta en modo solo lectura a la base de datos PostgreSQL de un ERP Odoo v16. La solución se articula en tres componentes diferenciados:

- **Frontend (React SPA):** interfaz de usuario que los directores y responsables de área utilizan para consultar métricas, dashboards y reportes. Se ejecuta en el navegador y se comunica con el backend exclusivamente a través de HTTP/JSON.
- **Backend (FastAPI):** API REST que recibe las peticiones del frontend, aplica lógica de negocio, ejecuta consultas contra la base de datos y devuelve los resultados en formato JSON.
- **Base de datos (PostgreSQL):** instancia existente de Odoo v16. El módulo accede a las tablas relevantes (`project_task`, `hr_employee`, `account_analytic_line`, etc.) sin alterar el esquema ni escribir datos.

Esta separación garantiza que Netkia Analytics no interfiere con el funcionamiento del ERP, al tiempo que permite desarrollar la capa de análisis de forma autónoma.

### 1.2 Diagrama de arquitectura del sistema

El diagrama de componentes muestra la arquitectura física desplegada, con el frontend React comunicándose con el backend FastAPI a través de HTTP, y el backend accediendo a la base de datos PostgreSQL mediante SQLAlchemy. El backend se organiza en capas (Routes, Services, Repositories, Models) que siguen principios de separación de responsabilidades y una única razón para cambiar.

![Diagrama de Componentes](./imagenes/paquetesSistema.png)

### 1.3 Restricción fundamental: modo solo lectura

El acceso exclusivo de lectura a la base de datos de Odoo determina varias decisiones de arquitectura: no se utilizan patrones de escritura como Unit of Work ni transacciones de modificación; todos los modelos ORM mapean tablas existentes sin añadir columnas; y la lógica del módulo se concentra en optimizar consultas y calcular métricas, no en persistir estado propio.

---

## 2. Análisis de Casos de Uso

Esta sección describe qué sucede en cada caso de uso desde el punto de vista del actor, sin entrar en detalles de implementación. Para cada CU se indica el actor que lo inicia, los datos de entrada que proporciona y la información que el sistema devuelve.

### 2.1 Actores

| Actor | Descripción |
|---|---|
| **Director** | Acceso total. Ve datos de toda la organización: todos los empleados, proyectos, departamentos y métricas financieras. |
| **Responsable** | Acceso restringido a su ámbito (empleados de su equipo, proyectos que gestiona, departamentos a su cargo). No puede acceder a datos de rentabilidad financiera. |

### 2.2 Casos de uso del sistema

**CU-01 — Autenticarse**
- Actor: Director, Responsable
- Entrada: credenciales de usuario (login + contraseña Odoo)
- Salida: token JWT con rol y ámbito organizativo embebidos; perfil del usuario

**CU-02 — Listar empleados**
- Actor: Director, Responsable
- Entrada: filtros opcionales (departamento, búsqueda por nombre, estado activo/inactivo, ordenación, paginación)
- Salida: lista paginada de empleados con nombre, cargo, departamento, email y coste por hora

**CU-03 — Ver resumen de empleado**
- Actor: Director, Responsable
- Entrada: identificador del empleado
- Salida: ficha del empleado + indicadores de carga de trabajo (% ocupación, horas pendientes), WIP (tareas en paralelo), productividad de los últimos 30 días y contadores de tareas por estado

**CU-04 — Listar departamentos**
- Actor: Director, Responsable
- Entrada: ninguna (filtros opcionales de búsqueda)
- Salida: lista de departamentos activos con nombre, manager y jerarquía

**CU-05 — Ver detalle de departamento**
- Actor: Director, Responsable
- Entrada: identificador del departamento
- Salida: ficha del departamento + carga de trabajo del equipo (% por empleado, sobrecargados, subcargados, sin tareas) + listado de empleados

**CU-06 — Listar proyectos**
- Actor: Director, Responsable
- Entrada: ninguna
- Salida: lista de proyectos activos con nombre, cliente y código

**CU-07 — Ver detalle de proyecto**
- Actor: Director, Responsable
- Entrada: identificador del proyecto
- Salida: ficha del proyecto + métricas de eficiencia, riesgo y rentabilidad por horas + listado de tareas y equipo asignado

**CU-08 — Listar y filtrar tareas**
- Actor: Director, Responsable
- Entrada: filtros combinables (estado, etapa, proyecto, departamento, fechas, empleado, responsable, solo-raíz, paginación, ordenación)
- Salida: lista paginada de tareas con nombre, etapa, horas estimadas, deadline y estado; las tareas filtradas por empleado incluyen horas trabajadas, pendientes y productividad según el estado seleccionado

**CU-09 — Ver detalle de tarea**
- Actor: Director, Responsable
- Entrada: identificador de la tarea
- Salida: toda la información de la tarea (fechas, horas, etapa, responsable, usuarios asignados, subtareas, progreso de horas, productividad)

**CU-10 — Consultar productividad**
- Actor: Director, Responsable
- Entrada: filtros opcionales (empleado, proyecto, rango de fechas, solo-raíz)
- Salida: productividad media, número de tareas analizadas, ranking de tareas con horas estimadas vs reales

**CU-11 — Consultar cumplimiento de plazos**
- Actor: Director, Responsable
- Entrada: filtros opcionales (empleado, proyecto, solo-raíz)
- Salida: tasa de cumplimiento (%), tareas completadas a tiempo y total analizadas

**CU-12 — Consultar WIP**
- Actor: Director, Responsable
- Entrada: identificador del empleado
- Salida: número de tareas abiertas asignadas, estado (óptimo/aceptable/sobrecargado) y recomendación

**CU-13 — Consultar carga de trabajo**
- Actor: Director, Responsable
- Entrada: identificador del empleado, modo detallado (opcional)
- Salida: porcentaje de ocupación, horas pendientes, estado (normal/sobrecargado/subcargado), listado de tareas pendientes y completadas en los últimos 30 días

**CU-14 — Consultar índice de riesgo**
- Actor: Director, Responsable
- Entrada: identificador del proyecto
- Salida: porcentaje de tareas en riesgo, número de tareas en riesgo y total de tareas abiertas

**CU-15 — Consultar tasa de retrabajo**
- Actor: Director, Responsable
- Entrada: filtros opcionales (proyecto, empleado)
- Salida: porcentaje de tareas cerradas que han sido reabiertas, número de reabiertas y total

**CU-16 — Consultar exactitud de estimación**
- Actor: Director, Responsable
- Entrada: identificador del responsable
- Salida: porcentaje de exactitud, ratio medio, sesgo (subestima/sobreestima/preciso), número de tareas

**CU-17 — Consultar lead time**
- Actor: Director, Responsable
- Entrada: filtros opcionales (empleado, proyecto)
- Salida: tiempo medio en días desde asignación hasta cierre, número de tareas analizadas

**CU-18 — Consultar tiempo por estado**
- Actor: Director, Responsable
- Entrada: filtros opcionales (proyecto, empleado)
- Salida: tiempo medio (horas) que las tareas permanecen en cada etapa del flujo

**CU-19 — Consultar tareas canceladas**
- Actor: Director, Responsable
- Entrada: filtros opcionales (proyecto, empleado, rango de fechas)
- Salida: número de tareas canceladas, porcentaje sobre el total, listado de tareas con horas imputadas

**CU-20 — Consultar tiempo por prioridad**
- Actor: Director, Responsable
- Entrada: filtros opcionales (proyecto, empleado)
- Salida: tiempo medio (horas) que las tareas tardan en finalizar por cada nivel de prioridad

**CU-21 — Consultar gráficos analíticos**
- Actor: Director, Responsable
- Entrada: rango de fechas, agrupación (semana/mes), filtros de entidad (empleado/departamento/proyecto)
- Salida: gráficos de evolución temporal (tareas abiertas/cerradas/vencidas), distribución de tareas por estado, distribución de horas por cliente (solo para director)

**CU-22 — Comparar asistencia vs partes**
- Actor: Director, Responsable
- Entrada: rango de fechas, filtros opcionales (departamento, empleado o responsable)
- Salida: horas fichadas vs horas imputadas por empleado, diferencia, porcentaje de cobertura y estado (ok/revisar/alerta); serie diaria cuando se filtra por empleado

**CU-23 — Consultar rentabilidad financiera global**
- Actor: Director
- Entrada: rango de fechas, filtros opcionales (proyecto o responsable)
- Salida: ingresos totales, gastos totales, neto, rentabilidad (%) y estado por proyecto

**CU-24 — Ver líneas analíticas de proyecto**
- Actor: Director
- Entrada: rango de fechas
- Salida: agrupación por cliente con ingresos, gastos, neto, horas y rentabilidad

**CU-25 — Ver líneas analíticas de cliente**
- Actor: Director
- Entrada: identificador de proyecto o cliente, rango de fechas
- Salida: detalle línea a línea de ingresos y gastos del project analytic lines de Odoo

**CU-26 — Buscar globalmente**
- Actor: Director, Responsable
- Entrada: término de búsqueda (mínimo 2 caracteres), tipo de entidad (all/tasks/projects/employees), límite
- Salida: coincidencias de tareas, proyectos y empleados con nombre, código y metadatos

**CU-27 - Cerrar sesión**
- Actor: Director, Responsable
- Entrada: ninguna
- Salida: token JWT invalidado (el frontend borra el token localmente y redirige al login)

**CU-28 — Panel manager**
- Actor: Director, Responsable
- Entrada: filtros opcionales (departamento, estado de carga, paginación)
- Salida: distribución del equipo por estado de carga, top empleados sobrecargados, tabla paginada por estado

---

## 3. Análisis de Clases

### 3.1 Capas de la solución

La solución sigue una **arquitectura por capas** con cuatro niveles en el backend y una SPA independiente en el frontend. Las dependencias fluyen exclusivamente hacia abajo (nunca ascienden).



| Capa | Responsabilidad | Cambios por |
|------|-----------------|-------------|
| **Routes** | Enrutamiento HTTP FastAPI | Nuevos endpoints, nueva interfaz HTTP |
| **Services** | Lógica de negocio, cálculos, orquestación | Reglas de negocio, fórmulas, algoritmos |
| **Repositories** | Acceso a datos, queries SQL | Cambios en estructura de tablas Odoo |
| **Models** | Mapeo de tablas Odoo con SQLAlchemy | Presencia/ausencia de columnas en Odoo |
| **Schemas** | Contratos JSON Pydantic | Nuevos campos en respuestas API |
| **Core** | Configuración, autenticación, base de datos | Cambios globales de configuración |
| **Utils** | Constantes y utilidades compartidas | Nuevas constantes, nuevos helpers |

Cada capa tiene **una única razón para cambiar** (Single Responsibility Principle).

### 3.2 Identificación de clases de análisis

Siguiendo la metodología RUP, las clases se clasifican en tres estereotipos:

**Clases Modelo (Entidad) — tablas Odoo mapeadas con SQLAlchemy:**

| Clase | Tabla Odoo | Datos principales |
|---|---|---|
| `Task` | `project_task` | id, name, planned_hours, is_closed, stage_id, project_id, responsable_id, date_deadline |
| `TaskStage` | `project_task_type` | id, name, closed |
| `Employee` | `hr_employee` | id, name, hourly_cost, department_id, user_id |
| `Department` | `hr_department` | id, name, manager_id, parent_id |
| `Project` | `project_project` | id, name, partner_id, date_start |
| `Timesheet` | `account_analytic_line` | id, unit_amount, amount, task_id, employee_id, date |
| `Attendance` | `hr_attendance` | id, employee_id, check_in, check_out, worked_hours |
| `TaskAssignment` | `project_task_user_rel` | task_id, user_id |
| `ResUsers` | `res_users` | id, login |
| `Partner` | `res_partner` | id, name |
| `MailMessage` | `mail_message` | id, model, res_id, date |
| `MailTrackingValue` | `mail_tracking_value` | mail_message_id, old_value_integer, new_value_integer |

**Clases Vista (Interfaz) — páginas React:**

| Clase | Actor | CU asociados |
|---|---|---|
| `Login` | Ambos | CU-01 |
| `Overview` | Ambos | Panel de inicio |
| `Employees` / `EmployeeDetail` | Ambos | CU-02, CU-03 |
| `Departments` / `DepartmentDetail` | Ambos | CU-04, CU-05 |
| `Projects` / `ProjectDetail` | Ambos | CU-06, CU-07 |
| `Tasks` / `TaskDetail` | Ambos | CU-08, CU-09 |
| `Metrics` | Ambos | CU-10 a CU-20 |
| `Manager` | Ambos | CU-28 |
| `Attendance` | Ambos | CU-22 |
| `Rentability` | Director | CU-23, CU-24, CU-25 |
| `Charts` | Ambos | CU-21 |
| `Search` | Ambos | CU-26 |

**Clases Controlador — routers FastAPI:**

| Clase | CU coordinados | Servicio al que delega |
|---|---|---|
| `auth.router` | CU-01 | `auth_service`, `scope_service` |
| `employee.router` | CU-02, CU-22 | `EmployeeService`, `AttendanceService` |
| `department.router` | CU-04, CU-05 | `DepartmentService` |
| `project.router` | CU-06, CU-07 | `ProjectService` |
| `task.router` | CU-08, CU-09 | `TaskService` |
| `metrics.router` | CU-10 a CU-21, CU-23-25 | 15 servicios de métricas + `RentabilityService` |
| `dashboards.router` | CU-03, CU-05, CU-07, CU-28 | `DashboardService` |
| `charts.router` | CU-21 | `ChartService` |
| `search.router` | CU-26 | `SearchService` |

### 3.3 Relaciones entre clases del dominio

| Relación | Tipo | Cardinalidad |
|---|---|---|
| `Project` → `Task` | Composición | 1 a 0..* |
| `Task` → `TaskStage` | Asociación | * a 1 |
| `Task` → `Task` | Auto-referencia (subtareas) | * a 0..1 |
| `Employee` → `Department` | Agregación | * a 0..1 |
| `Task` → `Employee` (responsable) | Asociación | * a 0..1 |
| `Task` ↔ `ResUsers` | Asociación M:N | * a * (via `TaskAssignment`) |
| `Timesheet` → `Task` | Asociación | * a 0..1 |
| `Timesheet` → `Employee` | Asociación | * a 1 |
| `Project` → `Partner` | Asociación | * a 0..1 |
| `MailTrackingValue` → `MailMessage` | Asociación | * a 1 |

---

## 4. Análisis de Paquetes

### 4.1 Estructura de paquetes del backend

El backend se distribuye en carpetas siguiendo el criterio de **cohesión funcional**: cada paquete agrupa módulos con la misma naturaleza de responsabilidad. La estructura relevante es:

```
app/
├── core/           → Configuración, seguridad JWT, conexión DB, constantes
├── models/         → Clases ORM (una por entidad Odoo)
├── schemas/        → Modelos Pydantic de entrada/salida
├── repositories/   → Funciones de acceso a datos agrupadas por entidad
│   └── metrics/    → Sub-paquete: una función por métrica operativa
├── services/       → Lógica de negocio agrupada por dominio
│   └── metrics/    → Sub-paquete: una clase por métrica operativa
├── routes/         → Controladores HTTP (un router por recurso)
└── utils/          → Funciones transversales (paginación, traducción, validación)
```

### 4.2 Justificación de la estructura

**`core/`** agrupa todo lo que es infraestructura transversal: configuración de entorno, generación y validación de tokens JWT, pool de conexiones y constantes de dominio compartidas (umbrales, etiquetas, ventanas temporales). Cambia solo cuando cambia la infraestructura, no el negocio.

**`models/`** contiene exclusivamente el mapeo ORM. Ningún modelo ejecuta lógica; solo declaran columnas y relaciones.

**`repositories/`** encapsula cada consulta SQL como una función pura que recibe una sesión y devuelve datos crudos. El sub-paquete `repositories/metrics/` extiende esta idea con un submódulo por métrica, evitando que un único fichero de 200 líneas acumule consultas heterogéneas.

**`services/`** aplica las reglas de negocio sobre los datos recuperados por los repositorios. El sub-paquete `services/metrics/` sigue la misma granularidad que `repositories/metrics/`: una clase, una métrica, una razón de cambio.

**`routes/`** actúa como capa de entrada HTTP. Los routers no contienen lógica de negocio: validan parámetros, comprueban autenticación y delegan en el servicio correspondiente.

**`utils/`** recoge funciones reutilizables que no pertenecen a ningún dominio: paginación, extracción de nombres de campos JSONB, ordenación de diccionarios y validaciones de rango de fechas.

### 4.3 Paquetes del frontend

Aunque el diseño de calidad recae principalmente sobre el backend, el frontend también sigue una estructura orientada a la separación de responsabilidades:

```
src/
├── api/            → Un módulo por dominio (employees.js, tasks.js, metrics.js...)
├── components/     → Componentes reutilizables (Card, Table, KpiCard, Sidebar...)
├── context/        → Estado global de autenticación (AuthContext)
├── hooks/          → Lógica reutilizable (useApi, useDebounce)
├── pages/          → Una página por recurso o funcionalidad
├── styles/         → Variables CSS globales y tokens de diseño
└── utils/          → Formateadores y helpers (formatters.js)
```

---
