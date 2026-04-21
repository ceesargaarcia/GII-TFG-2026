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

Los diagramas entidad-relación muestran cómo estas entidades se relacionan entre sí en casos de uso como "Tomar comanda", "Enviar ticket a caja" o "Gestionar reservas". Esto permite una visión estructurada de la base de datos y su diseño orientado a la integridad referencial y la trazabilidad de las operaciones.

## CONTROLADORES

![Modelos](/Estudiantes/daniel-puente/Capitulo-3/imagenes/controladores.svg)

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

## VISTAS (Falta Diagrama)

Las vistas representan la capa de presentación del sistema y se adaptan al rol autenticado en cada acceso. La aplicación se desarrolla como una PWA pensada para tablet, de modo que cada usuario visualiza únicamente las opciones correspondientes a sus permisos.

En este sentido, existen vistas compartidas entre Camarero y Administrador, como el plano de mesas, la consulta y edición de comandas y el envío de tickets a caja. Esto se debe a que el Administrador también puede asumir funciones operativas de sala cuando sea necesario.

Por otro lado, la vista del Cocinero corresponde al módulo KDS, donde se muestran en tiempo real las comandas pendientes y en preparación, junto con alérgenos y observaciones destacadas. Desde esta pantalla puede actualizar estados y provocar el envío de notificaciones automáticas al camarero.

Finalmente, el Administrador dispone de vistas exclusivas para la gestión de reservas, usuarios y roles, carta, menú del día, caja y auditoría. Todas las vistas se comunican con los controladores mediante la API REST y WebSocket, garantizando que la información se mantenga sincronizada durante el servicio.