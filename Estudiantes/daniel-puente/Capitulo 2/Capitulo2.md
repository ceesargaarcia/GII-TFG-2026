# Capítulo 2

# Modelo de Dominio

El sistema es una aplicación web progresiva (PWA) pensada para funcionar directamente desde el navegador de una tablet. Según el rol con el que se inicie sesión (Camarero, Cocinero o Administrador), la interfaz se adapta mostrando solo lo que cada usuario necesita.

##  Glosario

- **Usuario:** Cualquier persona que accede al sistema con sus credenciales. Puede ser un camarero, un cocinero o el administrador del restaurante. Su rol determina qué puede ver y hacer.

- **Zona:** Representa cada área física del restaurante: comedor, bar, terraza, plaza, mirador, balcón y escaleras. Cada zona tiene sus propias mesas con numeración independiente, lo que permite organizar el servicio por espacios diferenciados.

- **Mesa:** Unidad básica del servicio. Pertenece a una zona concreta, puede tener una comanda activa en curso y recibir reservas en distintos tramos horarios a lo largo del día.

- **Reserva:** Recoge la ocupación futura de una mesa: quién viene, cuántos son, a qué hora y en qué fecha. Siempre está vinculada a una mesa y una zona concretas, y es gestionada por el administrador.

- **Comanda:** Registro digital de todo lo que piden los comensales de una mesa. Se construye línea a línea y cuando llega el momento del cobro se genera automáticamente el ticket correspondiente.

- **LineaComanda:** Representa cada plato concreto dentro de una comanda: nombre, cantidad, alérgenos, observaciones y su estado (pendiente, en preparación o listo). Si el plato forma parte del menú del día, también indica la modalidad elegida: un plato o dos platos.

- **Plato:** Cada ítem disponible en la carta del restaurante, con su nombre, precio y categoría (primero, segundo, postre, bebida o café). Además, se indica si ese plato puede pedirse como parte del menú del día.

- **TarifaMenu:** Define los precios fijos del menú del día según el tipo de jornada. Entre semana, el menú de un plato cuesta 13 € y el de dos platos 17 €. En fines de semana y festivos, los precios son 18 € y 22 € respectivamente. El administrador puede modificar estas tarifas cuando sea necesario.

- **Ticket:** Resumen económico del servicio de una mesa: qué se consumió, precio y suma total. Solo el administrador puede cobrarlo, y en ese momento la mesa queda liberada para el siguiente servicio.

- **LogAuditoria:** Registro inmutable de todo lo que ocurre en el sistema: envíos y ediciones de tickets, cambios de estado en las comandas y cobros en caja. Cada entrada guarda quién hizo la acción y cuándo, lo que permite rastrear cualquier incidencia con precisión.

## Diagrama de Clases

![Diagrama de Clases](/Estudiantes/daniel-puente/Capitulo%202/imagenes/DiagramaClases.svg)

## Diagrama de Objetos

![Diagrama de Objetos](/Estudiantes/daniel-puente/Capitulo%202/imagenes/DiagramaObjetos.svg)

## Diagrama de Estados

![Diagrama de Estados 1](/Estudiantes/daniel-puente/Capitulo%202/imagenes/EstadosComanda.svg)
![Diagrama de Estados 2](/Estudiantes/daniel-puente/Capitulo%202/imagenes/DiagramaEstadosMesa.svg)
![Diagrama de Estados 3](/Estudiantes/daniel-puente/Capitulo%202/imagenes/EstadosReserva.png)

## Requisitos Funcionales

Los siguientes requisitos funcionales han sido identificados como esenciales para el correcto funcionamiento del sistema de gestión interna del Restaurante La Unión:

### Gestión de usuarios y acceso

| ID | Descripción |
|----|-------------|
| RF01 | El sistema debe permitir al Administrador autenticar usuarios mediante email y contraseña, generando un token de acceso JWT y un token de actualización. |
| RF02 | Cada usuario debe tener un rol asociado (Camarero, Cocinero o Administrador) que limite su acceso según permisos definidos. |
| RF03 | El sistema debe bloquear temporalmente el acceso tras 5 intentos fallidos de autenticación. |
| RF04 | El sistema debe permitir al Administrador crear, editar y eliminar usuarios del sistema. |

### Gestión de mesas y zonas

| ID | Descripción |
|----|-------------|
| RF05 | El sistema debe permitir visualizar el estado en tiempo real de todas las mesas agrupadas por zona (libre, ocupada, reservada). |
| RF06 | El sistema debe permitir abrir y cerrar mesas, siendo el cierre exclusivo del Administrador tras el cobro. |
| RF07 | El sistema debe permitir al Administrador configurar el plano del restaurante: agregar, eliminar, mover, unir y separar mesas por zona. |

### Gestión de comandas

| ID | Descripción |
|----|-------------|
| RF08 | El sistema debe permitir al camarero tomar comandas digitales seleccionando platos de la carta o del menú del día con su cantidad. |
| RF09 | El sistema debe exigir el registro obligatorio de alérgenos en cada línea de comanda. |
| RF10 | El sistema debe guardar las comandas localmente en IndexedDB cuando no haya conexión WiFi y sincronizarlas automáticamente al recuperar la conexión utilizando Background Sync. |
| RF11 | El sistema debe permitir editar una orden activa, bloqueando la modificación de los platos que ya están en preparación o listos en la cocina. |
| RF12 | El sistema debe permitir al camarero solicitar los segundos platos a la cocina únicamente cuando los primeros hayan sido marcados como listos. |
| RF13 | Cualquier edición sobre una comanda debe quedar registrada en el log de auditoría con usuario y timestamp. |

### Gestión de cocina (KDS)

| ID | Descripción |
|----|-------------|
| RF14 | El sistema debe mostrar al Cocinero las comandas pendientes en tiempo real en la pantalla KDS, con alérgenos y observaciones destacadas visualmente. |
| RF15 | El sistema debe permitir al cocinero marcar comandas en preparación y platos como listos, actualizando el estado en tiempo real. |
| RF16 | El sistema debe notificar automáticamente al camarero vía WebSocket cuando un plato esté listo para servir. |
| RF17 | El sistema debe actualizar el KDS en tiempo real cuando un camarero edite una comanda que ya se encuentre en cocina. |

### Gestión de tickets y caja

| ID | Descripción |
|----|-------------|
| RF18 | El sistema debe permitir al camarero enviar el ticket de una mesa a caja, generando un desglose de platos, precios y total. |
| RF19 | El ticket debe incluir obligatoriamente: identificador del camarero, mesa, zona, timestamp y desglose de platos con precios. |
| RF20 | El sistema debe permitir al camarero editar y reclamar un ticket enviado siempre que no haya sido cobrado. |
| RF21 | El cobro de un ticket debe ser exclusivo del Administrador, quien tras cobrarlo cerrará la mesa asociada. |

### Gestión de reservas

| ID | Descripción |
|----|-------------|
| RF22 | El sistema debe permitir al Administrador crear, editar y cancelar reservas, asignando mesa, zona, fecha, hora y número de comensales. |
| RF23 | El sistema debe impedir la creación de dos reservas activas para la misma mesa en el mismo tramo horario. |
| RF24 | El sistema debe enviar automáticamente un aviso al camarero vía WebSocket cuando queden 5 minutos o menos para una reserva sobre una mesa ocupada. |

### Gestión de carta y menú del día

| ID | Descripción |
|----|-------------|
| RF25 | El sistema debe permitir al Administrador agregar, editar y eliminar platos de la carta con nombre, precio y categoría. |
| RF26 | El sistema debe permitir al Administrador configurar las tarifas del menú del día definiendo dos tipos (entre semana y festivo/fin de semana), cada uno con precio para modalidad de un plato y modalidad de dos platos. |
| RF27 | El sistema debe permitir al camarero registrar un menú del día seleccionando la modalidad (un plato o dos platos) y aplicando automáticamente la tarifa correspondiente según si el día es entre semana o festivo/fin de semana, independientemente de la combinación de platos elegida por el comensal. |

### Auditoría

| ID | Descripción |
|----|-------------|
| RF28 | El sistema debe registrar de forma inmutable todas las acciones relevantes (envío y edición de tickets, cambios de estado de comandas, cobros) con usuario y marca de tiempo. |
| RF29 | El sistema debe permitir al administrador consultar el registro de auditoría completo. |

---

## Requisitos No Funcionales

### Seguridad

| ID | Descripción |
|----|-------------|
| RNF01 | El sistema debe utilizar HTTPS en todas sus comunicaciones entre cliente y servidor. |
| RNF02 | Todos los endpoints de la API REST deben validar el token JWT y el rol del usuario antes de ejecutar cualquier operación. |
| RNF03 | Los alérgenos registrados en una comanda enviada a cocina deben ser inmutables para garantizar la seguridad alimentaria conforme al Reglamento (UE) Nº 1169/2011. |
| RNF04 | Los tokens de actualización deben almacenarse en cookies seguras (HttpOnly) para prevenir ataques. |

### Disponibilidad y rendimiento

| ID | Descripción |
|----|-------------|
| RNF05 | El sistema debe funcionar de forma completa ante fallos puntuales de la red WiFi local mediante arquitectura local-first. |
| RNF06 | El sistema debe sincronizar automáticamente los datos pendientes en cuanto se recupere la conexión, sin intervención del usuario. |
| RNF07 | El tiempo de respuesta de la API ante cualquier petición no debe superar los 3 segundos en condiciones normales de uso. |
| RNF08 | Las notificaciones en tiempo real (plato listo, aviso de reserva) deben entregarse en menos de 2 segundos tras producirse el evento. |

### Usabilidad

| ID | Descripción |
|----|-------------|
| RNF09 | La interfaz debe ser instalable en cualquier tableta con navegador moderno sin necesidad de tienda de aplicaciones, mediante Web App Manifest. |
| RNF10 | La interfaz debe adaptarse a pantallas táctiles de tablets y ser operable con los dedos, sin necesidad de teclado físico durante el servicio. |
| RNF11 | Cada rol debe acceder directamente a su pantalla principal después del inicio de sesión, sin pasos intermedios innecesarios. |

### Mantenibilidad y escalabilidad

| ID | Descripción |
|----|-------------|
| RNF12 | El backend debe seguir una arquitectura modular por dominio (mesas, comandas, reservas, usuarios…) para facilitar su evolución independiente. |
| RNF13 | El sistema debe desplegarse sobre la infraestructura existente del restaurante (red WiFi y tabletas disponibles) sin requerir hardware adicional. |

# Disciplina de Requisitos

Esta disciplina tiene como finalidad establecer, de manera clara y estructurada, quiénes interactúan con el sistema y qué funcionalidades deben estar disponibles para cada uno de ellos. A partir de esto, se definen los casos de uso principales, su prioridad, y se detalla su comportamiento esperado.

## Identificación de Actores

![Actores](/Estudiantes/daniel-puente/Capitulo%202/imagenes/Actores.svg)

## Casos de Uso Principales

En esta sección se detallan los casos de uso que conforman el flujo principal de operaciones y administración del Restaurante La Unión.

### Tabla de Actores y Casos de Uso 

| ID | Caso de uso | Camarero | Administrador | Cocinero | Sist. Notif. |
|---|---|:---:|:---:|:---:|:---:|
| CU-01 | Iniciar sesión | ✅ | ✅ | ✅ | |
| CU-02 | Cerrar sesión | ✅ | ✅ | ✅ | |
| CU-03 | Ver estado de mesas por zona | ✅ | ✅ | | |
| CU-04 | Abrir mesa | ✅ | ✅ | | |
| CU-05 | Cerrar mesa | | ✅ | | |
| CU-06 | Tomar comanda | ✅ | ✅ | | |
| CU-07 | Ver comanda de una mesa | ✅ | ✅ | | |
| CU-08 | Editar comanda | ✅ | ✅ | | |
| CU-09 | Solicitar segundos platos / postres | ✅ | ✅ | | ✅ |
| CU-10 | Ver comandas pendientes en KDS | | | ✅ | |
| CU-11 | Marcar comanda en preparación | | | ✅ | |
| CU-12 | Marcar plato como listo | | | ✅ | ✅ |
| CU-13 | Ver alérgenos y observaciones | | | ✅ | |
| CU-14 | Enviar ticket a caja | ✅ | ✅ | | |
| CU-15 | Editar ticket | ✅ | ✅ | | |
| CU-16 | Reclamar ticket enviado | ✅ | ✅ | | |
| CU-17 | Cobrar ticket en caja | | ✅ | | |
| CU-18 | Ver listado de reservas del día | | ✅ | | |
| CU-19 | Gestionar reservas (Crear, Editar, Cancelar) | | ✅ | | |
| CU-20 | Asignar mesa a reserva | | ✅ | | |
| CU-21 | Gestionar usuarios y roles | | ✅ | | |
| CU-22 | Configurar plano de sala (Zonas y Mesas) | | ✅ | | |
| CU-23 | Unir / Separar mesas | | ✅ | | |
| CU-24 | Consultar log de auditoría | | ✅ | | |
| CU-25 | Gestionar carta (Añadir, Editar, Eliminar platos) | | ✅ | | |
| CU-26 | Configurar Menú del Día | | ✅ | | |
| CU-27 | Enviar notificaciones automáticas (plato listo, reservas próximas, actualizar KDS) | | | | ✅ |
| CU-28 | Marcar plato como agotado / disponible | | ✅ | ✅ | |

### Tabla de Actores y Casos de Uso Negativos

| ID | Caso de uso | Camarero | Administrador | Cocinero | Sist. Notif. |
|---|---|:---:|:---:|:---:|:---:|
| CU-N01 | Acceso denegado por credenciales incorrectas | ✅ | ✅ | ✅ | |
| CU-N02 | Operación bloqueada por rol insuficiente | ✅ | | ✅ | |
| CU-N03 | Comanda rechazada por alérgenos incompletos | ✅ | ✅ | | |
| CU-N04 | Reserva rechazada por conflicto de horario | | ✅ | | |
| CU-N05 | Edición bloqueada de línea en preparación | ✅ | ✅ | | |
| CU-N06 | Ticket rechazado por mesa ya cobrada | ✅ | | | |
| CU-N07 | Restringir pedido por falta de stock | ✅ | | | ✅ |
| CU-N08 | Anular ticket ya cobrado por error de cobro | | ✅ | | |
| CU-N09 | Marcar reserva como no presentado | | ✅ | | |
| CU-N10 | Desbloquear cuenta por intentos fallidos | | ✅ | | |

## Diagrama de Casos de Uso por Actor

### Cocinero
![CDU-Cocinero](/Estudiantes/daniel-puente/Capitulo%202/imagenes/CDU-Cocinero.svg)

### Camarero
![CDU-Camarero](/Estudiantes/daniel-puente/Capitulo%202/imagenes/CDU-Camarero.svg)

### Administrador
![CDU-Admin](/Estudiantes/daniel-puente/Capitulo%202/imagenes/CDU-Admin.svg)

### Sistema de Notificaciones
![CDU-SistNoti](/Estudiantes/daniel-puente/Capitulo%202/imagenes/CDU-NotificacionesAuto.svg)

### Casos de Uso Negativos
![CDU-Negativos](/Estudiantes/daniel-puente/Capitulo%202/imagenes/CDU-Negativos.svg)


## Priorización MoSCoW

| ID | Caso de uso | Prioridad |
|---|---|:---:|
| **CU-01** | Iniciar sesión | 🔴 Must |
| **CU-02** | Cerrar sesión | 🔴 Must |
| **CU-03** | Ver estado de mesas por zona | 🔴 Must |
| **CU-04** | Abrir mesa | 🔴 Must |
| **CU-05** | Cerrar mesa | 🔴 Must |
| **CU-06** | Tomar comanda | 🔴 Must |
| **CU-07** | Ver comanda de una mesa | 🔴 Must |
| **CU-08** | Editar comanda | 🔴 Must |
| **CU-09** | Solicitar segundos platos / postres | 🔴 Must |
| **CU-10** | Ver comandas pendientes en KDS | 🔴 Must |
| **CU-11** | Marcar comanda en preparación | 🔴 Must |
| **CU-12** | Marcar plato como listo | 🔴 Must |
| **CU-13** | Ver alérgenos y observaciones | 🔴 Must |
| **CU-14** | Enviar ticket a caja | 🔴 Must |
| **CU-17** | Cobrar ticket en caja | 🔴 Must |
| **CU-18** | Ver listado de reservas del día | 🔴 Must |
| **CU-19** | Gestionar reservas (Crear, Editar, Cancelar) | 🔴 Must |
| **CU-20** | Asignar mesa a reserva | 🔴 Must |
| **CU-21** | Gestionar usuarios y roles | 🔴 Must |
| **CU-25** | Gestionar carta (Añadir, Editar, Eliminar platos) | 🔴 Must |
| **CU-27** | Enviar notificaciones automáticas | 🔴 Must |
| **CU-N01** | Acceso denegado por credenciales incorrectas | 🔴 Must |
| **CU-N02** | Operación bloqueada por rol insuficiente | 🔴 Must |
| **CU-N03** | Comanda rechazada por alérgenos incompletos | 🔴 Must |
| **CU-N05** | Edición bloqueada de línea en preparación | 🔴 Must |
| **CU-N06** | Ticket rechazado por mesa ya cobrada | 🔴 Must |
| **CU-15** | Editar ticket | 🟠 Should |
| **CU-16** | Reclamar ticket enviado | 🟠 Should |
| **CU-24** | Consultar log de auditoría | 🟠 Should |
| **CU-26** | Configurar Menú del Día | 🟠 Should |
| **CU-28** | Marcar plato como agotado / disponible | 🟠 Should |
| **CU-N04** | Reserva rechazada por conflicto de horario | 🟠 Should |
| **CU-N07** | Restringir pedido por falta de stock | 🟠 Should |
| **CU-N10** | Desbloquear cuenta por intentos fallidos | 🟠 Should |
| **CU-22** | Configurar plano de sala (Zonas y Mesas) | 🟡 Could |
| **CU-23** | Unir / Separar mesas | 🟡 Could |
| **CU-N08** | Anular ticket ya cobrado por error de cobro | 🟡 Could |
| **CU-N09** | Marcar reserva como no presentado | 🟡 Could |

### 🔴 Must have — Alta prioridad

Los 26 casos de uso clasificados como Must cubren el núcleo operativo del sistema. Sin ellos el restaurante no puede funcionar con la aplicación: autenticación, gestión de mesas, comandas, KDS de cocina, tickets, carta, usuarios y gestión completa de reservas son funcionalidades críticas que conforman el MVP del proyecto. Se incluyen también los casos de uso negativos esenciales de seguridad y bloqueo operativo, como el control de acceso por rol o la protección de alérgenos en comanda.

### 🟠 Should have — Media prioridad

Los 8 casos de uso Should son importantes para la calidad del servicio pero no bloquean la operativa básica. Incluyen la edición y reclamación de tickets, la configuración del menú del día, el log de auditoría, la gestión de stock por agotamiento y el desbloqueo de cuentas por intentos fallidos. Se implementarán tras completar los Must.

### 🟡 Could have — Baja prioridad

Los 4 casos de uso Could añaden valor al sistema pero no son críticos para el lanzamiento del MVP. Cubren funcionalidades avanzadas como la configuración del plano de sala, la unión y separación de mesas, la anulación de tickets cobrados por error y el registro de reservas no presentadas. Se abordarán si el tiempo de desarrollo lo permite.

## Detallar Casos de Uso

## Prototipar Casos de Uso

![Prototipo 1](/Estudiantes/daniel-puente/Capitulo%202/imagenes/Prototipos1.jpeg)

![Prototipo 2](/Estudiantes/daniel-puente/Capitulo%202/imagenes/Prototipos2.jpeg)

![Prototipo 3](/Estudiantes/daniel-puente/Capitulo%202/imagenes/Prototipos3.jpeg)

![Prototipo 4](/Estudiantes/daniel-puente/Capitulo%202/imagenes/Prototipos4.jpeg)

## Estructurar Casos de Uso

### Objetivo

El objetivo de este paso es estructurar el modelo de casos de uso del sistema de gestión del Restaurante La Unión, identificando funcionalidades compartidas mediante relaciones de inclusión y comportamientos opcionales o alternativos mediante relaciones de extensión, reduciendo así la redundancia entre casos de uso y mejorando la trazabilidad del modelo.

### Relaciones Include y Extend

| Include | Extend |
|---|---|
| Representa funcionalidad obligatoria y común entre casos de uso | Representa comportamiento opcional o alternativo |
| Reduce la duplicación de especificaciones | Permite modelar flujos alternativos y excepciones |


### Relación `<<include>>`

| Caso de uso principal | Caso de uso incluido | Explicación |
|---|---|---|
| **CU-06 Tomar comanda** | CU-01 Iniciar sesión | Para tomar una comanda el usuario debe estar autenticado en el sistema |
| **CU-08 Editar comanda** | CU-07 Ver comanda de una mesa | Para editar una comanda es necesario consultarla previamente |
| **CU-14 Enviar ticket a caja** | CU-07 Ver comanda de una mesa | El ticket se genera a partir de la comanda activa de la mesa |
| **CU-17 Cobrar ticket en caja** | CU-05 Cerrar mesa | El cobro del ticket implica obligatoriamente el cierre de la mesa |
| **CU-19 Gestionar reservas** | CU-18 Ver listado de reservas del día | La gestión de reservas requiere consultar el listado previo |
| **CU-20 Asignar mesa a reserva** | CU-19 Gestionar reservas | Asignar una mesa es parte del flujo de gestión de reservas |
| **CU-11 Marcar comanda en preparación** | CU-10 Ver comandas pendientes en KDS | El cocinero debe ver las comandas pendientes antes de marcarlas |
| **CU-12 Marcar plato como listo** | CU-11 Marcar comanda en preparación | Un plato solo puede marcarse como listo si está en preparación |
| **CU-09 Solicitar segundos platos** | CU-12 Marcar plato como listo | Solo se pueden solicitar segundos si los primeros están marcados como listos |


#### Relación `<<extend>>`

| Caso de uso principal | Caso de uso extendido | Explicación |
|---|---|---|
| **CU-01 Iniciar sesión** | CU-N01 Acceso denegado por credenciales incorrectas | El acceso se deniega si las credenciales introducidas son incorrectas |
| **CU-01 Iniciar sesión** | CU-N10 Desbloquear cuenta por intentos fallidos | Tras 5 intentos fallidos el sistema bloquea la cuenta |
| **CU-06 Tomar comanda** | CU-N03 Comanda rechazada por alérgenos incompletos | El envío se bloquea si alguna línea tiene alérgenos sin completar |
| **CU-06 Tomar comanda** | CU-N07 Restringir pedido por falta de stock | El sistema impide añadir platos marcados como agotados |
| **CU-08 Editar comanda** | CU-N05 Edición bloqueada de línea en preparación | La edición se bloquea si la línea ya está en preparación o lista |
| **CU-14 Enviar ticket a caja** | CU-N06 Ticket rechazado por mesa ya cobrada | El envío se rechaza si la mesa ya ha sido cobrada y cerrada |
| **CU-19 Gestionar reservas** | CU-N04 Reserva rechazada por conflicto de horario | La reserva se rechaza si existe otra activa en el mismo tramo horario |
| **CU-21 Gestionar usuarios y roles** | CU-N02 Operación bloqueada por rol insuficiente | La operación se bloquea si el usuario no tiene rol de Administrador |