# Capitulo 4

## Descripcion de funcionamiento del sistema

El MVP implementado funciona como una PWA conectada a un backend Express y a una base de datos MongoDB. Las vistas no trabajan de forma independiente: comparten el estado de mesas, comandas, lineas, tickets y usuarios. Por ello, una accion realizada en sala puede reflejarse despues en cocina, caja o auditoria.

| Vista principal | Funcionamiento implementado |
|---|---|
| `LoginView` | El usuario inicia sesion y la aplicacion carga el menu segun su rol. |
| `SalaView` | Se muestran las mesas por zona junto con su estado operativo: libre, ocupada, cocina trabajando, ticket en caja o pendiente de cierre. |
| `ComandaView` | El camarero abre la mesa y registra platos de carta o menu con cantidad, observaciones y alergenos. |
| `ComandaView` / `Backend` | Si la comanda es de menu, primeros, segundos y postres se gestionan por pases para no saturar cocina. |
| `KdsView` | Cocina cambia el estado de las lineas: `PENDIENTE`, `EN_PREPARACION`, `LISTO` y `SERVIDO`. |
| `ComandaView` | El camarero puede enviar el ticket a caja cuando la comanda esta preparada para cobro. |
| `CajaView` | El administrador cobra el ticket y, una vez cobrado, cierra la mesa para liberarla. |
| `SyncView` | Si hay un corte de red, las operaciones se guardan en IndexedDB y se sincronizan al recuperar conexion. |

De esta forma, el sistema cubre el flujo completo del servicio: mesa, comanda, cocina, caja y cierre, manteniendo trazabilidad y continuidad operativa.

## Mapa de navegacion

El mapa de navegacion muestra las vistas principales del sistema y el recorrido funcional que sigue cada rol dentro del MVP.

![Mapa de navegacion](/Estudiantes/daniel-puente/Capitulo-4/imagenes/MapaNav.svg)

## Vistas principales

### Login

La pantalla de login autentica al usuario y permite restaurar la sesion mediante token JWT almacenado en el navegador.

![Login](/Estudiantes/daniel-puente/Capitulo-4/imagenes/login.png)

### Sala

La vista de sala permite consultar mesas por zona y estado, abrir mesa, tomar comanda y ver una comanda activa. Se ha reducido el tamano de cada mesa para que la vista sea util en servicios con muchas mesas. Tambien se muestra estado operativo: mesa sentada, cocina trabajando, ticket en caja o mesa pendiente de cierre.

![Sala](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Sala.png)

### Comanda

La vista de comanda esta pensada para camareros en servicio. Permite seleccionar mesa, alternar entre carta y menu, filtrar platos, revisar alergenos, anadir lineas, editar o anular lineas pendientes y enviar ticket a caja. En menus, los primeros, segundos y postres funcionan como pases: los segundos o postres no aparecen en cocina hasta que el camarero los solicita.

![Comanda](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Comanda.png)

### KDS

La pantalla KDS muestra a cocina un tablero por estados: pendiente, en preparacion y listo. El filtrado por pase evita que cocina vea al mismo tiempo todos los platos de 30 o 50 mesas. Asi se prioriza lo que se debe cocinar en ese momento.

![KDS](/Estudiantes/daniel-puente/Capitulo-4/imagenes/KDS.png)

### Caja

La vista de caja lista tickets por fecha, permite cobrar y deja visible la accion de cerrar mesa una vez cobrado el ticket. Esta separacion evita liberar mesas accidentalmente antes de finalizar el cobro.

![Caja](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Caja.png)

### Carta

La gestion de carta permite mantener platos, alergenos, disponibilidad y tarifas de menu. Esta informacion alimenta la toma de comandas y el calculo de tickets.

![Carta](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Carta.png)

### Usuarios

La vista de usuarios permite al administrador gestionar personal y roles. Esto soporta la separacion de permisos entre administrador, camarero y cocina.

![Usuarios](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Usuarios.png)

### Reservas

La vista de reservas lista las reservas del dia, permite crear, editar, cancelar y asignar mesa. La validacion de horario bloquea reservas fuera de comidas y cenas.

![Reservas](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Reservas.png)

### Auditoria

La auditoria registra acciones relevantes sobre entidades del sistema. Sirve como trazabilidad para incidencias operativas, cambios de carta, caja, reservas y usuarios.

![Auditoria](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Auditoria.png)

### Offline

La vista offline muestra operaciones pendientes de sincronizacion. El objetivo es que el restaurante pueda seguir trabajando ante cortes temporales de red.

![Offline](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Offline.png)

##  Estructura de directorios

La solucion separa frontend y backend para mantener responsabilidades claras.

```text
Restaurante_La_Union/
  backend/
    src/
      config/          Configuracion de entorno, MongoDB y Socket.IO
      controllers/     Entrada HTTP de cada recurso
      middleware/      Autenticacion, roles, validacion y errores
      models/          Modelos Mongoose
      routes/          Rutas REST agrupadas por recurso
      scripts/         Seed, reset de base de datos y pruebas de endpoints
      services/        Logica de negocio
      sockets/         Conexion y eventos en tiempo real
      validators/      Esquemas de validacion
  frontend/
    src/
      api/             Cliente HTTP centralizado
      components/      Layout, login y vistas
      components/views Vistas funcionales del sistema
      offline/         IndexedDB y cola local
      socket/          Cliente Socket.IO
      types/           Tipos de dominio TypeScript
```

## Namespaces y capas logicas

### Frontend

| Namespace / carpeta | Responsabilidad |
|---|---|
| `src/api` | Centraliza llamadas REST y token JWT. |
| `src/components` | Componentes de interfaz y layout comun. |
| `src/components/views` | Vistas de negocio: sala, comanda, KDS, caja, reservas, carta, usuarios, auditoria y offline. |
| `src/offline` | Persistencia local con IndexedDB para operaciones pendientes. |
| `src/socket` | Conexion en tiempo real con el backend. |
| `src/types` | Tipos compartidos por las vistas. |

### Backend

| Namespace / carpeta | Responsabilidad |
|---|---|
| `controllers` | Reciben peticiones y delegan en servicios. |
| `services` | Contienen reglas de negocio y validaciones funcionales. |
| `models` | Definen entidades persistentes en MongoDB. |
| `routes` | Declaran endpoints REST. |
| `middleware` | Autenticacion, autorizacion por rol y tratamiento de errores. |
| `sockets` | Eventos en tiempo real para sala, KDS, caja y reservas. |
| `scripts` | Automatizacion de datos iniciales y pruebas de flujo. |

### Namespaces REST principales

| Namespace API | Uso |
|---|---|
| `/api/auth` | Login, perfil y logout. |
| `/api/mesas` | Mesas, zonas, apertura, cierre y comanda activa. |
| `/api/comandas` | Creacion de comandas, lineas, edicion, cancelacion y pases. |
| `/api/kds` | Consulta y cambio de estado de lineas en cocina. |
| `/api/tickets` | Generacion, listado y cobro de tickets. |
| `/api/reservas` | Gestion de reservas y asignacion de mesa. |
| `/api/carta` | Platos y tarifas de menu. |
| `/api/usuarios` | Usuarios y roles. |
| `/api/auditoria` | Trazabilidad de acciones. |
| `/api/sync` | Registro y ejecucion de operaciones offline. |

## Casos de uso seleccionados

### Trazabilidad con casos de uso

En el cuerpo del capitulo se recoge la trazabilidad de los casos de uso principales seleccionados para el MVP. La matriz completa de trazabilidad se incluye en el Anexo A.

| ID | Caso de uso seleccionado | Vista principal | Resultado funcional |
|---|---|---|---|
| CU-04 | Abrir mesa | Sala | Mesa ocupada y comanda activa. |
| CU-06 | Tomar comanda | Comanda | Lineas de carta o menu registradas. |
| CU-08 | Editar comanda | Comanda | Lineas pendientes modificadas o canceladas. |
| CU-12 | Marcar plato como listo | KDS | Plato actualizado y visible para sala. |
| CU-14 | Enviar ticket a caja | Comanda / Caja | Ticket pendiente de cobro. |
| CU-17 | Cobrar ticket en caja | Caja | Ticket cobrado. |
| CU-05 | Cerrar mesa | Caja / Sala | Mesa liberada para un nuevo servicio. |

### Casos de uso implementados
 

#### CU-04. Abrir mesa 

##### Descripcion funcional

Este caso de uso inicia el servicio de una mesa. Cuando el camarero o el administrador selecciona una mesa libre, el sistema crea una comanda en estado `ABIERTA`, asocia esa comanda a la mesa y cambia el estado de la mesa a `OCUPADA`.

El endpoint principal es `POST /api/comandas`. Aunque funcionalmente el usuario percibe que "abre una mesa", tecnicamente se crea una comanda activa vinculada a esa mesa.

##### Captura de pantalla

![Sala - apertura de mesa](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Sala.png)

##### Parametros de entrada

| Parametro | Tipo | Descripcion |
|---|---|---|
| `mesa_id` | ObjectId | Identificador de la mesa principal que se desea abrir. |
| `mesas_ids` | ObjectId[] opcional | Lista de mesas adicionales si se abre una comanda sobre mesas unidas. |

##### Que puede hacer el usuario

- Visualizar mesas agrupadas por zona desde `SalaView`.
- Seleccionar una mesa libre o reservada.
- Abrir la mesa y comenzar una comanda.
- Acceder posteriormente a la vista `ComandaView` para anadir platos.

##### Flujo tecnico

| Paso | Capa | Clase / funcion | Accion |
|---|---|---|---|
| 1 | Frontend | `App.tsx -> abrirMesa()` | Ejecuta la accion desde la vista de sala. |
| 2 | API client | `api.abrirMesa(...)` | Envia `POST /api/comandas` con `mesa_id`. |
| 3 | Routes | `comanda.routes.js` | Valida JWT, rol `ADMIN` o `CAMARERO`, y cuerpo de la peticion. |
| 4 | Controller | `comandaController.abrir()` | Extrae usuario autenticado y delega en el servicio. |
| 5 | Service | `comandaService.abrirComanda()` | Comprueba disponibilidad de mesa, crea comanda y ocupa la mesa. |
| 6 | Models | `Comanda`, `Mesa`, `LogAuditoria` | Persiste la comanda, actualiza la mesa y registra auditoria. |
| 7 | Sockets | `emitirEvento('comanda:abierta')` | Notifica la actualizacion al resto de vistas. |

##### Datos de salida

| Campo | Descripcion |
|---|---|
| `comanda` | Objeto de comanda creado con estado `ABIERTA`, mesa asociada y camarero responsable. |

##### Restricciones

- La mesa no puede estar `OCUPADA` ni `FUERA_SERVICIO`.
- Una mesa no puede tener dos comandas activas.
- La operacion requiere usuario autenticado con rol `ADMIN` o `CAMARERO`.

##### Implementacion

Archivos implicados: `frontend/src/App.tsx`, `frontend/src/api/client.ts`, `backend/src/routes/comanda.routes.js`, `backend/src/controllers/comandaController.js`, `backend/src/services/comandaService.js`, `backend/src/models/Comanda.js` y `backend/src/models/Mesa.js`.

#### CU-06. Tomar comanda

##### Descripcion funcional

Permite registrar los platos solicitados por los clientes de una mesa. La comanda se construye linea a linea, indicando plato, cantidad, observaciones, alergenos y si pertenece a carta o menu del dia.

El endpoint principal es `POST /api/comandas/:id/lineas`. La vista implicada es `ComandaView`.

##### Captura de pantalla

![Comanda - toma de pedido](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Comanda.png)

##### Parametros de entrada

| Parametro | Tipo | Descripcion |
|---|---|---|
| `plato_id` | ObjectId | Plato seleccionado de la carta. |
| `cantidad` | number | Numero de unidades solicitadas. |
| `observaciones` | string | Indicaciones para cocina. |
| `alergenos` | string[] | Alergenos declarados y revisados por el camarero. |
| `esMenu` | boolean | Indica si la linea pertenece al menu del dia. |
| `tarifa_menu_id` | ObjectId opcional | Tarifa aplicada cuando la linea pertenece al menu. |

##### Que puede hacer el usuario

- Seleccionar mesa activa.
- Filtrar platos por categoria.
- Alternar entre carta y menu.
- Indicar cantidad y observaciones.
- Confirmar alergenos cuando el plato los contiene.
- Anadir lineas a la comanda activa.
- Solicitar segundos o postres cuando corresponda.

##### Flujo tecnico

| Paso | Capa | Clase / funcion | Accion |
|---|---|---|---|
| 1 | Frontend | `ComandaView` | El usuario selecciona plato, cantidad y observaciones. |
| 2 | Frontend | `App.tsx -> addLinea()` | Valida datos basicos y alergenos revisados. |
| 3 | API client | `api.crearLinea(...)` | Envia `POST /api/comandas/:id/lineas`. |
| 4 | Routes | `comanda.routes.js` | Valida JWT, rol y esquema `lineaComandaSchema`. |
| 5 | Controller | `comandaController.agregarLinea()` | Inyecta `comanda_id` y `usuario_id`. |
| 6 | Service | `comandaService.agregarLineaComanda()` | Valida comanda abierta, plato disponible, tarifa y alergenos. |
| 7 | Models | `LineaComanda` | Crea la linea con estado `PENDIENTE`. |
| 8 | Sockets | `lineaComanda:creada`, `kds:actualizado` | Actualiza KDS y comanda en tiempo real. |

##### Datos de salida

| Campo | Descripcion |
|---|---|
| `linea` | Linea de comanda creada, con plato, cantidad, alergenos, observaciones, estado y datos de menu si aplica. |

##### Restricciones

- Solo se pueden anadir lineas a comandas `ABIERTA`.
- El plato debe estar disponible.
- Si es menu, debe existir una tarifa vigente.
- Los alergenos del plato deben estar confirmados.
- Segundos y postres de menu quedan retenidos hasta que el camarero solicite el pase.

##### Implementacion

Archivos implicados: `frontend/src/components/views/ComandaView.tsx`, `frontend/src/App.tsx`, `frontend/src/api/client.ts`, `backend/src/routes/comanda.routes.js`, `backend/src/controllers/comandaController.js`, `backend/src/services/comandaService.js`, `backend/src/models/LineaComanda.js`, `backend/src/models/Plato.js` y `backend/src/models/TarifaMenu.js`.

#### CU-08. Editar comanda

##### Descripcion funcional

Permite corregir una linea de comanda mientras cocina todavia no ha empezado a prepararla. El sistema permite modificar cantidad u observaciones, y tambien cancelar una linea pendiente. Esta restriccion evita cambios tardios sobre platos que ya estan en preparacion.

Los endpoints principales son `PUT /api/comandas/lineas/:lineaId` y `PATCH /api/comandas/lineas/:lineaId/cancelar`.

##### Captura de pantalla

![Comanda - edicion](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Comanda.png)

##### Parametros de entrada

| Parametro | Tipo | Descripcion |
|---|---|---|
| `lineaId` | ObjectId | Identificador de la linea que se quiere modificar. |
| `cantidad` | number opcional | Nueva cantidad. |
| `observaciones` | string opcional | Nuevas observaciones para cocina. |
| `alergenos` | string[] opcional | Alergenos confirmados de la linea. |

##### Que puede hacer el usuario

- Revisar lineas de la comanda activa.
- Corregir lineas pendientes.
- Anular lineas pendientes.
- Mantener visibles las lineas canceladas para trazabilidad.

##### Flujo tecnico

| Paso | Capa | Clase / funcion | Accion |
|---|---|---|---|
| 1 | Frontend | `ComandaView` | Muestra acciones de editar o cancelar por linea. |
| 2 | Frontend | `App.tsx -> guardarEdicionLinea()` | Recoge nueva cantidad y observaciones. |
| 3 | API client | `api.actualizarLinea(...)` | Envia `PUT /api/comandas/lineas/:lineaId`. |
| 4 | Routes | `comanda.routes.js` | Valida JWT, rol y `lineaId`. |
| 5 | Controller | `comandaController.actualizarLinea()` | Delega en el servicio. |
| 6 | Service | `comandaService.actualizarLineaComanda()` | Comprueba que la comanda este abierta y la linea siga `PENDIENTE`. |
| 7 | Models | `LineaComanda`, `LogAuditoria` | Actualiza la linea y registra auditoria. |
| 8 | Sockets | `lineaComanda:actualizada`, `kds:actualizado` | Refresca KDS y comanda. |

##### Datos de salida

| Campo | Descripcion |
|---|---|
| `linea` | Linea actualizada o cancelada. |

##### Restricciones

- Solo se pueden editar lineas en estado `PENDIENTE`.
- No se pueden editar lineas `EN_PREPARACION`, `LISTO` o `SERVIDO`.
- La cancelacion no elimina fisicamente la linea: cambia su estado a `CANCELADO`.

##### Implementacion

Archivos implicados: `frontend/src/components/views/ComandaView.tsx`, `frontend/src/App.tsx`, `backend/src/routes/comanda.routes.js`, `backend/src/controllers/comandaController.js`, `backend/src/services/comandaService.js` y `backend/src/models/LineaComanda.js`.

#### CU-12. Marcar plato como listo

##### Descripcion funcional

Permite a cocina cambiar el estado de una linea de comanda hasta `LISTO`. Este cambio informa a sala de que el plato puede servirse y actualiza el KDS en tiempo real.

El endpoint principal es `PATCH /api/kds/lineas/:lineaId/estado`. Tambien existen accesos especificos como `PATCH /api/kds/lineas/:lineaId/lista`.

##### Captura de pantalla

![KDS - plato listo](/Estudiantes/daniel-puente/Capitulo-4/imagenes/KDS.png)

##### Parametros de entrada

| Parametro | Tipo | Descripcion |
|---|---|---|
| `lineaId` | ObjectId | Linea de comanda que cocina quiere actualizar. |
| `estado` | string | Nuevo estado: `EN_PREPARACION`, `LISTO`, `SERVIDO` o `CANCELADO`. |

##### Que puede hacer el usuario

- Ver lineas pendientes y en preparacion.
- Marcar platos como `EN_PREPARACION`.
- Marcar platos como `LISTO`.
- Dejar el plato disponible para que sala lo sirva.

##### Flujo tecnico

| Paso | Capa | Clase / funcion | Accion |
|---|---|---|---|
| 1 | Frontend | `KdsView` | Cocina pulsa el cambio de estado. |
| 2 | Frontend | `App.tsx -> updateKds()` | Llama al cliente HTTP o encola offline si procede. |
| 3 | API client | `api.kdsEstado(...)` | Envia `PATCH /api/kds/lineas/:lineaId/estado`. |
| 4 | Routes | `kds.routes.js` | Valida JWT, rol y estado permitido. |
| 5 | Controller | `kdsController.cambiarEstado()` | Delega en `kdsService`. |
| 6 | Service | `kdsService.cambiarEstadoLineaKds()` | Valida transicion de estado. |
| 7 | Models | `LineaComanda`, `Comanda`, `LogAuditoria` | Actualiza estado y registra auditoria. |
| 8 | Sockets | `kds:actualizado`, notificaciones de platos listos | Actualiza cocina y sala. |

##### Datos de salida

| Campo | Descripcion |
|---|---|
| `linea` | Linea actualizada con el nuevo estado. |

##### Restricciones

- No se permiten transiciones incoherentes: por ejemplo, de `PENDIENTE` a `SERVIDO`.
- Las lineas canceladas no avanzan en el KDS.
- El KDS solo muestra lineas cuyo pase haya sido solicitado.

##### Implementacion

Archivos implicados: `frontend/src/components/views/KdsView.tsx`, `frontend/src/App.tsx`, `backend/src/routes/kds.routes.js`, `backend/src/controllers/kdsController.js`, `backend/src/services/kdsService.js`, `backend/src/services/socketService.js` y `backend/src/models/LineaComanda.js`.

#### CU-14. Enviar ticket a caja

##### Descripcion funcional

Genera un ticket a partir de la comanda activa de una mesa y lo deja disponible en caja para su cobro. El ticket contiene el desglose de platos, tarifas de menu y total economico.

El endpoint principal es `POST /api/tickets`.

##### Captura de pantalla

![Comanda - envio a caja](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Comanda.png)

##### Parametros de entrada

| Parametro | Tipo | Descripcion |
|---|---|---|
| `comanda_id` | ObjectId | Identificador de la comanda que se quiere transformar en ticket. |

##### Que puede hacer el usuario

- Revisar la comanda antes del cobro.
- Generar el ticket de caja.
- Evitar calculos manuales.
- Dejar el ticket pendiente para administracion.

##### Flujo tecnico

| Paso | Capa | Clase / funcion | Accion |
|---|---|---|---|
| 1 | Frontend | `ComandaView` | El usuario pulsa `Enviar ticket a caja`. |
| 2 | Frontend | `App.tsx -> enviarTicket()` | Llama al cliente HTTP o registra cola offline. |
| 3 | API client | `api.generarTicket(...)` | Envia `POST /api/tickets`. |
| 4 | Routes | `ticket.routes.js` | Valida JWT, rol y body `ticketSchema`. |
| 5 | Controller | `ticketController.generar()` | Delega en el servicio. |
| 6 | Service | `ticketService.generarTicket()` | Comprueba comanda, evita duplicados y calcula total. |
| 7 | Models | `Ticket`, `LineaComanda`, `Comanda`, `LogAuditoria` | Crea ticket y registra auditoria. |
| 8 | Sockets | `ticket:generado` | Actualiza caja. |

##### Datos de salida

| Campo | Descripcion |
|---|---|
| `ticket` | Ticket generado con estado `PENDIENTE`, total y snapshot de la comanda. |

##### Restricciones

- No se genera ticket de una comanda cancelada.
- No se duplica un ticket si ya existe.
- No se genera ticket si quedan lineas pendientes, en preparacion o listas sin servir.
- Las lineas canceladas no suman al total.
- Los menus se cobran por tarifa, no por cada plato individual.

##### Implementacion

Archivos implicados: `frontend/src/components/views/ComandaView.tsx`, `frontend/src/App.tsx`, `backend/src/routes/ticket.routes.js`, `backend/src/controllers/ticketController.js`, `backend/src/services/ticketService.js`, `backend/src/models/Ticket.js` y `backend/src/models/LineaComanda.js`.

#### CU-17. Cobrar ticket en caja

##### Descripcion funcional

Permite al administrador confirmar el cobro de un ticket pendiente. El ticket pasa a estado `COBRADO`, queda asociado al usuario que realiza la accion y registra la fecha de cobro.

El endpoint principal es `PATCH /api/tickets/:id/cobrar`.

##### Captura de pantalla

![Caja - cobro](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Caja.png)

##### Parametros de entrada

| Parametro | Tipo | Descripcion |
|---|---|---|
| `id` | ObjectId | Identificador del ticket que se desea cobrar. |

##### Que puede hacer el usuario

- Ver tickets pendientes en `CajaView`.
- Revisar total y desglose.
- Confirmar el cobro.
- Preparar la mesa para su cierre.

##### Flujo tecnico

| Paso | Capa | Clase / funcion | Accion |
|---|---|---|---|
| 1 | Frontend | `CajaView` | El administrador pulsa `Cobrar`. |
| 2 | Frontend | `App.tsx -> cobrar()` | Solicita el cobro al backend. |
| 3 | API client | `api.cobrarTicket(...)` | Envia `PATCH /api/tickets/:id/cobrar`. |
| 4 | Routes | `ticket.routes.js` | Valida JWT y restringe la accion al rol `ADMIN`. |
| 5 | Controller | `ticketController.cobrar()` | Delega en `ticketService`. |
| 6 | Service | `ticketService.cobrarTicket()` | Comprueba que el ticket este `PENDIENTE`. |
| 7 | Models | `Ticket`, `LogAuditoria` | Cambia estado a `COBRADO`, guarda usuario y fecha. |
| 8 | Sockets | `ticket:cobrado`, `comanda:actualizada` | Refresca caja, sala y comanda. |

##### Datos de salida

| Campo | Descripcion |
|---|---|
| `ticket` | Ticket actualizado en estado `COBRADO`, con `cobradoEn` y `cobradoPor`. |

##### Restricciones

- Solo el rol `ADMIN` puede cobrar tickets.
- No se puede cobrar dos veces el mismo ticket.
- Cobrar el ticket no libera automaticamente la mesa.

##### Implementacion

Archivos implicados: `frontend/src/components/views/CajaView.tsx`, `frontend/src/App.tsx`, `backend/src/routes/ticket.routes.js`, `backend/src/controllers/ticketController.js`, `backend/src/services/ticketService.js` y `backend/src/models/Ticket.js`.

#### CU-05. Cerrar mesa

##### Descripcion funcional

Cierra el servicio de una mesa una vez que el ticket asociado ya ha sido cobrado. La mesa vuelve a estado `LIBRE`, la comanda queda cerrada y la mesa puede utilizarse en un nuevo servicio.

El endpoint principal es `PATCH /api/mesas/:id/cerrar`.

##### Captura de pantalla

![Caja - cierre de mesa](/Estudiantes/daniel-puente/Capitulo-4/imagenes/Caja.png)

##### Parametros de entrada

| Parametro | Tipo | Descripcion |
|---|---|---|
| `id` | ObjectId | Identificador de la mesa que se desea cerrar. |

##### Que puede hacer el usuario

- Ver en caja que el ticket ya ha sido cobrado.
- Cerrar la mesa desde `CajaView`.
- Liberar la mesa para nuevos clientes.
- Actualizar el estado en `SalaView`.

##### Flujo tecnico

| Paso | Capa | Clase / funcion | Accion |
|---|---|---|---|
| 1 | Frontend | `CajaView` | El administrador pulsa `Cerrar mesa`. |
| 2 | Frontend | `App.tsx -> cerrarMesaCaja()` | Solicita cierre al backend. |
| 3 | API client | `api.cerrarMesa(...)` | Envia `PATCH /api/mesas/:id/cerrar`. |
| 4 | Routes | `mesa.routes.js` | Valida JWT y restringe la accion al rol `ADMIN`. |
| 5 | Controller | `mesaController.cerrar()` | Delega en `mesaService`. |
| 6 | Service | `mesaService.cerrarMesa()` | Comprueba comanda activa y ticket cobrado. |
| 7 | Models | `Mesa`, `Comanda`, `Ticket`, `LogAuditoria` | Cierra comanda, libera mesa y registra auditoria. |
| 8 | Sockets | `mesa:actualizada` | Refresca la vista de sala. |

##### Datos de salida

| Campo | Descripcion |
|---|---|
| `mesa` | Mesa actualizada tras el cierre del servicio. |

##### Restricciones

- Solo el rol `ADMIN` puede cerrar mesas.
- No se puede cerrar una mesa sin comanda activa, salvo que ya estuviera libre.
- No se puede cerrar una mesa sin ticket cobrado.
- El cierre libera todas las mesas asociadas a la misma comanda en caso de mesas unidas.

##### Implementacion

Archivos implicados: `frontend/src/components/views/CajaView.tsx`, `frontend/src/App.tsx`, `backend/src/routes/mesa.routes.js`, `backend/src/controllers/mesaController.js`, `backend/src/services/mesaService.js`, `backend/src/models/Mesa.js`, `backend/src/models/Comanda.js` y `backend/src/models/Ticket.js`.

## Logica de negocio mas relevante

### Ciclo de vida de mesa

La mesa no se considera solo un registro con estado libre u ocupada. El backend enriquece la informacion con un estado operativo derivado de comanda, lineas y ticket:

- Libre o reservada.
- Sentados, cuando hay comanda abierta sin platos.
- Cocina trabajando, cuando hay lineas pendientes o en preparacion.
- Segundos pendientes o postres pendientes, cuando un menu tiene pases retenidos.
- Listo para servir o todo servido.
- Ticket en caja.
- Cobrada, cerrar mesa.

Esto permite que sala actue de un vistazo sin entrar en cada mesa.

### Control de pases de menu

Uno de los puntos mas importantes para un restaurante de alto transito es que cocina no reciba todos los platos de golpe. La logica implementada distingue entre carta y menu:

- Los primeros platos de menu se solicitan inicialmente.
- Los segundos y postres quedan retenidos con `solicitadoKds = false`.
- El camarero solicita segundos o postres desde la comanda cuando el cliente lo pide.
- El KDS solo muestra lineas solicitadas.

Con esta regla, cocina trabaja por pase y por mesa, evitando acumulacion visual y errores de prioridad.

### Calculo correcto de menus

El ticket agrupa las lineas de menu por tarifa y cantidad, evitando cobrar un primer y un segundo como dos menus separados. En lugar de sumar cada plato de menu, se cobra la tarifa del menu una vez por comensal/tarifa. La carta si se suma por precio de plato y cantidad.

### KDS y estados de cocina

El KDS gestiona el flujo operativo:

1. `PENDIENTE`.
2. `EN_PREPARACION`.
3. `LISTO`.
4. `SERVIDO`.

La separacion por estado permite a cocina distinguir lo que entra, lo que ya se esta cocinando y lo que debe retirar sala. Esta trazabilidad reduce llamadas verbales entre camarero y cocina.

### Alergenos y observaciones

Los platos contienen alergenos declarados. Al anadir una linea, el camarero debe confirmar los alergenos si existen. La informacion viaja hasta la comanda y el KDS, por lo que cocina puede ver observaciones y alertas relevantes.

### Caja y cierre de mesa

El sistema separa cobrar ticket de cerrar mesa:

- Cobrar ticket cambia el estado economico del ticket.
- Cerrar mesa libera la mesa solo cuando el ticket esta cobrado.
- Esta operacion queda reservada para administracion.

La separacion evita que una mesa quede libre por error antes de haber finalizado el cobro.

### Reservas y horarios validos

Las reservas estan limitadas a horarios reales de servicio:

- Comidas: 13:00 a 16:00.
- Cenas: 20:00 a 23:00.

Ademas, se controla el solapamiento de reservas por mesa y se permite doblar mesa si hay margen temporal suficiente.

### Offline y sincronizacion

El frontend guarda operaciones pendientes en IndexedDB cuando hay problemas de conexion. Al recuperar conectividad, el sistema sincroniza con el backend. Las operaciones contempladas incluyen abrir comanda, crear/editar/cancelar lineas, cambiar estados de KDS, generar/cobrar ticket y crear/editar reservas.

### Auditoria

Las acciones sensibles se registran como logs de auditoria: cambios de usuarios, reservas, carta, caja, comandas y mesas. Esto permite reconstruir incidencias del servicio.

### Extractos de codigo de logica de negocio

En esta seccion se recogen los fragmentos de codigo mas representativos del MVP. No se incluye todo el codigo fuente, sino las reglas que explican por que el sistema se comporta de una forma concreta en los casos de uso seleccionados.

#### Apertura de comanda y ocupacion de mesa

Archivo: `backend/src/services/comandaService.js`

Este bloque implementa el inicio real del servicio. Comprueba que la mesa exista, que todas las mesas implicadas esten libres o reservadas, crea la comanda y actualiza el estado de la mesa a `OCUPADA`. Tambien emite eventos en tiempo real y registra auditoria.

```js
export const abrirComanda = async ({ mesa_id, mesas_ids = [], camarero_id }) => {
  const mesa = await Mesa.findById(mesa_id);

  if (!mesa) {
    throw new Error('Mesa no encontrada');
  }

  const idsMesasUnidas = Array.from(new Set([mesa_id, ...mesas_ids].filter(Boolean).map(String)));
  const mesas = await Mesa.find({ _id: { $in: idsMesasUnidas } });

  const mesaNoDisponible = mesas.find((item) => !['LIBRE', 'RESERVADA'].includes(item.estado) || item.comanda_activa_id);

  if (mesaNoDisponible) {
    throw new Error(`La mesa ${mesaNoDisponible.numero} no esta libre para abrir comanda`);
  }

  const comanda = await Comanda.create({
    mesa_id,
    camarero_id,
    estado: 'ABIERTA',
  });

  await Mesa.updateMany(
    { _id: { $in: idsMesasUnidas } },
    { estado: 'OCUPADA', comanda_activa_id: comanda._id },
  );

  await registrarAuditoria({
    accion: 'COMANDA_ABIERTA',
    usuario_id: camarero_id,
    entidadAfectada: 'Comanda',
    entidadId: comanda._id,
    detalles: `Comanda abierta en mesa ${mesa.numero}`,
  });

  emitirEvento('comanda:abierta', { comanda });
  return comanda;
};
```

Esta regla cubre directamente el caso de uso **CU-04 Abrir mesa**, evitando duplicidades de comanda activa y dejando la mesa preparada para tomar pedido.

#### Validacion de alergenos y retencion de pases de menu

Archivo: `backend/src/services/comandaService.js`

La logica de alta de linea aplica varias reglas a la vez: la comanda debe estar abierta, el plato debe estar disponible, la tarifa de menu debe ser vigente y los alergenos del plato deben quedar confirmados. Ademas, cuando el plato pertenece al menu, segundos y postres se retienen para que no aparezcan en cocina antes de tiempo.

```js
const CATEGORIAS_MENU_RETENIDAS = ['SEGUNDO', 'POSTRE'];

const lineaMenuVisibleInicialmente = (plato, esMenu) => (
  !esMenu || !CATEGORIAS_MENU_RETENIDAS.includes(normalizarCategoria(plato.categoria))
);

const validarAlergenosConfirmados = (plato, alergenos = null) => {
  if (!plato.alergenos.length) {
    return plato.alergenos;
  }

  if (!Array.isArray(alergenos)) {
    throw new Error('Alergenos incompletos para el plato seleccionado');
  }

  const alergenosNormalizados = alergenos.map((alergeno) => alergeno.trim().toLowerCase());
  const faltantes = plato.alergenos.filter(
    (alergeno) => !alergenosNormalizados.includes(alergeno.trim().toLowerCase()),
  );

  if (faltantes.length > 0) {
    throw new Error('Alergenos incompletos para el plato seleccionado');
  }

  return plato.alergenos;
};
```

```js
const linea = await LineaComanda.create({
  comanda_id,
  plato_id,
  tarifa_menu_id: esMenu ? tarifa_menu_id : null,
  cantidad,
  observaciones,
  alergenos: alergenosConfirmados,
  esMenu,
  solicitadoKds: lineaMenuVisibleInicialmente(plato, esMenu),
  estado: 'PENDIENTE',
});
```

Este fragmento justifica los casos **CU-06 Tomar comanda**, **CU-N03 Rechazar comanda por alergenos incompletos** y **CU-09 Solicitar segundos platos / postres**.

#### Edicion segura de comanda

Archivo: `backend/src/services/comandaService.js`

La edicion de comanda esta limitada a lineas pendientes. Si cocina ya ha empezado a trabajar una linea, el backend bloquea la modificacion aunque el frontend intentase enviarla.

```js
export const actualizarLineaComanda = async ({ lineaId, cantidad, observaciones, alergenos = null, usuario_id }) => {
  const linea = await LineaComanda.findById(lineaId);

  if (!linea) {
    throw new Error('Linea de comanda no encontrada');
  }

  const comanda = await Comanda.findById(linea.comanda_id);

  if (!comanda || comanda.estado !== 'ABIERTA') {
    throw new Error('Solo se pueden editar lineas de una comanda ABIERTA');
  }

  if (linea.estado !== 'PENDIENTE') {
    throw new Error('No se puede editar una linea que ya esta en preparacion');
  }

  const plato = await Plato.findById(linea.plato_id);

  linea.cantidad = cantidad ?? linea.cantidad;
  linea.observaciones = observaciones ?? linea.observaciones;
  linea.alergenos = validarAlergenosConfirmados(plato, alergenos ?? linea.alergenos);
  await linea.save();

  await registrarAuditoria({
    accion: 'LINEA_COMANDA_ACTUALIZADA',
    usuario_id,
    entidadAfectada: 'LineaComanda',
    entidadId: linea._id,
    detalles: `Linea actualizada en comanda ${comanda._id}`,
  });

  emitirEvento('kds:actualizado', { comanda_id: comanda._id });
  return linea;
};
```

Este codigo es importante porque hace cumplir el caso **CU-08 Editar comanda** y el caso negativo **CU-N05 Bloquear edicion de linea en preparacion**.

#### Solicitud de segundos y postres

Archivo: `backend/src/services/comandaService.js`

El sistema no permite solicitar segundos si los primeros no estan listos. Tampoco permite solicitar postres si los segundos aun no han terminado. Esta regla replica el funcionamiento real del menu del dia.

```js
const validarPaseAnteriorListo = async ({ comandaId, categoria }) => {
  const requisitos = {
    SEGUNDO: 'PRIMERO',
    POSTRE: 'SEGUNDO',
  };
  const categoriaRequerida = requisitos[categoria];

  if (!categoriaRequerida) return;

  const lineasPrevias = await LineaComanda.find({
    comanda_id: comandaId,
    esMenu: true,
    estado: { $ne: 'CANCELADO' },
  }).populate('plato_id');

  const lineasDelPaseAnterior = lineasPrevias.filter(
    (linea) => normalizarCategoria(linea.plato_id?.categoria) === categoriaRequerida,
  );

  const paseAnteriorListo = lineasDelPaseAnterior.every((linea) => (
    ['LISTO', 'SERVIDO'].includes(linea.estado)
  ));

  if (!paseAnteriorListo) {
    throw new Error(`No se puede solicitar ${categoria} hasta que ${categoriaRequerida} este listo`);
  }
};
```

```js
await LineaComanda.updateMany(
  { _id: { $in: lineasPase.map((linea) => linea._id) } },
  { solicitadoKds: true },
);

emitirEvento('kds:actualizado', { comanda_id: comanda._id });
emitirEvento('comanda:actualizada', { comanda });
```

Con esto se evita que cocina reciba todos los platos de golpe y se mantiene el orden de servicio por pases.

#### Transiciones de estado en KDS

Archivo: `backend/src/services/kdsService.js`

La cocina no puede mover una linea a cualquier estado. El servicio define una maquina de estados simple: pendiente, en preparacion, listo y servido.

```js
const TRANSICIONES_VALIDAS = {
  PENDIENTE: ['EN_PREPARACION', 'CANCELADO'],
  EN_PREPARACION: ['LISTO'],
  LISTO: ['SERVIDO'],
  SERVIDO: [],
  CANCELADO: [],
};

export const cambiarEstadoLineaKds = async ({ lineaId, estado, usuario_id }) => {
  const linea = await LineaComanda.findById(lineaId);
  const transiciones = TRANSICIONES_VALIDAS[linea.estado] || [];

  if (!transiciones.includes(estado)) {
    throw new Error(`No se puede cambiar una linea de ${linea.estado} a ${estado}`);
  }

  linea.estado = estado;
  await linea.save();

  await registrarAuditoria({
    accion: 'ESTADO_LINEA_KDS_ACTUALIZADO',
    usuario_id,
    entidadAfectada: 'LineaComanda',
    entidadId: linea._id,
    detalles: `Estado actualizado a ${estado}`,
  });

  if (estado === 'LISTO') {
    await notificarPlatosListos(comanda);
  }

  return linea;
};
```

Este fragmento explica **CU-12 Marcar plato como listo** y la notificacion posterior a sala.

#### Calculo del ticket sin duplicar menus

Archivo: `backend/src/services/ticketService.js`

El calculo del ticket separa carta y menu. Los platos de carta suman por precio y cantidad; los menus se agrupan por tarifa para no cobrar un primer y un segundo como dos menus distintos.

```js
const calcularTotalComanda = async (comandaId) => {
  const lineas = await LineaComanda.find({
    comanda_id: comandaId,
    estado: { $ne: 'CANCELADO' },
  })
    .populate('plato_id')
    .populate('tarifa_menu_id');

  const totalCarta = lineas
    .filter((linea) => !linea.esMenu)
    .reduce((total, linea) => total + (linea.plato_id?.precio || 0) * linea.cantidad, 0);

  const menus = lineas
    .filter((linea) => linea.esMenu && linea.tarifa_menu_id)
    .reduce((acc, linea) => {
      const tarifaId = String(linea.tarifa_menu_id._id || linea.tarifa_menu_id);
      const actual = acc.get(tarifaId) || { precio: linea.tarifa_menu_id?.precio || 0, cantidad: 0 };
      acc.set(tarifaId, {
        precio: actual.precio,
        cantidad: Math.max(actual.cantidad, linea.cantidad),
      });
      return acc;
    }, new Map());

  const totalMenus = Array.from(menus.values())
    .reduce((total, menu) => total + menu.precio * menu.cantidad, 0);

  return totalCarta + totalMenus;
};
```

Esta regla soporta **CU-14 Enviar ticket a caja** y evita errores economicos en el menu del dia.

#### Cobro y cierre de mesa

Archivos: `backend/src/services/ticketService.js` y `backend/src/services/mesaService.js`

El cobro cambia el estado economico del ticket, pero no libera automaticamente la mesa. El cierre de mesa es una accion posterior que exige que exista un ticket cobrado.

```js
export const cobrarTicket = async ({ ticketId, usuario_id }) => {
  const ticket = await Ticket.findById(ticketId);

  if (ticket.estado !== 'PENDIENTE') {
    throw new Error('Solo se puede cobrar un ticket PENDIENTE');
  }

  const comanda = await Comanda.findById(ticket.comanda_id);

  ticket.estado = 'COBRADO';
  ticket.cobradoEn = new Date();
  ticket.cobradoPor = usuario_id;
  await ticket.save();

  await registrarAuditoria({
    accion: 'TICKET_COBRADO',
    usuario_id,
    entidadAfectada: 'Ticket',
    entidadId: ticket._id,
    detalles: `Ticket cobrado para comanda ${comanda._id}`,
  });

  emitirEvento('ticket:cobrado', { ticket });
  return ticket;
};
```

```js
export const cerrarMesa = async ({ mesaId, usuario_id }) => {
  const mesa = await Mesa.findById(mesaId);
  const ticket = await Ticket.findOne({ comanda_id: mesa.comanda_activa_id });

  if (!ticket || ticket.estado !== 'COBRADO') {
    throw new Error('No se puede cerrar una mesa sin ticket cobrado');
  }

  const comandaId = mesa.comanda_activa_id;

  await Comanda.findByIdAndUpdate(comandaId, { estado: 'CERRADA' });
  await Mesa.updateMany(
    { comanda_activa_id: comandaId },
    { estado: 'LIBRE', comanda_activa_id: null },
  );

  await registrarAuditoria({
    accion: 'MESA_CERRADA',
    usuario_id,
    entidadAfectada: 'Mesa',
    entidadId: mesa._id,
    detalles: `Mesa ${mesa.numero} cerrada tras cobro`,
  });

  return mesa;
};
```

Estos fragmentos cubren **CU-17 Cobrar ticket en caja** y **CU-05 Cerrar mesa**.

#### Cola offline en IndexedDB

Archivo: `frontend/src/offline/db.ts`

El frontend guarda operaciones pendientes cuando no hay conexion. Cada operacion queda marcada como `PENDIENTE` y se sincroniza cuando vuelve la red.

```ts
const DB_NAME = 'la_union_offline';
const STORE = 'operaciones';

export const addOfflineOperation = async (
  operation: Omit<OperacionOffline, 'id' | 'estado' | 'creadoEnCliente'>,
) => {
  const store = await txStore('readwrite');
  const payload: OperacionOffline = {
    ...operation,
    estado: 'PENDIENTE',
    creadoEnCliente: new Date().toISOString(),
  };

  return new Promise<number>((resolve, reject) => {
    const request = store.add(payload);
    request.onsuccess = () => resolve(Number(request.result));
    request.onerror = () => reject(request.error);
  });
};
```

Esta pieza justifica la arquitectura local-first descrita en capitulos anteriores y aporta continuidad operativa ante cortes de red.

## Anexo. Matriz completa de trazabilidad

La siguiente tabla recoge la trazabilidad completa entre los casos de uso definidos en los capitulos anteriores y su soporte dentro del sistema. Se incluye como anexo para no sobrecargar el capitulo 4, donde se han desarrollado con mayor detalle los casos de uso principales del flujo operativo de sala, cocina y caja.

| ID | Caso de uso | Vista principal | Endpoint / componente | Soporte en el sistema |
|---|---|---|---|---|
| CU-01 | Iniciar sesion | Login | `POST /api/auth/login` | Autenticacion por email y password con JWT. |
| CU-02 | Cerrar sesion | Menu lateral | `AuthContext` / cierre de sesion frontend | Limpieza de sesion y salida del usuario. |
| CU-03 | Ver estado de mesas por zona | Sala | `GET /api/mesas` | Filtros por zona y estado, resumen operativo por mesa. |
| CU-04 | Abrir mesa | Sala / Comanda | `POST /api/comandas` | Creacion de comanda y asociacion a mesa. |
| CU-05 | Cerrar mesa | Caja / Sala | Logica de cierre de mesa | Solo despues de ticket cobrado y con rol autorizado. |
| CU-06 | Tomar comanda | Comanda | Lineas de comanda | Alta de lineas de carta o menu. |
| CU-07 | Ver comanda de una mesa | Sala / Comanda | Consulta de comanda activa | Visualizacion de comanda y lineas asociadas. |
| CU-08 | Editar comanda | Comanda | Actualizacion de lineas | Correccion o anulacion de lineas pendientes. |
| CU-09 | Solicitar segundos platos / postres | Comanda | Solicitud de pase | Pases retenidos hasta solicitud del camarero. |
| CU-10 | Ver comandas pendientes en KDS | KDS | Consulta KDS | Lineas filtradas por estado y pase solicitado. |
| CU-11 | Marcar comanda en preparacion | KDS | Cambio de estado de linea | Cambio de estado a `EN_PREPARACION`. |
| CU-12 | Marcar plato como listo | KDS | Cambio de estado de linea | Cambio de estado a `LISTO` y aviso a sala. |
| CU-13 | Ver alergenos y observaciones | Comanda / KDS | `LineaComanda` | Etiquetas de alergenos y observaciones por linea. |
| CU-14 | Enviar ticket a caja | Comanda / Caja | Generacion de ticket | Ticket creado a partir de la comanda. |
| CU-17 | Cobrar ticket en caja | Caja | Actualizacion de ticket | Cambio de ticket a `COBRADO`. |
| CU-18 | Ver listado de reservas del dia | Reservas | Consulta de reservas | Consulta por fecha. |
| CU-19 | Gestionar reservas | Reservas | Rutas de reservas | Crear, editar, cancelar y borrar reservas. |
| CU-20 | Asignar mesa a reserva | Reservas | Actualizacion de reserva | Vinculacion de reserva con mesa. |
| CU-21 | Gestionar usuarios y roles | Usuarios | Rutas de usuarios | CRUD de usuarios y roles. |
| CU-25 | Gestionar carta | Carta | Rutas de carta | Alta, edicion y baja logica de platos y tarifas. |
| CU-27 | Enviar notificaciones automaticas | Backend / Socket | `socketService` | Aviso de reserva proxima a mesa ocupada. |
| CU-N01 | Denegar acceso por credenciales incorrectas | Login | Middleware de autenticacion | Error de autenticacion. |
| CU-N02 | Bloquear operacion por rol insuficiente | Backend | Middleware de autorizacion | Control de permisos por rol. |
| CU-N03 | Rechazar comanda por alergenos incompletos | Comanda / backend | Validaciones de comanda | Validacion de alergenos declarados. |
| CU-N05 | Bloquear edicion de linea en preparacion | Comanda / backend | Validaciones de estado | Solo se editan lineas pendientes. |
| CU-N06 | Rechazar ticket por mesa ya cobrada | Caja / backend | Validaciones de ticket | Control de estado del ticket. |
