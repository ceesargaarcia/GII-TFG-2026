# Glosario

### Proyecto

Entidad que representa un contexto organizativo o funcional al que pertenece una documentacion.
Permite agrupar documentacion, casos de uso, requisitos funcionales y escenarios de prueba.

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

### Escenario Gherkin

Representacion de un escenario de prueba mediante el formato: Given / And / When / Then.
Es una forma de expresar escenarios de prueba de manera estructurada y verificable.

### Caso de Prueba

Especificacion detallada que define como verificar una funcionalidad concreta del sistema.
Describe las condiciones iniciales, los pasos a ejecutar, los datos de entrada y el resultado esperado.
Se construye a partir de uno o varios escenarios de prueba, casos de uso y requisitos funcionales.

### Actor

Entidad externa que interactua con el sistema para iniciar acciones, recibir resultados o participar en el flujo funcional.
En este capitulo los actores principales son `Ingeniero de QA` y `Kiwi TCMS` como sistema externo participante.

### Sistema Externo

Sistema ajeno al nucleo interno de la solucion que intercambia informacion con ella.
Puede enviar solicitudes, recibir resultados o almacenar artefactos generados.

### Agente de IA

Componente del sistema encargado de realizar tareas automaticas como analisis de documentacion, extraccion de informacion, estructuracion de casos de uso y requisitos funcionales, generacion de escenarios o publicacion de resultados.
Actua como Ingeniero QA en la fase automática.

### Analisis de documentacion

Proceso mediante el cual el sistema interpreta la documentacion para extraer informacion relevante.

### Estructuracion

Proceso mediante el cual el sistema organiza los casos de uso y requisitos funcionales extraidos.

### Generacion de escenarios

Proceso automatico mediante el cual se crean escenarios de prueba a partir de los casos de uso y requisitos funcionales.

### Escenarios Gherkin

Descripción estructurada en lenguaje natural (DSL) que define el comportamiento esperado de una funcionalidad de software, utilizado en BDD (Behavior Driven Development). Sigue una sintaxis (Given-And-When-Then) para precondiciones, acciones y resultados, facilitando la comunicación entre perfiles técnicos y de negocio.

### Revision de escenarios

Proceso en el que el Ingeniero QA analiza los escenarios generados antes de su uso o publicacion.

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

### Publicacion en Kiwi TCMS

Proceso mediante el cual los artefactos generados se envian al sistema externo para su almacenamiento y gestion.

### Trazabilidad

Capacidad de relacionar y seguir el origen y la evolucion de los artefactos del sistema.
Permite conectar proyecto, documentacion, casos de uso, requisitos funcionales, escenarios de prueba y casos de prueba publicados.

### Precondicion

Condicion que debe cumplirse antes de ejecutar un caso de uso o una operacion del sistema.
Define el contexto minimo necesario para que el flujo pueda comenzar.

### Postcondicion

Estado o resultado que debe quedar garantizado tras la ejecucion correcta de un caso de uso.
Describe lo que el sistema deja preparado o registrado al finalizar.

### Caso de Error

Situacion excepcional o alternativa en la que el flujo principal no puede completarse como estaba previsto.
Se utiliza para describir fallos de entrada, validacion, procesamiento o almacenamiento.

### Borrador

Version preliminar de un artefacto generado por el sistema que aun no ha sido aprobada ni publicada.
En el capitulo 2 se utiliza especialmente para los escenarios Gherkin pendientes de revision.

### Feedback

Observacion, correccion o comentario incorporado durante la revision de un artefacto.
Se utiliza para refinar escenarios de prueba antes de su aprobacion o publicacion.

### Versionado

Mecanismo para registrar cambios sucesivos sobre un artefacto manteniendo la referencia a sus distintas versiones.
Permite revisar la evolucion de escenarios y borradores a lo largo del flujo.

### Texto Libre

Entrada documental no necesariamente estructurada en un formato formal como DRF o DDS.
Puede utilizarse como fuente para extraer casos de uso, requisitos funcionales o generar borradores.


### Artefacto

Elemento de informacion gestionado por el sistema dentro del flujo de analisis, generacion y publicacion.
Incluye documentos de entrada, resultados de extraccion, escenarios borrador y casos publicados.

