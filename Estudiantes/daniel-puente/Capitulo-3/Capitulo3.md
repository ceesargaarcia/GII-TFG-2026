# 4. ANÁLISIS Y DISEÑO

La solución propuesta se ha diseñado bajo un enfoque arquitectónico MVC (Modelo - Vista - Controlador), permitiendo una separación clara entre la lógica de negocio, la representación de los datos y la interacción del usuario. A continuación, se desglosan los casos de uso seleccionados para el desarrollo del MVP del sistema de gestión interna del Restaurante La Unión.

## MODELOS

![Modelos](/Estudiantes/daniel-puente/Capitulo-3/imagenes/modelos.svg)

Los modelos representan las entidades centrales del sistema y su estructura en la base de datos. Se han modelado utilizando Mongoose sobre MongoDB, lo que permite definir esquemas y relaciones de forma flexible y adaptada a la operativa real del Restaurante La Unión. Entre los modelos más relevantes se encuentran:

Usuario: Representa a cada persona con acceso al sistema. Almacena sus credenciales, rol (Camarero, Cocinero o Administrador) y el estado de su cuenta, incluyendo el control de intentos fallidos de autenticación.

Zona: Identifica cada área física del restaurante (comedor, bar, terraza, plaza, mirador, balcón y escaleras). Cada zona agrupa sus propias mesas con numeración independiente.

Mesa: Unidad básica del servicio. Pertenece a una zona concreta y registra su estado en tiempo real (libre, ocupada o reservada), así como su comanda activa en curso.

Comanda: Registro digital de los pedidos de una mesa. Se construye línea a línea y cuando llega el momento del cobro genera automáticamente el ticket correspondiente.

LineaComanda: Representa cada plato concreto dentro de una comanda, con su nombre, cantidad, alérgenos, observaciones y estado (pendiente, en preparación o listo). Si el plato forma parte del menú del día, incluye también la modalidad elegida.

Plato: Cada ítem disponible en la carta del restaurante, con nombre, precio y categoría (primero, segundo, postre, bebida o café), e indicación de si puede pedirse como parte del menú del día.

TarifaMenu: Define los precios fijos del menú del día según el tipo de jornada: entre semana o festivo/fin de semana, en modalidad de un plato o dos platos.

Ticket: Resumen económico del servicio de una mesa. Recoge los platos consumidos, precios y total. Solo el Administrador puede cobrarlo, momento en el que la mesa queda liberada para el siguiente servicio.

Reserva: Recoge la ocupación futura de una mesa, indicando nombre del cliente, número de comensales, fecha y hora. Siempre está vinculada a una mesa y zona concretas.

LogAuditoria: Registro inmutable de todas las acciones relevantes del sistema: envíos y ediciones de tickets, cambios de estado en las comandas y cobros en caja. Cada entrada guarda el usuario y el timestamp exacto de la acción.



## CONTROLADORES

![Controladores](/Estudiantes/daniel-puente/Capitulo-3/imagenes/controladores.svg)

Los controladores definen la lógica de negocio que conecta los modelos con las vistas. Cada controlador responde a una serie de endpoints definidos en la API REST, cumpliendo funciones como:

AuthController: Maneja la autenticación de usuarios, la generación y renovación de tokens JWT y el bloqueo de cuentas por intentos fallidos.

MesaController: Gestiona el estado en tiempo real de las mesas por zona, incluyendo la apertura y el cierre tras el cobro del ticket.

ComandaController: Controla la creación, edición y consulta de comandas activas, validando alérgenos y bloqueando la modificación de líneas ya en preparación.

TicketController: Gestiona el envío, edición, reclamación y cobro de tickets, registrando cada acción en el log de auditoría con usuario y timestamp.

KDSController: Recibe las comandas pendientes en cocina y gestiona los cambios de estado de cada línea (pendiente, en preparación, listo), emitiendo notificaciones en tiempo real vía WebSocket al camarero.

ReservaController: Permite crear, editar y cancelar reservas, validando conflictos de horario y emitiendo avisos automáticos cuando queden menos de 5 minutos para la próxima reserva de una mesa ocupada.

UsuarioController: Gestiona el alta, edición y eliminación de usuarios del sistema, con control de acceso restringido al rol Administrador.

CartaController: Permite al Administrador gestionar los platos disponibles en la carta y configurar las tarifas del menú del día.

El uso de controladores desacoplados mejora la mantenibilidad y estabilidad del sistema, y está alineado con las buenas prácticas de desarrollo backend en Node.js con Express.

## VISTAS

![Vistas](/Estudiantes/daniel-puente/Capitulo-3/imagenes/vistas.svg)

Las vistas representan la capa de presentación del sistema y se adaptan al rol autenticado en cada acceso. La aplicación se desarrolla como una PWA pensada para tablet, de modo que cada usuario visualiza únicamente las opciones correspondientes a sus permisos.

En este sentido, existen vistas compartidas entre Camarero y Administrador, como el plano de mesas, la consulta y edición de comandas y el envío de tickets a caja. Esto se debe a que el Administrador también puede asumir funciones operativas de sala cuando sea necesario.

Por otro lado, la vista del Cocinero corresponde al módulo KDS, donde se muestran en tiempo real las comandas pendientes y en preparación, junto con alérgenos y observaciones destacadas. Desde esta pantalla puede actualizar estados y provocar el envío de notificaciones automáticas al camarero.

Finalmente, el Administrador dispone de vistas exclusivas para la gestión de reservas, usuarios y roles, carta, menú del día, caja y auditoría. Todas las vistas se comunican con los controladores mediante la API REST y WebSocket, garantizando que la información se mantenga sincronizada durante el servicio.


### CU-04 Abrir mesa

Permite al Camarero o al Administrador cambiar el estado de una mesa libre a ocupada cuando llegan nuevos clientes al restaurante. Este caso de uso constituye el inicio del flujo operativo en sala, ya que una vez abierta la mesa queda habilitada para asociar una comanda activa y continuar con el servicio.

**Modelos implicados**

El modelo principal implicado es `Mesa`, ya que almacena el identificador de la mesa, la zona a la que pertenece y su estado actual dentro del plano del restaurante. También participa `Zona`, que permite contextualizar la mesa dentro de un área física concreta del establecimiento, y `LogAuditoria`, utilizado para registrar la apertura de la mesa con el usuario responsable y la marca temporal correspondiente.

**Controladores implicados**

La lógica de este caso de uso recae sobre `MesaController`, responsable de comprobar que la mesa se encuentra en estado libre antes de permitir la operación. Una vez validada la acción, el controlador actualiza el estado de la mesa a ocupada, registra el evento en el log de auditoría y propaga el cambio al resto de clientes conectados para mantener la vista de sala sincronizada en tiempo real. 

**Rutas propuestas**

Para soportar este caso de uso, el diseño de la API REST contempla una ruta específica de actualización de estado sobre el recurso mesa. Dicha ruta deberá exigir autenticación previa mediante JWT y restringir la operación a los roles autorizados para operar en sala.

| Método | Ruta | Descripción | Roles permitidos |
|---|---|---|---|
| PATCH | /api/mesas/:mesaId/abrir | Cambia el estado de una mesa libre a ocupada | Camarero, Administrador |

**Vista implicada**

La interacción se realiza desde `MesasView`, que constituye la pantalla principal de sala para Camarero y Administrador. Desde esta vista, el usuario selecciona una mesa libre en el plano agrupado por zonas, confirma la acción y visualiza inmediatamente el cambio de estado a ocupada. 

**Diagrama propuesto**

![AbrirMesa](/Estudiantes/daniel-puente/Capitulo-3/imagenes/abrirMesa.svg)


### CU-06 Tomar comanda

Permite registrar digitalmente los platos solicitados por una mesa ocupada, incluyendo cantidades, observaciones y alérgenos obligatorios. Se trata de una de las funcionalidades clave del nuevo sistema, ya que sustituye a la comanda en papel, conecta directamente sala con cocina y garantiza que la información crítica quede registrada incluso ante fallos puntuales de conectividad gracias al enfoque local-first planteado para la solución.

**Modelos implicados**

Este caso de uso implica los modelos `Mesa`, `Comanda`, `LineaComanda`, `Plato` y `TarifaMenu`. `Mesa` identifica la mesa ocupada sobre la que se realiza la toma del pedido; `Comanda` representa el pedido global; `LineaComanda` almacena cada plato concreto con cantidad, observaciones, alérgenos y estado; `Plato` proporciona la información disponible de carta; y `TarifaMenu` permite aplicar la lógica del menú del día cuando corresponda. Además, `LogAuditoria` puede emplearse para conservar trazabilidad sobre la creación de la comanda.

**Controladores implicados**

La responsabilidad principal recae en `ComandaController`, que valida los datos introducidos, comprueba que la mesa se encuentra ocupada y verifica que todas las líneas incluyan la información obligatoria de alérgenos. También se coordina con `KDSController`, ya que una vez registrada la comanda esta debe quedar disponible en la pantalla de cocina en tiempo real. Si la conectividad falla, el diseño contempla almacenar temporalmente la operación en el dispositivo y sincronizarla posteriormente, en línea con los requisitos de arquitectura local-first del sistema. 

**Rutas propuestas**

Para este caso de uso, la API debe contemplar endpoints que permitan consultar los platos disponibles y registrar una nueva comanda asociada a una mesa ocupada. Todas estas rutas deberán estar protegidas mediante autenticación JWT y limitar su acceso a los roles que pueden operar en sala. 

| Método | Ruta | Descripción | Roles permitidos |
|---|---|---|---|
| GET | /api/platos | Recupera la carta y los platos disponibles para tomar una comanda | Camarero, Administrador |
| GET | /api/tarifas-menu/actual | Obtiene la tarifa vigente del menú del día | Camarero, Administrador |
| POST | /api/comandas | Crea una nueva comanda asociada a una mesa ocupada | Camarero, Administrador |

**Vista implicada**

La funcionalidad se ejecuta desde `ComandaView`, que permite al usuario seleccionar platos de carta o menú del día, indicar cantidades, registrar observaciones y completar de forma obligatoria la información relativa a alérgenos antes de confirmar el envío. Tras la confirmación, la vista debe reflejar si la comanda ha sido enviada correctamente a cocina o si ha quedado pendiente de sincronización local por pérdida temporal de red. 

**Diagrama propuesto**

![tomarComanda](/Estudiantes/daniel-puente/Capitulo-3/imagenes/tomarComanda.svg)

### CU-08 Editar comanda

Permite modificar una comanda activa de una mesa ocupada mientras existan líneas que todavía no hayan entrado en preparación en cocina. Este caso de uso es esencial para adaptarse a la operativa real del restaurante, donde durante el servicio pueden producirse cambios de última hora por parte de los comensales, siempre que no comprometan platos ya iniciados o finalizados en cocina. 

**Modelos implicados**

Intervienen `Comanda`, `LineaComanda`, `Mesa`, `Plato` y `LogAuditoria`. `Comanda` representa el pedido en curso asociado a una mesa; `LineaComanda` permite identificar qué elementos concretos pueden ser modificados; `Mesa` vincula la operación con la mesa activa; `Plato` proporciona la referencia de carta para nuevas incorporaciones o sustituciones; y `LogAuditoria` registra la edición realizada, incluyendo usuario y marca temporal, para asegurar trazabilidad.

**Controladores implicados**

El caso de uso se apoya en `ComandaController`, que recupera la comanda activa, valida los cambios solicitados y comprueba el estado de cada línea antes de permitir su modificación. Si existen líneas en preparación o ya listas, estas deben quedar bloqueadas, tal y como se recoge en el caso de uso negativo asociado a la edición de líneas en cocina. Una vez aplicados los cambios válidos, el controlador actualiza la comanda, registra la operación en auditoría y comunica la modificación al `KDSController` para mantener la pantalla de cocina sincronizada en tiempo real.

**Rutas propuestas**

El diseño de la API contempla una ruta de consulta de la comanda activa de una mesa y otra ruta de actualización para reflejar los cambios autorizados. Ambas operaciones deben exigir autenticación y verificar que el rol del usuario corresponde a un perfil con permisos de sala. 

| Método | Ruta | Descripción | Roles permitidos |
|---|---|---|---|
| GET | /api/comandas/mesa/:mesaId/activa | Recupera la comanda activa asociada a una mesa | Camarero, Administrador |
| PATCH | /api/comandas/:comandaId | Actualiza una comanda activa permitiendo añadir, modificar o eliminar líneas en estado pendiente | Camarero, Administrador |

**Vista implicada**

La edición se realiza desde `ComandaView`, reutilizando la misma vista de toma de comandas pero en modo de actualización. Desde esta pantalla, el usuario puede consultar el detalle del pedido actual, modificar cantidades, añadir nuevos platos o eliminar líneas pendientes, visualizando de forma diferenciada aquellas líneas que ya no son editables por encontrarse en preparación o listas.

**Diagrama propuesto**

![editarComanda](/Estudiantes/daniel-puente/Capitulo-3/imagenes/editarComanda.svg)

### CU-12 Marcar plato como listo

Permite al Cocinero indicar en la pantalla KDS que un plato que estaba en preparación ya se encuentra finalizado y disponible para ser servido en sala. Este caso de uso forma parte del flujo central de coordinación entre cocina y camareros, ya que al completarse el cambio de estado el sistema debe notificar automáticamente al camarero responsable de la mesa para agilizar la entrega del plato al cliente. 

**Modelos implicados**

Los modelos principales involucrados son `Comanda`, `LineaComanda`, `Mesa` y `LogAuditoria`. `LineaComanda` es la entidad más relevante, ya que cada plato individual mantiene su propio estado dentro del ciclo pendiente, en preparación o listo. `Comanda` agrupa las líneas pertenecientes a la misma mesa, `Mesa` permite contextualizar a qué servicio corresponde el plato finalizado y `LogAuditoria` conserva trazabilidad sobre el cambio de estado realizado por cocina.

**Controladores implicados**

La lógica principal recae sobre `KDSController`, encargado de recuperar las líneas de comanda visibles en cocina, verificar que el plato se encuentra efectivamente en estado **en preparación** y actualizarlo a **listo**. Tras esta actualización, el controlador debe coordinarse con el módulo de notificaciones para emitir un evento en tiempo real hacia el camarero correspondiente, cumpliendo así con el requisito de avisos automáticos vía WebSocket cuando un plato esté listo para servir. 

**Rutas propuestas**

Para dar soporte a este caso de uso, la API REST debe permitir consultar las comandas activas en cocina y actualizar el estado de una línea concreta. Estas operaciones deben estar protegidas por autenticación y restringidas al rol Cocinero para mantener la coherencia del flujo operativo.

| Método | Ruta | Descripción | Roles permitidos |
|---|---|---|---|
| GET | /api/kds/comandas | Recupera las comandas visibles en la pantalla KDS | Cocinero |
| PATCH | /api/lineas-comanda/:lineaId/listo | Cambia el estado de una línea de comanda a listo | Cocinero |

**Vista implicada**

La interacción se realiza desde `KDSView`, que muestra al cocinero las comandas pendientes y en preparación con sus platos, cantidades, alérgenos y observaciones destacadas visualmente. Desde esta vista, el usuario puede pulsar la acción **Listo** sobre una línea concreta y provocar tanto la actualización del estado como el envío de la notificación correspondiente al camarero.

**Diagrama propuesto**

![platoListo](/Estudiantes/daniel-puente/Capitulo-3/imagenes/platoListo.svg)

### CU-14 Enviar ticket a caja

Permite al Camarero o al Administrador generar el ticket de una mesa a partir de su comanda activa y dejarlo disponible para su posterior cobro en caja. Este caso de uso supone la transición entre la fase de servicio en sala y la fase de facturación, ya que consolida los platos consumidos, calcula el importe total y registra la información necesaria para su control posterior.

**Modelos implicados**

En este caso intervienen `Mesa`, `Comanda`, `LineaComanda`, `Ticket`, `Zona` y `LogAuditoria`. `Comanda` y `LineaComanda` aportan el detalle de productos consumidos, `Ticket` representa el documento económico generado a partir de ese consumo, `Mesa` y `Zona` identifican el origen del servicio y `LogAuditoria` garantiza la trazabilidad del envío del ticket a caja con usuario y marca temporal.

**Controladores implicados**

La lógica principal se concentra en `TicketController`, que debe recuperar la comanda activa de la mesa, calcular el desglose económico del servicio y generar el ticket con todos los campos obligatorios: camarero, mesa, zona, timestamp, platos, precios y total. Además, debe comprobar que la mesa no haya sido ya cobrada y cerrada, ya que en ese caso se activaría el flujo alternativo asociado al rechazo de ticket por mesa ya cobrada. Finalmente, el envío debe quedar registrado en auditoría para cumplir con los requisitos de trazabilidad del sistema.

**Rutas propuestas**

El diseño de la API contempla una ruta de consulta de la comanda activa y una ruta específica para generar el ticket asociado a la mesa. Ambas deben requerir autenticación previa y limitarse a los roles con acceso operativo a sala o caja.

| Método | Ruta | Descripción | Roles permitidos |
|---|---|---|---|
| GET | /api/comandas/mesa/:mesaId/activa | Recupera la comanda activa sobre la que se generará el ticket | Camarero, Administrador |
| POST | /api/tickets | Genera y envía a caja el ticket de una mesa | Camarero, Administrador |

**Vista implicada**

La acción se desencadena desde `ComandaView` o desde una vista resumida de la mesa dentro del plano de sala. En ambas alternativas, el usuario consulta el detalle del servicio y pulsa la opción **Enviar a caja**, tras lo cual el sistema muestra el ticket generado y lo deja disponible en la vista de caja para el Administrador. 

**Diagrama propuesto**

![ticketCaja](/Estudiantes/daniel-puente/Capitulo-3/imagenes/ticketCaja.svg)


### CU-17 Cobrar ticket en caja

Permite al Administrador confirmar el cobro de un ticket previamente enviado a caja, cerrar la operación económica y dar por finalizado el servicio asociado a la mesa. Este caso de uso es exclusivo del rol Administrador y constituye el cierre formal del flujo de facturación, ya que a partir de él el sistema deja constancia del cobro y habilita la liberación de la mesa para un nuevo servicio.

**Modelos implicados**

Los modelos principales son `Ticket`, `Mesa`, `Usuario` y `LogAuditoria`. `Ticket` almacena la información económica pendiente de cobro, `Mesa` representa el recurso físico que debe quedar liberado tras finalizar la operación, `Usuario` identifica al Administrador que ejecuta el cobro y `LogAuditoria` registra de forma inmutable la acción realizada con su correspondiente timestamp.

**Controladores implicados**

La lógica se centraliza en `TicketController`, que agrupa la gestión completa del ciclo de vida del ticket, incluyendo su envío, cobro y registro en auditoría. Este controlador recupera los tickets pendientes de cobro, permite seleccionar uno concreto y valida que el usuario autenticado disponga del rol Administrador antes de confirmar la operación. Tras ello, actualiza el estado del ticket a cobrado, registra la acción en el log de auditoría y coordina el cierre de la mesa asociada a través de `MesaController`, en consonancia con la relación `<<include>>` definida entre **CU-17 Cobrar ticket en caja** y **CU-05 Cerrar mesa**.

**Rutas propuestas**

La API REST debe proporcionar una ruta para consultar tickets pendientes en caja y otra para confirmar el cobro de uno de ellos. Ambas deben exigir autenticación, y la segunda debe quedar restringida exclusivamente al Administrador.

| Método | Ruta | Descripción | Roles permitidos |
|---|---|---|---|
| GET | /api/caja/tickets-pendientes | Recupera los tickets pendientes de cobro | Administrador |
| PATCH | /api/tickets/:ticketId/cobrar | Marca un ticket como cobrado y desencadena el cierre de mesa | Administrador |

**Vista implicada**

La interacción se realiza desde `CajaView`, pantalla exclusiva del Administrador donde se listan los tickets pendientes con la información mínima necesaria para operar: mesa, zona, camarero, desglose resumido y total. Desde esta vista, el Administrador selecciona un ticket, confirma el cobro y provoca el cambio de estado tanto del ticket como de la mesa asociada.

**Diagrama propuesto**

![cobrarTicket](/Estudiantes/daniel-puente/Capitulo-3/imagenes/cobrarTicket.svg)

### CU-05 Cerrar mesa

Permite al Administrador liberar una mesa ocupada una vez que el ticket asociado ha sido cobrado correctamente. Se trata del último paso del flujo operativo de atención a una mesa, ya que con esta acción el sistema da por finalizado el servicio, elimina la ocupación activa y deja la mesa disponible para nuevos comensales.

**Modelos implicados**

Los modelos implicados son `Mesa`, `Ticket`, `Zona` y `LogAuditoria`. `Mesa` es la entidad principal, dado que su estado cambia de ocupada a libre; `Ticket` se utiliza para verificar que el servicio ha sido realmente cobrado antes de autorizar el cierre; `Zona` permite mantener la coherencia visual del plano del restaurante y `LogAuditoria` registra el cierre con su correspondiente usuario y timestamp.

**Controladores implicados**

La lógica funcional se apoya en `MesaController`, que recibe la solicitud de cierre, verifica que la mesa está ocupada y comprueba que el ticket vinculado se encuentra ya en estado cobrado. Solo si ambas condiciones se cumplen, el controlador actualiza el estado de la mesa a libre, registra el evento de cierre y difunde el cambio al resto de dispositivos conectados para que el plano de sala se mantenga sincronizado en tiempo real. Dado que el requisito funcional establece que el cierre es exclusivo del Administrador tras el cobro, esta operación debe estar protegida por validación de rol y dependencia respecto al estado previo del ticket.

**Rutas propuestas**

La API debe exponer una ruta específica de cierre de mesa, restringida al rol Administrador y condicionada a que el ticket asociado haya sido cobrado previamente.

| Método | Ruta | Descripción | Roles permitidos |
|---|---|---|---|
| PATCH | /api/mesas/:mesaId/cerrar | Cambia el estado de una mesa ocupada a libre tras verificar el cobro | Administrador |

**Vista implicada**

La operación puede realizarse desde `CajaView` como consecuencia del cobro o desde `MesasView` para reflejar visualmente que la mesa vuelve a quedar libre dentro del plano del restaurante. En ambos casos, el cambio debe propagarse de inmediato al resto de usuarios conectados para que el estado del plano siga siendo consistente en tiempo real. 

**Diagrama propuesto**

![cerrarMesa](/Estudiantes/daniel-puente/Capitulo-3/imagenes/cerrarMesa.svg)


## Diagrama Entidad Relacion (Pendiente)

## Diagrama General del Sistema

![diagramaGeneral](/Estudiantes/daniel-puente/Capitulo-3/imagenes/diagramaGeneral.svg)

El diagrama anterior muestra la arquitectura general del sistema de gestión del Restaurante La Unión. El sistema se estructura en cuatro capas principales:

- **Frontend (cliente):** Dos interfaces PWA desarrolladas en React con TypeScript, adaptadas al rol del usuario autenticado. La vista de Camarero y Administrador permite gestionar mesas, comandas, tickets y reservas; la vista de Cocinero presenta el módulo KDS con las comandas activas en cocina.

- **Backend API:** Servidor Express con TypeScript organizado en controladores por dominio, protegidos por un middleware de autenticación JWT y control de rol. Los controladores acceden a los datos a través de los modelos Mongoose definidos 
sobre MongoDB.

- **Tiempo real:** La comunicación entre cocina y sala se gestiona mediante Socket.io, que emite eventos como PLATO_LISTO, ACTUALIZAR_KDS o AVISO_RESERVA.

- **Soporte offline:** Un Service Worker intercepta las peticiones de red y, ante una pérdida de conectividad, almacena las operaciones pendientes en IndexedDB. Cuando la red se recupera, el Background Sync API sincroniza automáticamente los datos con el servidor sin intervención del usuario.
