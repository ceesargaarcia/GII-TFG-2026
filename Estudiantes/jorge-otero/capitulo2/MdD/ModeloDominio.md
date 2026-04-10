# Modelo de Dominio

## Glosario

| Término            | Descripción                                                                                                                          |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| Cliente            | Usuario externo que envía solicitudes al sistema mediante correo electrónico y recibe respuestas.                                    |
| Técnico            | Usuario interno encargado de analizar las solicitudes, interpretarlas y gestionarlas dentro del sistema.                             |
| Correo             | Medio de comunicación principal utilizado para enviar solicitudes y recibir respuestas. Contiene la información inicial del cliente. |
| Respuesta          | Comunicación generada como resultado del procesamiento de una solicitud, enviada al cliente.                                         |
| Intención          | Resultado del análisis del contenido del correo que identifica el propósito de la solicitud (consulta, incidencia, etc.).            |
| Petición           | Representación estructurada de la solicitud del cliente tras su interpretación, que puede ser gestionada por el sistema.             |
| Aplicación Interna | Sistema o herramienta corporativa donde el técnico consulta o gestiona la información relacionada con las peticiones.                |

## Diagrama de Clases 
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de Clases](./DdC/imagen/DdC.png)|[Ver Código del Diagrama de Clases](./DdC/codigo/DdC.puml)

El diagrama de clases representa el funcionamiento actual del sistema de gestión del buzón. En él, un cliente envía un correo electrónico, que contiene una intención y, en muchos casos, una petición.

El técnico es el encargado de analizar el correo recibido, identificar su contenido e interpretar la petición. A partir de esta, consulta diferentes aplicaciones internas para obtener la información necesaria.

Finalmente, el técnico genera una respuesta, que es enviada al cliente como contestación a su solicitud.

## Diagrama de Objetos
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de Objetos](./DdO/imagen/DdO.png)|[Ver Código del Diagrama de Objetos](./DdO/codigo/DdO.puml)

El diagrama de objetos representa un caso concreto del funcionamiento del sistema. En él, un cliente envía un correo electrónico con una consulta asociada a una petición identificada por un número. Este correo contiene una intención, en este caso de tipo consulta.

El técnico recibe y analiza el correo, interpreta la petición y accede a una aplicación interna (Petter) para obtener la información correspondiente al número indicado. A partir de estos datos, genera una respuesta informando del estado de la petición, que finalmente es enviada al cliente.

## Diagrama de Estados
Se han elaborado diagramas de estados para las clases más importantes del sistema, concretamente Cliente y Técnico, con el objetivo de representar el cliclo de vida dentro del proceso de gestión del buzón.

### Ciclo de Vida del Cliente
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de Estado](./DdE/imagen/Cliente.png)|[Ver Código del Diagrama de Estado del Cliente](./DdE/codigo/Cliente.puml)

El diagrama de estados del Cliente describe un flujo sencillo en el que este pasa de un estado inactivo a enviar un correo y permanecer en espera hasta recibir una respuesta, tras lo cual vuelve a su estado inicial. Este comportamiento refleja la interacción básica del cliente con el sistema.

### Ciclo de Vida del Técnico
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama de Estado](./DdE/imagen/Tecnico.png)|[Ver Código del Diagrama de Estado del Tecnico](./DdE/codigo/Tecnico.puml)

Por otro lado, el diagrama de estados del Técnico representa un proceso más complejo. El técnico comienza en espera de nuevos correos, y cuando recibe uno, lo analiza e interpreta su intención. En función de esta interpretación, decide si es necesario consultar los sistemas internos. En caso afirmativo, realiza consultas a diferentes aplicaciones para obtener la información necesaria. Finalmente, genera y envía una respuesta, regresando al estado inicial a la espera de nuevos correos.

## Requisitos Funcionales
| Categoría              | Requisito                           | Descripción                                                                                                                                                                    |
| ---------------------- | ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Gestión de solicitudes | Enviar solicitud                    | El cliente debe poder generar y enviar una solicitud al sistema tras identificar una necesidad.                                                                                |
| Gestión de solicitudes | Recibir respuesta                   | El sistema debe procesar las solicitudes y proporcionar una respuesta al cliente.                                                                                              |
| Gestión de solicitudes | Completar formulario                | El cliente debe poder aportar información adicional mediante un formulario asociado a una solicitud.                                                                           |
| Gestión de solicitudes | Registrar solicitudes               | El sistema debe registrar todas las solicitudes entrantes, sin necesidad de almacenar el detalle completo de su contenido.                                                     |
| Gestión interna        | Ver solicitudes pendientes          | El sistema debe almacenar la información necesaria para permitir la consulta de solicitudes pendientes, aunque el desarrollo de la vista de datos no forma parte del proyecto. |
| Gestión interna        | Actualizar estado                   | El técnico debe poder marcar como resuelto un formulario o solicitud.                                                                                                          |
| Gestión interna        | Validar existencia de formulario    | El sistema debe comprobar si existe un formulario asociado antes de actualizar el estado de una solicitud.                                                                     |
| Procesamiento          | Procesar solicitudes                | El sistema debe analizar las solicitudes recibidas para generar una respuesta adecuada.                                                                                        |
| Procesamiento          | Identificar intención               | El sistema debe identificar la intención de la solicitud a partir del contenido recibido.                                                                                      |
| Comunicación           | Enviar respuesta                    | El sistema debe enviar la respuesta generada al cliente.                                                                                                                       |
| Comunicación           | Solicitar información adicional     | El sistema debe poder requerir información adicional al cliente cuando la solicitud no sea suficiente.                                                                         |
| Integración de datos   | Consultar bases de datos existentes | El sistema debe ser capaz de consultar la información de las solicitudes en bases de datos ya existentes para su procesamiento y gestión.                                      |



## Requisitos Suplementarios

| Categoría      | Requisito                  | Descripción                                                                                        |
| -------------- | -------------------------- | -------------------------------------------------------------------------------------------------- |
| Rendimiento    | Tiempo de respuesta        | El sistema debe procesar y responder a todas las solicitudes en un tiempo inferior a 10 minutos            |
| Disponibilidad | Disponibilidad del sistema | El sistema debe estar disponible durante el horario laboral o de forma continua según necesidades. |
| Usabilidad     | Facilidad de uso           | La interacción debe ser sencilla para el cliente, sin necesidad de conocimientos técnicos.         |
| Mantenibilidad | Facilidad de modificación  | El sistema debe permitir cambios en reglas, flujos o procesamiento sin grandes modificaciones.     |
| Extensibilidad | Posibilidad de ampliación  | El sistema debe permitir añadir nuevas funcionalidades o tipos de solicitudes en el futuro.        |
| Fiabilidad     | Procesamiento correcto     | El sistema debe minimizar errores en la interpretación de solicitudes.                             |
| Compatibilidad | Integración con correo     | El sistema debe integrarse correctamente con el servicio de correo electrónico utilizado.          |
| Trazabilidad   | Seguimiento de solicitudes | Debe ser posible registrar y consultar el estado de cada solicitud.                                |
