# Análisis y Diseño 

## Introducción

En este capítulo se aborda el análisis y diseño del sistema a partir de los requisitos y casos de uso definidos previamente. En primer lugar, se realiza el análisis mediante la identificación de las clases y sus relaciones siguiendo el patrón arquitectónico Modelo-Vista-Controlador (MVC), así como la elaboración de diagramas de clases y de colaboración. A continuación, se presenta la transición hacia el diseño, donde se concreta la solución tecnológica adoptada y se detallan las clases con un enfoque implementable. Finalmente, se incluyen los diagramas de clases de diseño y los diagramas de secuencia que describen el comportamiento del sistema de forma más precisa.

## Análisis

El sistema se basa en un enfoque de automatización orientada a eventos, en el que la mayor parte de los procesos se ejecutan sin intervención directa del usuario. El sistema gestiona solicitudes recibidas a través del correo electrónico, permite su ampliación mediante formularios y ofrece una capa de visualización para su consulta.

A partir de los diagramas de contexto definidos previamente, se identifican los siguientes actores:

Exchange Online, que actúa como origen del flujo principal al generar eventos cuando se recibe un correo en el buzón corporativo.
Microsoft Forms, que genera eventos cuando un formulario es completado, permitiendo ampliar la información de las solicitudes.
Técnico, que consulta la información procesada a través de una vista en Power BI.

A diferencia de sistemas tradicionales, el cliente no se considera actor directo, ya que su interacción se realiza a través de servicios intermedios (correo y formularios), sin comunicación directa con la solución.

A partir de estas interacciones, se construye el análisis del sistema siguiendo la estructura MVC. Sin embargo, es importante destacar que la aplicación de este patrón presenta particularidades en este proyecto: los casos de uso principales (recepción de solicitudes y formularios) no disponen de interfaz de usuario, por lo que las clases vista no están presentes en estos procesos. En su lugar, la capa de visualización se concentra exclusivamente en el caso de uso de consulta, implementado mediante Power BI.

En consecuencia, el sistema se organiza en torno a una capa de control que gestiona los flujos automatizados, una capa de modelo que representa las entidades del dominio (como solicitudes y detalles asociados), y una capa de vista limitada a la visualización de datos para el técnico.

### Identificación de clases de análisis

| Diagrama | Código Fuente |
|----------|---------------|
|![ModeloVistaControlador](./MVC/imagen/MVC.png)|[Ver Código](./MVC/codigo/MVC.puml)

### Identificación de clases de análisis

### Identificación de clases de análisis

En el paquete de **Vistas** se define la clase *SolicitudesView*, que representa la única interfaz de usuario del sistema. Esta vista está implementada mediante Power BI y permite al técnico consultar las solicitudes procesadas. Dado que el sistema se basa en automatización, no existen otras interfaces de interacción directa con el usuario.

En la capa de **Controladores** se incluyen las clases *RecibirSolicitudController*, *RecibirFormularioController* y *VerSolicitudesController*. Estas clases representan la lógica de control asociada a los distintos casos de uso identificados. A diferencia de sistemas tradicionales, estos controladores no responden a acciones directas del usuario, sino a eventos externos. En concreto, los dos primeros gestionan flujos automáticos activados por servicios externos, mientras que el tercero gestiona la interacción del técnico con la vista de consulta.

En el paquete de **Modelos** se encuentran las clases *Solicitud*, *Formulario* y *CorreoRecibido*, que representan las entidades principales del dominio. La clase *Solicitud* constituye el elemento central del sistema, ya que recoge la información asociada a cada petición recibida. La clase *Formulario* permite almacenar la información adicional proporcionada por el usuario mediante formularios, complementando así los datos de la solicitud. La clase *CorreoRecibido*, que representa un historico de las intenciones de los correos que han entrado al buzón.

### Diagramas de Colaboración

#### Recibir Solicitud

| Diagrama | Código Fuente |
|----------|---------------|
|![EnviarSolicitud](./DdC/imagen/RecibirSolicitud.png)|[Ver Código](./DdC/codigo/RecibirSolicitud.puml)

### Diagrama de colaboración: CA1 Recibir solicitud

En este caso, el actor **Exchange Online** actúa como origen del evento, enviando la solicitud al sistema cuando se recibe un nuevo correo en el buzón corporativo. Este evento es capturado por la clase **RecibirSolicitudController**, que constituye el elemento encargado de gestionar la lógica del proceso.

El controlador actúa como intermediario entre el actor externo y los elementos del modelo. Por un lado, interactúa con la clase **CorreoRecibido**, que representa la información del correo entrante. Esta clase no solo recoge los datos iniciales del mensaje (asunto, remitente, contenido y fecha), sino que además se utiliza para mantener un histórico de las solicitudes recibidas, incluyendo la intención detectada, lo que permite realizar un seguimiento y análisis del volumen de solicitudes por tipo.

Por otro lado, el controlador utiliza la clase **Solicitud** para registrar y gestionar los datos relevantes de la petición dentro del sistema, representando la entidad principal del dominio sobre la que se realiza el procesamiento.

De este modo, se diferencia entre el registro histórico de correos (*CorreoRecibido*) y la entidad operativa del sistema (*Solicitud*), permitiendo tanto el análisis de la información recibida como la gestión de las solicitudes.

Cabe destacar que, a diferencia de otros casos de uso, no existe una clase vista asociada, ya que el proceso se ejecuta de forma automática sin intervención directa del usuario.


#### Recibir Formulario

| Diagrama | Código Fuente |
|----------|---------------|
|![EnviarSolicitud](./DdC/imagen/RecibirFormulario.png)|[Ver Código](./DdC/codigo/RecibirFormulario.puml)


En este caso, el actor **Microsoft Forms** actúa como origen del evento, enviando los datos al sistema cuando un formulario es completado. Este evento es recibido por la clase **RecibirFormularioController**, que se encarga de gestionar el proceso asociado.

El controlador actúa como intermediario entre el actor externo y el modelo, delegando en la clase **Formulario** la gestión de la información recibida. Esta clase representa los datos introducidos en el formulario y permite su almacenamiento dentro del sistema.

Al igual que en el caso de uso anterior, no existe una clase vista asociada, ya que el proceso se ejecuta automáticamente sin interacción directa del usuario.

#### Ver Solicitudes

| Diagrama | Código Fuente |
|----------|---------------|
|![EnviarSolicitud](./DdC/imagen/VerSolicitudes.png)|[Ver Código](./DdC/codigo/VerSolicitudes.puml)

### Diagrama de colaboración: CA3 Ver solicitudes

En este caso, el actor **Técnico** interactúa directamente con la clase *SolicitudesView*, que representa la vista implementada en Power BI. Esta vista permite acceder a la información almacenada en el sistema de forma estructurada.

A diferencia de otros casos de uso, no existe una clase controladora intermedia, ya que la herramienta de visualización accede directamente a los datos. De este modo, la vista se conecta directamente con las clases del modelo *Solicitud*, *Formulario* y *CorreoRecibido*, que contienen la información necesaria para su representación.

La clase *Solicitud* recoge los datos principales asociados a cada petición y constituye la entidad central del sistema. La clase *Formulario* contiene la información adicional proporcionada por el usuario mediante formularios, complementando los datos de la solicitud. Por su parte, la clase *CorreoRecibido* permite disponer de un histórico de los correos procesados, incluyendo la intención detectada, lo que posibilita el análisis del volumen y tipología de solicitudes recibidas.

De este modo, la vista integra información operativa y analítica, permitiendo al técnico no solo consultar el estado de las solicitudes, sino también obtener una visión global del comportamiento del sistema.

## Decisión Tecnológica

| Diagrama | Código Fuente |
|----------|---------------|
|![Decision_Tecnologica](./DecisionTecnologica/imagen/Decision_Tecnologica.png)|[Ver Código](./DecisionTecnologica/codigo/Decision_Tecnologica.puml)

El sistema se estructura en torno a una capa de control implementada mediante Power Automate, donde se definen dos flujos principales: **RecibirSolicitudController** y **RecibirFormularioController**. Estos flujos se activan de forma automática a partir de eventos externos y constituyen el núcleo de la lógica del sistema.

Por un lado, **Exchange Online** actúa como punto de entrada para las solicitudes. Cuando se recibe un nuevo correo en el buzón corporativo, se activa el flujo *RecibirSolicitudController*, que procesa la información, consulta en caso necesario los **servicios de información externos** (como APIs o workflows auxiliares) y registra los datos relevantes en el **repositorio de datos**. Además, el sistema genera y envía una respuesta al remitente a través del propio servicio de correo.

Por otro lado, **Microsoft Forms** permite la recogida de información adicional mediante formularios. Cuando un formulario es completado, se activa el flujo *RecibirFormularioController*, que procesa los datos recibidos, puede enviar un correo adicional si procede y registra la información en el repositorio de datos.

El **repositorio de datos**, implementado como dataset de Power BI, actúa como almacenamiento central del sistema, donde se guardan las entidades principales, como solicitudes y formularios.

Finalmente, **Power BI** proporciona la capa de visualización a través de la vista *SolicitudesView*, permitiendo al técnico consultar la información almacenada. El técnico interactúa directamente con esta herramienta, que accede al repositorio de datos sin necesidad de una capa intermedia.

De este modo, la arquitectura separa claramente la captura de eventos, el procesamiento automático, la integración con servicios externos, el almacenamiento de la información y su posterior visualización, garantizando una solución modular y coherente con un sistema orientado a eventos.

## Diseño 

### Diagrama de Clases de Diseño
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama Clases Diseño](./DdC_Diseno/imagen/Diagrama_Clases_Diseno.png)|[Ver Código](./DdC_Diseno/codigo/Diagrama_Clases_Diseno.puml)

En el paquete de **Interfaces** se incluyen las clases que representan los puntos de entrada y salida del sistema. La clase *CorreoExchangeOnline* modela la información recibida desde el buzón corporativo, mientras que *FormularioMicrosoftForms* representa los datos recogidos a través de formularios. Por su parte, *SolicitudesViewPowerBI* actúa como la capa de visualización, permitiendo consultar la información almacenada en el sistema.

En la capa de **Controladores** se definen las clases *RecibirSolicitudController* y *RecibirFormularioController*, encargadas de gestionar la lógica de negocio. Estas clases se activan a partir de eventos externos y coordinan el procesamiento de la información, delegando en los modelos y servicios necesarios para completar cada flujo.

El paquete de **Modelos** contiene las entidades principales del sistema. La clase *Solicitud* representa la información operativa asociada a cada petición, incluyendo datos relevantes para su análisis y gestión. La clase *Formulario* recoge la información adicional proporcionada por el usuario, vinculada a una solicitud concreta. Por otro lado, la clase *CorreoRecibido* mantiene un histórico independiente de los correos procesados, almacenando información como la fecha de recepción y la intención detectada, lo que permite realizar análisis estadísticos del volumen y tipología de solicitudes.

En el paquete de **Servicios Externos** se agrupan las clases que representan las integraciones con sistemas externos. *ExchangeOnlineService* gestiona la interacción con el correo electrónico, *MicrosoftFormsService* permite acceder a los datos de los formularios, y *BaseDatos* se utiliza para la consulta de información adicional necesaria durante el procesamiento. Finalmente, *RepositorioDatos* actúa como capa de persistencia, centralizando el almacenamiento y recuperación de las entidades del sistema.

Las relaciones entre clases reflejan el flujo de información: los controladores reciben datos de las interfaces, interactúan con los modelos y utilizan los servicios externos para completar el procesamiento. La vista en Power BI accede directamente al repositorio de datos, sin necesidad de un controlador intermedio, lo que simplifica la arquitectura en la capa de consulta.

### Diagramas de Secuencia por Caso de Uso 

#### Recibir Solicitud
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama Secuencia](./DdS/imagen/RecibirSolicitud.png)|[Ver Código](./DdS/codigo/RecibirSolicitud.puml)

El proceso se inicia cuando **Exchange Online** detecta la llegada de un nuevo correo en el buzón corporativo y notifica al **RecibirSolicitudController**, que actúa como elemento central de control del sistema.

En una primera fase, el controlador evalúa el asunto del correo para identificar si corresponde a alguno de los casos predefinidos. Si el asunto indica que un formulario ha sido resuelto, el sistema actualiza su estado. En los casos de envío de documentación, el correo es clasificado y movido a la carpeta correspondiente, finalizando el flujo sin procesamiento adicional.

Si el asunto no coincide con estos casos, el sistema entra en una segunda fase de análisis, en la que se procesa el contenido del correo para identificar la intención de la solicitud. A continuación, se crea un registro en la entidad **CorreoRecibido**, que se almacena en el repositorio de datos, permitiendo mantener un histórico de las solicitudes y facilitar su análisis posterior.

En función de la intención detectada, el flujo se bifurca. Si se trata de una consulta, reclamación o situación de riesgo, el sistema realiza una consulta a la **Base de Datos** para obtener la información necesaria. Con los datos recuperados, se genera una respuesta específica y se crea una entidad **Solicitud**, que se almacena en el repositorio. Finalmente, se envía la respuesta al remitente a través de Exchange Online.

En aquellos casos en los que la intención no corresponde con los escenarios contemplados, el sistema genera una respuesta basada en una plantilla predefinida y la envía directamente, sin necesidad de consultar datos adicionales ni registrar una solicitud.

#### Recibir Formulario
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama Secuencia](./DdS/imagen/RecibirFormulario.png)|[Ver Código](./DdS/codigo/RecibirFormulario.puml)

El flujo se inicia cuando **Microsoft Forms** detecta que un formulario ha sido completado y activa el **RecibirFormularioController**, encargado de gestionar el procesamiento de los datos recibidos.

A continuación, el controlador crea la entidad **Formulario** con la información introducida por el usuario. Después, evalúa si se ha marcado la opción relacionada con el envío de documentación.

Si la opción de documentación está marcada, el sistema envía un correo electrónico con las instrucciones necesarias para remitir la documentación correspondiente a través de **Exchange Online**. Si no está marcada, el flujo continúa sin realizar este envío adicional.

Finalmente, independientemente de la opción seleccionada, el sistema guarda la información del formulario en el **RepositorioDatos (Power BI)**, dejando los datos disponibles para su posterior consulta desde la vista de solicitudes.

#### Ver Solicitudes Pendientes
| Diagrama | Código Fuente |
|----------|---------------|
|![Diagrama Secuencia](./DdS/imagen/VerSolicitudes.png)|[Ver Código](./DdS/codigo/VerSolicitudes.puml)

El flujo se inicia cuando el **Técnico** accede a la vista de solicitudes, implementada en Power BI. Esta acción provoca que la vista realice una petición al **Repositorio de Datos**, donde se almacena toda la información generada por el sistema.

El repositorio devuelve los datos correspondientes a las distintas entidades del sistema, incluyendo solicitudes, formularios y el histórico de correos recibidos. A continuación, la vista procesa esta información, mapeando los datos a las entidades **Solicitud**, **Formulario** y **CorreoRecibido** para su correcta interpretación.

Finalmente, la vista presenta la información al técnico de forma estructurada, permitiendo su consulta y análisis. Este proceso se realiza sin intervención de una capa de control intermedia, ya que Power BI accede directamente a los datos almacenados.

De este modo, el diagrama refleja un flujo simple centrado en la recuperación y visualización de la información, diferenciándose de los otros casos de uso por la ausencia de procesamiento complejo y por su carácter exclusivamente consultivo.
