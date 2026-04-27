# Casos de Uso Detallados

## CU-01 – Autenticarse en el Sistema

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | El usuario dispone de credenciales válidas en el sistema ERP con un empleado activo vinculado. |
| **Postcondición** | El usuario tiene una sesión activa y accede al sistema con el rol y ámbito que le corresponden. |

![Diagrama de flujo de autenticación](../imagenes/CdU/flujoCU01.png)

**Flujo principal:**
1. El actor accede a la pantalla de inicio de sesión e introduce sus credenciales.
2. El sistema valida las credenciales contra el ERP.
3. El sistema verifica que existe un empleado activo asociado al usuario.
4. El sistema determina el rol del usuario: Director (acceso global) o Responsable (acceso filtrado).
5. Para el Responsable, el sistema calcula el ámbito organizativo que le corresponde.
6. Se crea la sesión del usuario y se redirige a la pantalla principal.

**Flujos alternativos:**
- `FA-01`: Credenciales incorrectas → mensaje de error, permanece en el login.
- `FA-02`: Sin empleado activo vinculado → acceso denegado.
- `FA-03`: Usuario sin permisos de acceso al sistema → acceso denegado con mensaje informativo.
- `FA-04`: Sesión expirada → el sistema cierra la sesión y redirige al login.

**Relaciones:** CU-01 es precondición de todos los demás CU.

---

## CU-02 – Listar y Buscar Empleados

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor localiza el empleado buscado y puede navegar a CU-03. |

![Diagrama de flujo](../imagenes/CdU/flujoCU02.png)

**Flujo principal:**
1. El actor accede al listado de empleados.
2. El sistema muestra los empleados disponibles según su rol y ámbito.
3. El actor puede filtrar por nombre, departamento o estado (activo/inactivo).
4. El sistema actualiza el listado paginado con los filtros aplicados.
5. El actor selecciona un empleado para consultar su resumen.

**Flujos alternativos:**
- `FA-01`: Sin resultados con los filtros aplicados → estado vacío con mensaje.

**Relaciones:** Navega a CU-03.

---

## CU-03 – Consultar Resumen de Empleado

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. El empleado pertenece al ámbito del actor. |
| **Postcondición** | El actor conoce el estado completo del empleado: carga, WIP, productividad y tareas. |

![Diagrama de flujo](../imagenes/CdU/flujoCU03.png)

**Flujo principal:**
1. El actor selecciona un empleado desde CU-02 o CU-05.
2. El sistema verifica que el empleado pertenece al ámbito del actor.
3. El sistema muestra el perfil del empleado con sus indicadores de rendimiento.
4. El actor navega por las pestañas de tareas (pendientes, completadas, asignadas, como responsable).
5. El actor puede filtrar las tareas por rango de fechas.
6. El actor puede seleccionar una tarea para ver su detalle.

**Flujos alternativos:**
- `FA-01`: Empleado fuera del ámbito → acceso denegado.
- `FA-02`: Empleado sin usuario vinculado → indicadores a 0, tareas vacías.

**Relaciones:** `<<extend>>` por CU-08 (listados de tareas contextuales). Navega a CU-09. `<<extend>>` hacia CU-17 (guardar snapshot de la ficha).

---

## CU-04 – Listar Departamentos

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor localiza el departamento y puede navegar a CU-05. |

![Diagrama de flujo](../imagenes/CdU/flujoCU04.png)

**Flujo principal:**
1. El actor accede al listado de departamentos.
2. El sistema muestra los departamentos activos disponibles según su ámbito.
3. El actor selecciona un departamento para consultar su resumen.

**Flujos alternativos:**
- `FA-01`: Sin departamentos en el ámbito → estado vacío.

**Relaciones:** Navega a CU-05.

---

## CU-05 – Consultar Resumen de Departamento

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. El departamento pertenece al ámbito del actor. |
| **Postcondición** | El actor conoce el estado de carga del departamento y puede navegar a sus empleados. |

![Diagrama de flujo](../imagenes/CdU/flujoCU05.png)

**Flujo principal:**
1. El actor selecciona un departamento desde CU-04.
2. El sistema verifica que el departamento pertenece al ámbito del actor.
3. El sistema muestra el panel con nombre, responsable e indicadores de distribución de carga.
4. El sistema alerta visualmente si hay empleados sobrecargados.
5. El actor puede consultar la carga de trabajo o el listado de empleados mediante pestañas.
6. El actor puede seleccionar un empleado para acceder a su resumen.

**Flujos alternativos:**
- `FA-01`: Departamento fuera del ámbito → acceso denegado.
- `FA-02`: Departamento sin empleados → pestañas vacías.

**Relaciones:** Navega a CU-03. `<<extend>>` hacia CU-17.

---

## CU-06 – Listar Proyectos

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor localiza el proyecto y puede navegar a CU-07. |

![Diagrama de flujo](../imagenes/CdU/flujoCU06.png)

**Flujo principal:**
1. El actor accede al listado de proyectos.
2. El sistema muestra los proyectos activos disponibles según su ámbito.
3. El actor selecciona un proyecto para consultar su resumen.

**Flujos alternativos:**
- `FA-01`: Sin proyectos en el ámbito → estado vacío.

**Relaciones:** Navega a CU-07.

---

## CU-07 – Consultar Resumen de Proyecto

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. El proyecto pertenece al ámbito del actor. |
| **Postcondición** | El actor conoce el estado completo del proyecto: eficiencia, riesgo, rentabilidad, tareas y equipo. |

![Diagrama de flujo](../imagenes/CdU/flujoCU07.png)

**Flujo principal:**
1. El actor selecciona un proyecto desde CU-06 o desde los resultados de CU-15.
2. El sistema verifica que el proyecto pertenece al ámbito del actor.
3. El sistema muestra el panel del proyecto con indicadores de eficiencia, riesgo y rentabilidad.
4. El actor puede consultar las tareas del proyecto o los miembros del equipo mediante pestañas.
5. El actor puede filtrar las tareas por estado o etapa.
6. El actor puede seleccionar una tarea o un empleado para ver su detalle.

**Flujos alternativos:**
- `FA-01`: Sin horas registradas → rentabilidad y eficiencia a 0.
- `FA-02`: Proyecto fuera del ámbito → acceso denegado.

**Relaciones:** Navega a CU-03 y CU-09. `<<extend>>` hacia CU-17.

---

## CU-08 – Listar Tareas

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor localiza las tareas buscadas o accede a su detalle (CU-09). |

![Diagrama de flujo](../imagenes/CdU/flujoCU08.png)

**Flujo principal:**
1. El actor accede al listado de tareas, bien desde la sección global, bien desde CU-07 o CU-03.
2. El sistema muestra las tareas disponibles según el ámbito del actor, con los filtros contextuales preseleccionados si los hubiera.
3. El actor puede combinar filtros: estado, etapa, proyecto, empleado, rango de fechas y tareas principales.
4. El sistema actualiza el listado paginado con los filtros aplicados.
5. El actor selecciona una tarea para ver su detalle.

**Flujos alternativos:**
- `FA-01`: Sin tareas con los filtros aplicados → estado vacío con mensaje.
- `FA-02`: Filtro por proyecto o empleado fuera del ámbito → acceso denegado.

**Relaciones:** Navega a CU-09.

---

## CU-09 – Consultar Detalle de Tarea

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. La tarea pertenece a un proyecto en el ámbito del actor. |
| **Postcondición** | El actor conoce todos los datos de la tarea. |

![Diagrama de flujo](../imagenes/CdU/flujoCU09.png)

**Flujo principal:**
1. El actor selecciona una tarea desde CU-08, CU-03 o CU-07.
2. El sistema verifica que la tarea pertenece al ámbito del actor.
3. El sistema muestra la ficha completa: información general, personas responsables y asignadas, horas y subtareas.
4. El actor puede navegar al proyecto de la tarea, a los perfiles de los empleados implicados, a las subtareas o a la tarea padre si existe.

**Flujos alternativos:**
- `FA-01`: Tarea no encontrada → mensaje de error.
- `FA-02`: Tarea fuera del ámbito → acceso denegado.

**Relaciones:** Navega a CU-03, CU-07, CU-09 (recursivo en subtareas). `<<extend>>` hacia CU-17.

---

## CU-10 – Consultar Métrica Operativa

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. Los parámetros que requiera la métrica elegida están dentro del ámbito del actor. |
| **Postcondición** | El actor ha consultado el valor de una métrica con los filtros configurados. |

![Diagrama de flujo](../imagenes/CdU/flujoCU10.png)

**Flujo principal:**
1. El actor accede a la página de métricas.
2. El sistema muestra las métricas disponibles agrupadas por categoría (proyecto, empleado, equipo y generales).
3. El actor selecciona una métrica.
4. El sistema muestra los parámetros que requiere esa métrica (empleado, proyecto, departamento, rango de fechas, etc.).
5. El actor configura los parámetros y confirma.
6. El sistema verifica que los parámetros están dentro del ámbito del actor.
7. El sistema calcula la métrica y muestra el panel de detalle con sus indicadores y gráficos específicos.

**Flujos alternativos:**
- `FA-01`: Parámetro obligatorio sin informar → el sistema muestra un estado vacío solicitando el parámetro.
- `FA-02`: Parámetros fuera del ámbito del actor → acceso denegado.
- `FA-03`: Sin datos para los filtros → el sistema muestra un panel vacío con mensaje informativo.

**Observación:** Caso de uso único parametrizado por el nombre de la métrica. La métrica de carga de trabajo admite además el modo agregado de equipo, que se activa cuando el actor no especifica un empleado concreto.

**Relaciones:** `<<extend>>` hacia CU-17 (guardar snapshot de la métrica calculada).

---

## CU-11 – Visualizar Gráficos Analíticos

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor ha visualizado los gráficos filtrados por su ámbito y configuración. |

![Diagrama de flujo](../imagenes/CdU/flujoCU11.png)

**Flujo principal:**
1. El actor accede a la página de gráficos.
2. El actor configura el rango de fechas, la agrupación temporal (semanal o mensual) y la entidad de análisis (empresa, empleado, departamento o proyecto).
3. El sistema genera los gráficos disponibles: evolución temporal de tareas y distribución por estado actual.
4. Si el actor es Director, el sistema añade la distribución de horas por cliente.

**Flujos alternativos:**
- `FA-01`: Sin datos en el período → mensaje informativo en cada gráfico.
- `FA-02`: Responsable → no visualiza la distribución por cliente.

**Relaciones:** `<<extend>>` hacia CU-17 (guardar snapshot del gráfico).

---

## CU-12 – Consultar Asistencia vs Imputaciones

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable (modo equipo global) |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor conoce la cobertura horaria del período analizado. |

![Diagrama de flujo](../imagenes/CdU/flujoCU12.png)

**Flujo principal:**
1. El actor accede a la página de asistencia.
2. El actor configura el rango de fechas y, opcionalmente, un departamento.
3. Si el actor es Director, puede elegir entre modo equipo global o modo por responsable.
4. El sistema compara las horas fichadas con las horas imputadas en partes de horas para cada empleado del ámbito.
5. El sistema muestra indicadores globales, gráfico comparativo y tabla con la cobertura individual y su semáforo.
6. Al seleccionar un empleado, el sistema muestra la serie diaria de horas fichadas e imputadas.

**Flujos alternativos:**
- `FA-01`: Sin datos en el período → indicadores a 0 y tabla vacía.
- `FA-02`: Responsable → solo puede usar el modo equipo global.

**Relaciones:** Ninguna.

---

## CU-13 – Analizar Rentabilidad Financiera ★

| Campo | Valor |
|---|---|
| **Actores** | Director (exclusivo) |
| **Precondición** | CU-01 completado con rol Director. |
| **Postcondición** | El Director ha consultado ingresos, gastos y rentabilidad del período. |

![Diagrama de flujo](../imagenes/CdU/flujoCU13.png)

**Flujo principal:**
1. El Director accede a la página de rentabilidad financiera.
2. El sistema valida el rol; en caso contrario muestra pantalla de acceso restringido.
3. El Director configura el rango de fechas y el modo de análisis (global, por proyecto o por responsable).
4. El sistema muestra el resumen financiero: ingresos, gastos, resultado neto, rentabilidad en porcentaje y distribución de proyectos entre ganancia, neutro y pérdida.
5. El Director selecciona una pestaña de desglose (por proyecto o por cliente).

**Flujos alternativos:**
- `FA-01`: Sin partes analíticos en el período → indicadores a 0.
- `FA-02`: Actor sin rol Director → acceso denegado.

**Relaciones:** `<<extend>>` hacia CU-14 (ver detalles) y CU-17 (guardar snapshot).

---

## CU-14 – Consultar Líneas Analíticas ★

| Campo | Valor |
|---|---|
| **Actores** | Director (exclusivo) |
| **Precondición** | El Director está en CU-13 con un resultado de rentabilidad mostrado y pulsa "Ver detalles" sobre una fila. |
| **Postcondición** | El Director ha visto el desglose de ingresos y gastos del ámbito elegido (proyecto o cliente). |

![Diagrama de flujo](../imagenes/CdU/flujoCU14.png)

**Flujo principal:**
1. Desde CU-13, el Director solicita el detalle de una fila. El ámbito (proyecto o cliente) queda determinado por la pestaña de origen.
2. El sistema recupera las líneas analíticas del proyecto o del conjunto de proyectos del cliente en el período indicado.
3. El sistema clasifica las líneas en ingresos (importes positivos) y gastos (importes negativos).
4. El sistema muestra dos tablas paralelas con la fecha, descripción, importe, horas y, si corresponde, el proyecto de origen de cada línea.

**Flujos alternativos:**
- `FA-01`: Sin líneas en el período → tablas vacías con mensaje.

**Observación:** Caso de uso único parametrizado por el ámbito. No tiene ruta de entrada propia desde el menú principal.

**Relaciones:** Invocado siempre desde CU-13 vía `<<extend>>`.

---

## CU-15 – Realizar Búsqueda Global

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado. |
| **Postcondición** | El actor ha encontrado una entidad y ha navegado a su detalle. |

![Diagrama de flujo](../imagenes/CdU/flujoCU15.png)

**Flujo principal:**
1. El actor abre la búsqueda global e introduce al menos dos caracteres.
2. El sistema busca coincidencias en tareas, proyectos y empleados, aplicando el ámbito del actor.
3. El sistema muestra los resultados agrupados por tipo. El actor puede filtrar por tipo.
4. Al seleccionar un resultado, el sistema navega a CU-03, CU-07 o CU-09 según corresponda.

**Flujos alternativos:**
- `FA-01`: Menos de dos caracteres → el sistema solicita más caracteres.
- `FA-02`: Sin resultados → estado vacío.

**Relaciones:** Navega a CU-03, CU-07 y CU-09.

---

## CU-16 – Cerrar Sesión

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | Existe una sesión activa. |
| **Postcondición** | La sesión queda invalidada y las credenciales locales eliminadas. |

![Diagrama de flujo](../imagenes/CdU/flujoCU16.png)

**Flujo principal:**
1. El actor pulsa "Cerrar sesión".
2. El sistema invalida la sesión y elimina los datos almacenados en el navegador.
3. El sistema redirige al actor a la pantalla de inicio de sesión.

**Observación:** Invocable tanto desde el frontend principal como desde el visor de snapshots. La sesión también puede cerrarse automáticamente por expiración (FA-04 de CU-01).

**Relaciones:** Ninguna.

---

## CU-17 – Guardar Snapshot

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | El actor está en una vista calculada del frontend principal con un resultado ya mostrado en pantalla (métrica, gráfico, rentabilidad o ficha de entidad). |
| **Postcondición** | Existe una snapshot asociada al actor y a la fecha actual con los datos calculados. |

![Diagrama de flujo](../imagenes/CdU/flujoCU17.png)

**Flujo principal:**
1. El actor pulsa "Guardar snapshot" desde la vista calculada actual.
2. El sistema determina el tipo de snapshot (métrica, gráfico o entidad) y toma los parámetros y datos ya mostrados en pantalla.
3. El sistema registra la fecha actual y el actor responsable.
4. Si ya existe una snapshot del mismo tipo con los mismos parámetros y fecha, el sistema la sobrescribe. Si no existe, la crea nueva.
5. El sistema muestra una notificación de confirmación indicando si la snapshot es nueva o se ha actualizado.

**Flujos alternativos:**
- `FA-01` (Upsert diario): si existe una snapshot previa del día con los mismos parámetros, se actualiza en lugar de crearse una nueva. Esta es la razón por la que no existe un caso de uso separado de "Actualizar snapshot".

**Restricciones:** Como máximo una snapshot por combinación de tipo, parámetros y día.

**Relaciones:** Invocado vía `<<extend>>` desde CU-03, CU-05, CU-07, CU-09, CU-10, CU-11 y CU-13.

---

## CU-18 – Listar Snapshots

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | CU-01 completado en el visor de snapshots. |
| **Postcondición** | El actor ha visto la tabla paginada de snapshots de la colección elegida. |

![Diagrama de flujo](../imagenes/CdU/flujoCU18.png)

**Flujo principal:**
1. El actor accede al visor de snapshots.
2. El sistema muestra la home con el resumen global por colección (total guardadas y guardadas hoy).
3. El actor selecciona una colección (métricas, gráficos o entidades).
4. El sistema muestra la tabla paginada con tipo, fecha, parámetros, última actualización y actor responsable.
5. El actor puede aplicar filtros por tipo y rango de fechas, y navegar por las páginas.

**Flujos alternativos:**
- `FA-01`: Sin resultados → estado vacío con mensaje informativo.

**Observación:** El listado no aplica filtrado por ámbito: las snapshots son lecturas inmutables de momentos pasados y no exponen información distinta a la ya visible en la capa operativa.

**Relaciones:** `<<extend>>` hacia CU-19.

---

## CU-19 – Consultar Detalle de Snapshot

| Campo | Valor |
|---|---|
| **Actores** | Director, Responsable |
| **Precondición** | Existe una snapshot accesible desde CU-18. |
| **Postcondición** | El actor ha visualizado la ficha reconstruida de la snapshot. |

![Diagrama de flujo](../imagenes/CdU/flujoCU19.png)

**Flujo principal:**
1. El actor selecciona una snapshot desde el listado (CU-18) o desde la home del visor.
2. El sistema recupera la snapshot.
3. El sistema reconstruye la visualización según el tipo:
   - Métrica → gauge, gráfico y indicadores propios de la métrica.
   - Gráfico → el mismo tipo de gráfico interactivo con que se guardó.
   - Entidad → ficha con avatar, campos y barra de progreso.
4. El actor puede expandir el contenido completo de la snapshot para inspeccionar los datos originales.

**Flujos alternativos:**
- `FA-01`: Snapshot no encontrada → mensaje de error con opción de volver al listado.

**Observación:** La visualización se reconstruye exactamente como se guardó el día de la captura. No se recalcula ningún dato contra el ERP.

**Relaciones:** `<<extend>>` hacia CU-20.

---

## CU-20 – Eliminar Snapshot

| Campo | Valor |
|---|---|
| **Actores** | Director |
| **Precondición** | Existe una snapshot seleccionada desde CU-18 o CU-19. |
| **Postcondición** | La snapshot ha sido eliminada de forma permanente. |

![Diagrama de flujo](../imagenes/CdU/flujoCU20.png)

**Flujo principal:**
1. El actor pulsa "Eliminar".
2. El sistema muestra un diálogo de confirmación advirtiendo que la operación es permanente.
3. Tras confirmar, el sistema elimina la snapshot.
4. El sistema recarga la tabla (si el origen es CU-18) o navega al listado (si el origen es CU-19).

**Flujos alternativos:**
- `FA-01`: El actor cancela el diálogo → no ocurre ningún cambio.
- `FA-02`: Error en la operación → mensaje de error sin modificar la vista.

**Observación:** El borrado es definitivo, sin papelera ni versiones previas. Tras el borrado, volver a guardar el mismo día con los mismos parámetros crea una snapshot nueva a través de CU-17.

**Relaciones:** Ninguna.
