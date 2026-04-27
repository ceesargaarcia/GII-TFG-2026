# ANÁLISIS

La solución propuesta se ha diseñado bajo un enfoque arquitectónico MVC (Modelo - Vista - Controlador), lo que permite separar de forma clara la lógica de negocio, la representación de los datos y la interacción con el usuario. Esta organización mejora la mantenibilidad, facilita la escalabilidad del sistema y reduce el acoplamiento entre capas. El desarrollo del MVP se ha centrado en los casos de uso más relevantes para la operativa interna del Restaurante La Unión.

## ANALISIS MODELO-VISTA-CONTROLADOR

### Resumen de casos de uso

Antes de detallar cada flujo, resulta útil agrupar los casos de uso seleccionados según el actor, el componente principal y el resultado esperado. Esta tabla reduce redundancias y ofrece una visión global del alcance funcional del MVP.

| CU | Caso de uso | Actor | Componente principal | Resultado |
|---|---|---|---|---|
| CU-04 | Abrir mesa | Camarero / Administrador | Mesa | La mesa pasa de libre a ocupada. |
| CU-06 | Tomar comanda | Camarero / Administrador | Comanda | Se registra el pedido y se envía a cocina. |
| CU-08 | Editar comanda | Camarero / Administrador | Comanda | Se modifican solo las líneas aún editables. |
| CU-12 | Marcar plato como listo | Cocinero | LineaComanda | El plato cambia a listo y se notifica a sala. |
| CU-14 | Enviar ticket a caja | Camarero / Administrador | Ticket | El ticket queda disponible para cobro. |
| CU-17 | Cobrar ticket en caja | Administrador | Ticket | El ticket queda cobrado y se libera la mesa. |
| CU-05 | Cerrar mesa | Administrador | Mesa | La mesa vuelve a estado libre. |

### Modelos

![Modelos](/Estudiantes/daniel-puente/Capitulo-3/imagenes/modelos.svg)


Los modelos representan las entidades centrales del sistema y su estructura en la base de datos. Se han implementado con Mongoose sobre MongoDB, lo que permite definir esquemas flexibles y adaptados a la operativa real del restaurante. Entre los modelos principales se encuentran `Usuario`, `Zona`, `Mesa`, `Comanda`, `LineaComanda`, `Plato`, `TarifaMenu`, `Ticket`, `Reserva` y `LogAuditoria`.

`Usuario` almacena las credenciales y el rol de acceso; `Zona` identifica las áreas físicas del restaurante; `Mesa` registra el estado de cada mesa y su relación con una zona; `Comanda` agrupa el pedido activo asociado a una mesa; `LineaComanda` representa cada plato solicitado con su estado; `Plato` define la carta disponible; `TarifaMenu` gestiona los precios del menú del día; `Ticket` resume el importe del servicio; `Reserva` modela la ocupación futura; y `LogAuditoria` conserva la trazabilidad de las acciones relevantes del sistema.

Los diagramas entidad-relación reflejan cómo estas entidades se relacionan entre sí en flujos como la toma de comanda, el envío del ticket a caja y la gestión de reservas. Esta estructura permite mantener integridad lógica, trazabilidad y coherencia funcional en todo el sistema.

### Vistas

![Vistas](/Estudiantes/daniel-puente/Capitulo-3/imagenes/vistas.svg)

Las vistas constituyen la capa de presentación y se adaptan al rol autenticado en cada sesión. La aplicación se ha planteado como una PWA orientada a tablet, de modo que cada usuario visualiza únicamente las funciones correspondientes a sus permisos.

Existen vistas compartidas entre Camarero y Administrador, como el plano de mesas, la consulta y edición de comandas y el envío de tickets a caja. Por su parte, el Cocinero accede a la vista KDS, donde visualiza en tiempo real las comandas pendientes y en preparación. El Administrador dispone además de vistas exclusivas para reservas, usuarios, carta, menú del día, caja y auditoría.

### Controladores

![Controladores](/Estudiantes/daniel-puente/Capitulo-3/imagenes/controladores.svg)

Los controladores definen la lógica de negocio que conecta los modelos con las vistas. Cada uno actúa como punto de entrada para los distintos recursos de la API REST, aplicando validaciones, control de permisos y actualización de estado.

Los controladores principales son `AuthController`, `MesaController`, `ComandaController`, `TicketController`, `KDSController`, `ReservaController`, `UsuarioController` y `CartaController`. `AuthController` gestiona autenticación, emisión de JWT y bloqueo por intentos fallidos; `MesaController` controla la apertura y cierre de mesas; `ComandaController` gestiona la creación y edición de comandas; `TicketController` centraliza el envío y cobro de tickets; `KDSController` actualiza el estado de los platos en cocina; `ReservaController` administra reservas; `UsuarioController` gestiona el alta y mantenimiento de usuarios; y `CartaController` permite administrar la carta y el menú del día.



#### CU-04 Abrir mesa

Permite al Camarero o al Administrador cambiar el estado de una mesa libre a ocupada cuando llegan nuevos clientes. Es el punto de partida del servicio en sala, ya que habilita la asociación de una comanda activa a esa mesa.

`Mesa` y `Zona` son los modelos implicados, mientras que `MesaController` valida que la mesa esté libre y actualiza su estado. La operación queda registrada en `LogAuditoria` para conservar trazabilidad. La ruta propuesta es `PATCH /api/mesas/:mesaId/abrir`, accesible para Camarero y Administrador. La vista implicada es `MesasView`.

![abrirMesa](/Estudiantes/daniel-puente/Capitulo-3/imagenes/abrirMesa.svg)

#### CU-06 Tomar comanda

Permite registrar los platos solicitados por una mesa ocupada, incluyendo cantidades, observaciones y alérgenos obligatorios. Sustituye la comanda en papel y conecta sala con cocina mediante una comunicación estructurada.

En este flujo intervienen `Mesa`, `Comanda`, `LineaComanda`, `Plato` y `TarifaMenu`, con apoyo de `LogAuditoria`. La lógica principal se concentra en `ComandaController`, que valida la mesa, la disponibilidad de la carta y la información obligatoria de cada línea. Las rutas propuestas son `GET /api/platos`, `GET /api/tarifas-menu/actual` y `POST /api/comandas`. La vista implicada es `ComandaView`.

![tomarComanda](/Estudiantes/daniel-puente/Capitulo-3/imagenes/tomarComanda.svg)

#### CU-08 Editar comanda

Permite modificar una comanda activa mientras existan líneas que todavía no hayan entrado en preparación. El objetivo es adaptar el pedido a cambios de última hora sin alterar platos ya procesados en cocina.

Intervienen `Comanda`, `LineaComanda`, `Mesa`, `Plato` y `LogAuditoria`. `ComandaController` recupera la comanda activa, verifica el estado de las líneas y permite solo las operaciones sobre elementos pendientes. Las rutas propuestas son `GET /api/comandas/mesa/:mesaId/activa` y `PATCH /api/comandas/:comandaId`. La vista reutilizada es `ComandaView`.

![editarComanda](/Estudiantes/daniel-puente/Capitulo-3/imagenes/editarComanda.svg)

#### CU-12 Marcar plato como listo

Permite al Cocinero indicar que un plato ya está finalizado y listo para servirse. Este cambio de estado activa la notificación en tiempo real al camarero responsable de la mesa.

Los modelos implicados son `Comanda`, `LineaComanda`, `Mesa` y `LogAuditoria`. La lógica la ejecuta `KDSController`, que actualiza el estado de la línea y emite el evento correspondiente mediante WebSocket. Las rutas propuestas son `GET /api/kds/comandas` y `PATCH /api/lineas-comanda/:lineaId/listo`. La vista implicada es `KDSView`.

![platoListo](/Estudiantes/daniel-puente/Capitulo-3/imagenes/platoListo.svg)

#### CU-14 Enviar ticket a caja

Permite generar el ticket de una mesa a partir de su comanda activa y dejarlo preparado para su cobro posterior. Este flujo marca la transición entre servicio en sala y facturación.

Los modelos implicados son `Mesa`, `Comanda`, `LineaComanda`, `Ticket`, `Zona` y `LogAuditoria`. `TicketController` consolida el desglose económico, calcula el total y registra el envío a caja. Las rutas propuestas son `GET /api/comandas/mesa/:mesaId/activa` y `POST /api/tickets`. La vista implicada es `ComandaView` o una vista resumida de mesa.

![ticketCaja](/Estudiantes/daniel-puente/Capitulo-3/imagenes/ticketCaja.svg)

#### CU-17 Cobrar ticket en caja

Permite al Administrador confirmar el cobro de un ticket previamente enviado a caja. Con esta acción se cierra la operación económica y se deja lista la mesa para su posterior liberación.

Los modelos implicados son `Ticket`, `Mesa`, `Usuario` y `LogAuditoria`. `TicketController` valida el rol del usuario, marca el ticket como cobrado y coordina el cierre de la mesa con `MesaController`. Las rutas propuestas son `GET /api/caja/tickets-pendientes` y `PATCH /api/tickets/:ticketId/cobrar`. La vista implicada es `CajaView`.

![cobrarTicket](/Estudiantes/daniel-puente/Capitulo-3/imagenes/cobrarTicket.svg)

#### CU-05 Cerrar mesa

Permite liberar una mesa ocupada una vez que el ticket asociado ha sido cobrado correctamente. Es el último paso del flujo operativo de atención a una mesa.

Los modelos implicados son `Mesa`, `Ticket`, `Zona` y `LogAuditoria`. `MesaController` verifica que el ticket esté cobrado y actualiza la mesa a estado libre. La ruta propuesta es `PATCH /api/mesas/:mesaId/cerrar`, exclusiva del rol Administrador. La operación puede reflejarse tanto en `CajaView` como en `MesasView`.

![cerrarMesa](/Estudiantes/daniel-puente/Capitulo-3/imagenes/cerrarMesaa.svg)

# DISEÑO

## Tecnologías utilizadas

### Frontend

| Tecnología | Propósito | Motivo de elección |
|---|---|---|
| React | Biblioteca principal de interfaz | Permite construir una SPA modular y reutilizable. |
| TypeScript | Tipado estático | Reduce errores y mejora la mantenibilidad. |
| Material UI (MUI) | Sistema de componentes | Acelera el desarrollo con una base visual consistente. |
| React Router | Navegación | Gestiona las vistas y rutas protegidas por rol. |
| Axios | Cliente HTTP | Simplifica interceptores JWT y manejo centralizado de errores. |
| Socket.io | Tiempo real | Sincroniza sala, cocina y caja al instante. |
| Service Worker + IndexedDB | Soporte offline | Permite un enfoque local-first con sincronización posterior. |
| Vite | Herramienta de desarrollo | Ofrece un entorno rápido y ligero para el frontend. |

### Backend

| Tecnología | Propósito | Motivo de elección |
|---|---|---|
| Node.js | Entorno de ejecución | Adecuado para APIs modernas y escalables. |
| Express | Framework HTTP | Facilita la creación de rutas y controladores. |
| Mongoose | ODM | Modela colecciones MongoDB con validaciones y referencias. |
| Socket.io | Eventos en tiempo real | Permite emitir notificaciones inmediatas entre módulos. |
| JWT | Autenticación | Implementa acceso stateless con tokens firmados. |
| bcrypt | Hash de contraseñas | Aumenta la seguridad del almacenamiento de credenciales. |
| TypeScript | Tipado estático | Mejora la robustez del backend y la calidad del código. |

### Base de datos

| Tecnología | Propósito | Motivo de elección |
|---|---|---|
| MongoDB | Base documental | Se adapta bien a entidades flexibles como comandas o reservas. |
| MongoDB Atlas | Despliegue en la nube | Facilita la producción y la disponibilidad del sistema. |

### Infraestructura y herramientas

| Tecnología | Propósito | Motivo de elección |
|---|---|---|
| Git + GitHub | Control de versiones | Permite trazabilidad y trabajo ordenado sobre el proyecto. |
| Postman | Pruebas de API | Facilita la validación manual de endpoints. |
| PlantUML | Diagramación | Permite documentar visualmente la arquitectura y los casos de uso. |

## Diagrama general del sistema

![diagramaGeneral](/Estudiantes/daniel-puente/Capitulo-3/imagenes/diagramaGeneral.svg)

El sistema se estructura en cuatro capas principales: frontend, backend API, tiempo real y soporte offline. La aplicación cliente se desarrolla como una PWA en React con TypeScript, mientras que el backend se implementa con Express y Mongoose sobre MongoDB. La sincronización en tiempo real se gestiona con Socket.io y el modo offline con Service Worker e IndexedDB.

## Diagrama entidad-relación

![diagramaEntidadRelacion](/Estudiantes/daniel-puente/Capitulo-3/imagenes/diagramaEntidadRelacion.svg)

## Modelos MongoDB

Los modelos de persistencia se implementan mediante Mongoose sobre MongoDB, definiendo esquemas con referencias entre documentos. Esta elección permite trasladar el diseño lógico a una estructura documental flexible sin perder validaciones ni coherencia funcional.

| Modelo | Campos principales | Relaciones |
|---|---|---|
| `Usuario` | nombre, email, passwordHash, rol, activo, intentosFallidos, bloqueadoHasta | Referenciado por `Comanda`, `Ticket` y `LogAuditoria`. |
| `Zona` | nombre, descripcion | Relacionada con `Mesa` y `Reserva`. |
| `Mesa` | numero, estado, zona_id, comanda_activa_id | Referencia a `Zona` y opcionalmente a `Comanda`. |
| `Comanda` | mesa_id, camarero_id, estado, creadaEn | Referencia a `Mesa` y `Usuario`. |
| `LineaComanda` | comanda_id, plato_id, tarifa_menu_id, cantidad, observaciones, estado, esMenu | Referencia a `Comanda`, `Plato` y opcionalmente a `TarifaMenu`. |
| `Plato` | nombre, precio, categoria, disponible, disponibleEnMenu | Referenciado por `LineaComanda`. |
| `TarifaMenu` | tipo, modalidad, precio, vigente | Referenciado opcionalmente por `LineaComanda`. |
| `Ticket` | comanda_id, camarero_id, total, estado, creadoEn, cobradoEn, cobradoPor | Referencia a `Comanda` y `Usuario`. |
| `Reserva` | nombreCliente, telefono, comensales, fecha, mesa_id, zona_id, estado | Referencia a `Mesa` y `Zona`. |
| `LogAuditoria` | accion, usuario_id, entidadAfectada, entidadId, timestamp | Referencia a `Usuario`. |

### Ejemplo de esquema Mongoose

```ts
const mesaSchema = new Schema({
  numero: { type: Number, required: true },
  estado: {
    type: String,
    enum: ['libre', 'ocupada', 'reservada'],
    default: 'libre'
  },
  zona_id: { type: Types.ObjectId, ref: 'Zona', required: true },
  comanda_activa_id: { type: Types.ObjectId, ref: 'Comanda', default: null }
});
```

## Estructura del proyecto

### Backend

```text
backend/
├── src/
│   ├── config/
│   ├── controllers/
│   ├── services/
│   ├── models/
│   ├── routes/
│   ├── middleware/
│   ├── sockets/
│   │   ├── handlers/
│   │   └── connection.ts
│   ├── utils/
│   ├── types/
│   ├── app.ts
│   └── index.ts
├── .env
└── package.json
```

### Frontend

```text
frontend/
├── public/
│   ├── icons/
│   ├── manifest.json
│   └── sw.js
├── src/
│   ├── assets/
│   │   └── styles/
│   ├── components/
│   │   ├── ui/
│   │   └── layout/
│   ├── views/
│   ├── router/
│   ├── context/
│   ├── hooks/
│   ├── services/
│   │   ├── api.ts
│   │   └── socket.ts
│   ├── types/
│   ├── utils/
│   ├── App.tsx
│   └── main.tsx
├── .env
├── vite.config.ts
└── package.json
```

## Wireframes