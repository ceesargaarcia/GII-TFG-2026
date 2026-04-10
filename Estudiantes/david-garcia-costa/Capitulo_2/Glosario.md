# Glosario

### Proyecto

Entidad que representa un contexto organizativo o funcional al que pertenece una documentacion.
Permite agrupar documentacion, casos de uso, requisitos funcionales y escenarios de prueba.

### Sesion

Contexto de trabajo seleccionado o creado desde la interfaz para organizar y continuar la interaccion del usuario con el sistema.
Permite mantener agrupados los artefactos y resultados asociados a una misma actividad.

### Documentacion

Conjunto de informacion que describe el sistema a desarrollar o analizar.
Es el punto de entrada del sistema y contiene los elementos necesarios para el procesamiento.

### DRF (Documento de Requisitos Funcionales)

Tipo de documentacion que describe las funcionalidades que debe cumplir el sistema desde el punto de vista del usuario.

### DDS (Documento de Diseno del Sistema)

Tipo de documentacion que describe la arquitectura, componentes y diseno tecnico del sistema.

### Caso de Uso

Descripcion de una interaccion entre un actor y el sistema para lograr un objetivo concreto.
Define como se utiliza el sistema desde la perspectiva del usuario.

### Requisito Funcional

Especificacion de una funcionalidad que el sistema debe cumplir.
Describe que debe hacer el sistema.

### Requisito No Funcional

Restriccion o propiedad de calidad que el sistema debe cumplir sin describir una funcionalidad concreta.
Incluye aspectos como consistencia, usabilidad, mantenibilidad, extensibilidad o integracion.

### Escenario de Prueba

Descripcion concreta de una situacion que permite verificar el comportamiento del sistema.
Se deriva de uno o varios casos de uso y requisitos funcionales.

### Caso de Prueba

Especificacion detallada que define como verificar una funcionalidad concreta del sistema.
Describe las condiciones iniciales, los pasos a ejecutar, los datos de entrada y el resultado esperado.
Se construye a partir de uno o varios escenarios de prueba, casos de uso y requisitos funcionales, y se deriva de borrador.

### Agente de IA

Componente del sistema encargado de realizar tareas automaticas como analisis de documentacion, extraccion de informacion, estructuracion de casos de uso y requisitos funcionales, generacion de escenarios o publicacion de resultados.
Actua como Ingeniero QA en la fase automática.

### Escenarios Gherkin

Descripción estructurada en lenguaje natural (DSL) que define el comportamiento esperado de una funcionalidad de software, utilizado en BDD (Behavior Driven Development). Sigue una sintaxis (Given-And-When-Then) para precondiciones, acciones y resultados, facilitando la comunicación entre perfiles técnicos y de negocio.

Es una forma de representar escenarios de prueba.

### Project Manager

Responsable de generar o gestionar la documentacion, definir casos de uso, definir requisitos funcionales y supervisar la evolucion general del proyecto.

### Ingeniero QA

Perfil que analiza la documentacion y valida los resultados generados por los agentes de Inteligencia Artificial.
Es el usuario principal del sistema en la fase de testing.

### Kiwi TCMS

Sistema externo de gestion de pruebas.
Recibe, almacena, consulta y permite gestionar los casos de prueba generados por el sistema.

### API de Kiwi TCMS

Interfaz utilizada para enviar, consultar o modificar casos de prueba entre el sistema y Kiwi TCMS.

### Borrador

Version preliminar de un artefacto generado por el sistema que aun no ha sido aprobada ni publicada.
Representa un estado intermedio en el que el contenido puede revisarse, corregirse y enriquecerse con feedback antes de derivar un caso de prueba o descartarse.
En el capitulo 2 se utiliza especialmente para los escenarios Gherkin pendientes de revision.

### Feedback

Observacion, correccion o comentario incorporado durante la revision de un artefacto.
Se utiliza para refinar escenarios de prueba antes de su aprobacion o publicacion.

### Texto Libre

Entrada documental no necesariamente estructurada en un formato formal como DRF o DDS.
Puede utilizarse como fuente para extraer casos de uso, requisitos funcionales o generar borradores.


### Artefacto

Elemento de informacion gestionado por el sistema dentro del flujo de analisis, generacion y publicacion.
Incluye documentos de entrada, resultados de extraccion, escenarios borrador y casos publicados.
