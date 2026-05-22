# Descripción de la solución propuesta

Este capítulo presenta la solución desarrollada desde el punto de vista funcional y de implementación. Se muestra primero el mapa de navegación de la aplicación, diferenciando el flujo manual y el flujo automático basado en agentes de IA. Después se recogen los componentes de código más importantes y, por último, se documentan los casos de uso más representativos mediante las pantallas implementadas.

## Mapa de navegación

El sistema comienza con el acceso del actor a la aplicación. Desde la pantalla inicial se inicia sesión y se crea una sesión de trabajo, que actúa como contenedor de la documentación, los artefactos funcionales y los borradores generados.

![Pantalla de inicio de sesión](Imagenes_MapaNavegacion/IniciarSesion.png)

![Primera pantalla de la aplicación](Imagenes_MapaNavegacion/PrimeraPantalla.png)

Una vez dentro de la aplicación, el actor crea una sesión para comenzar con el flujo de trabajo.

![Creación de sesión](Imagenes_MapaNavegacion/CrearSesion.png)

La documentación funcional es el punto de partida del sistema. Antes de elegir entre el flujo manual o el flujo automático, el actor introduce la documentación de referencia del proyecto.

![Introducción de documentación](Imagenes_MapaNavegacion/IntroducirDocumentacion.png)

Tras introducir la documentación, el sistema permite elegir entre continuar con el flujo manual o traspasar la sesión al flujo de agentes. Si se sigue cualquiera de los dos flujos, no se puede volver atrás dentro de esa misma sesión. Para probar el otro flujo sería necesario abrir una nueva sesión, lo que se plantea como mejora futura.

![Selección de flujo](Imagenes_MapaNavegacion/Flujos.png)

## Flujo manual

En el flujo manual, el ingeniero de QA trabaja directamente con los artefactos funcionales dentro de la aplicación. Puede crear y revisar casos de uso, requisitos funcionales, escenarios Gherkin y borradores de casos de prueba.

![Requisitos funcionales y casos de uso](Imagenes_MapaNavegacion/RFyCU.png)

![Escenarios Gherkin](Imagenes_MapaNavegacion/EscenariosGherkin.png)

![Borrador de caso de prueba](Imagenes_MapaNavegacion/Borrador.png)

![Inicio de Kiwi TCMS](Imagenes_MapaNavegacion/InicioKiwi.png)

![Búsqueda de casos en Kiwi TCMS](Imagenes_MapaNavegacion/BuscarCasos.png)

![Vista de caso manual](Imagenes_MapaNavegacion/VerCasoManual.png)

![Caso de prueba manual](Imagenes_MapaNavegacion/CasoPruebaManual.png)

![Borrador rechazado en flujo manual](Imagenes_MapaNavegacion/BorradorRechazadoManual.png)

## Flujo de agentes

En el flujo automático, la aplicación traspasa la sesión al entorno de agentes de IA. Los agentes utilizan la documentación funcional como entrada, extraen casos de uso y requisitos funcionales, generan escenarios Gherkin y preparan borradores de casos de prueba.

![Flujo automático](Imagenes_MapaNavegacion/FlujoAuto.png)

![Introducción de documentación en flujo automático](Imagenes_MapaNavegacion/IntroDocAuto.png)

![Guardar resultados del flujo automático](Imagenes_MapaNavegacion/GuardarResultadosRFUC.png)

![Guardar borradores](Imagenes_MapaNavegacion/GuardarBorradores.png)

![Requisitos y casos de uso generados por agentes](Imagenes_MapaNavegacion/RFyCUAuto.png)

![Escenario Gherkin generado por agentes](Imagenes_MapaNavegacion/EscenarioGherkinAuto.png)

![Listado de borradores para aceptar](Imagenes_MapaNavegacion/ListarBorradorAceptar.png)

![Aceptar borrador automático](Imagenes_MapaNavegacion/AceptarBorradorAuto.png)

Para ver los casos publicados en Kiwi TCMS, el actor accede con su cuenta y navega a `Buscar > Buscar casos de prueba`.

![Casos de prueba automáticos](Imagenes_MapaNavegacion/CasosPruebaAuto.png)

![Ver casos de prueba en Kiwi TCMS](Imagenes_MapaNavegacion/VerCasosPrueba_Auto.png)

## Detalles más importantes de la parte manual

### Entrada principal del backend

El archivo `app/src/app.js` es el punto donde arranca la aplicación manual. En él se configura Express, la conexión con MongoDB, las sesiones y las rutas principales.

![Código de app.js](CodigoFlujoManual/app.js.png)

### Rutas de la API manual

El archivo `app/src/routes/index.js` funciona como mapa de funcionalidades manuales. Agrupa las rutas relacionadas con requisitos, casos de uso, escenarios, borradores, sesiones y conexión con los agentes.

![Código de index.js](CodigoFlujoManual/index.js.png)

### Servicio de sesiones

El archivo `app/src/services/sessionService.js` controla si una sesión pertenece al flujo manual o al flujo automático. Esta separación es clave para distinguir el trabajo tradicional dentro de la aplicación del traspaso hacia los agentes.

![Código de sessionService.js](CodigoFlujoManual/sessionService.png)

### Creación de escenarios Gherkin

El archivo `app/src/services/gherkinService.js` permite crear escenarios Gherkin manualmente y valida que estén conectados con requisitos y sesiones.

![Código de gherkinService.js](CodigoFlujoManual/gherkinService.png)

### Trazabilidad

El archivo `app/src/services/traceabilityService.js` evita que un escenario apunte a requisitos inexistentes. Esta validación aporta valor real al flujo de QA porque mantiene la coherencia entre documentación, requisitos, casos de uso y escenarios.

![Código de traceabilityService.js](CodigoFlujoManual/traceablityService.png)

### Publicación en Kiwi TCMS

El archivo `app/src/services/manualKiwiService.js` contiene la conexión real con Kiwi TCMS. Su función es convertir el trabajo de la aplicación en un caso de prueba publicado.

![Código de manualKiwiService.js](CodigoFlujoManual/manualKiwiService.js.png)

## Detalles más importantes de los agentes de IA

La arquitectura de agentes está construida alrededor de un orquestador principal. Este recibe la petición del usuario y decide a qué agente especializado debe delegar el trabajo.

![Arquitectura de agentes](../Capitulo_4/DiagramaAgentes/DiagramaAgentes.svg)

### kiwi_orchestrator

Archivo: `agent/my_agent/agent.py`

![Código de agent.py](CodigoFlujoAgentes/agent.py.png)

Es el agente principal del sistema. No realiza todo el trabajo directamente, sino que decide qué flujo debe ejecutarse. Se encarga de detectar si el usuario quiere generar escenarios desde documentación, revisar o publicar borradores, gestionar requisitos o casos de uso, y recuperar documentación de una sesión de la aplicación manual cuando recibe un `source_session_id`.

La herramienta principal que utiliza es `get_kiwimvc_session_docs`, que permite recuperar la documentación guardada en MongoDB desde la aplicación manual. Es importante porque conecta el flujo manual con la parte de agentes, y la documentación es siempre la entrada inicial del proceso.

![Código de get_kiwimvc_session_docs](CodigoFlujoAgentes/get_kiwimvc_session_docs.png)

### draft_pipeline_agent

Archivo: `agent/my_agent/agents/draft_pipeline.py`

![Código de draft_pipeline.py](CodigoFlujoAgentes/draft_pipeline.py.png)

Este agente actúa como un pipeline secuencial. Ejecuta en orden `extract_uc_rf_agent` y `kiwi_gherkin_agent`. No tiene herramientas propias, ya que su función principal es coordinar el flujo automático.

### extract_uc_rf_agent

Archivo: `agent/my_agent/agents/extract_uc_rf.py`

![Código de extract_uc_rf.py](CodigoFlujoAgentes/extract_uc_rf.py.png)

![Código auxiliar de extract_uc_rf.py](CodigoFlujoAgentes/extract_uc_rf2.py.png)

Este agente lee la documentación funcional y extrae casos de uso, requisitos funcionales, relaciones entre casos de uso y requisitos, citas literales y notas cuando falta información o existen contradicciones. Su importancia está en transformar texto libre en una estructura que después puede utilizar el generador Gherkin.

### kiwi_gherkin_agent

Archivo: `agent/my_agent/agents/kiwi_gherkin.py`

![Código de kiwi_gherkin.py](CodigoFlujoAgentes/kiwi_gherkin.py.png)

Este agente recibe el JSON generado por `extract_uc_rf_agent` y realiza tres tareas principales: guarda los casos de uso, guarda los requisitos funcionales y genera escenarios Gherkin como borradores. No publica directamente en Kiwi TCMS, sino que deja los borradores pendientes de aprobación.

La herramienta `save_draft` guarda el escenario Gherkin, los casos de uso y los requisitos funcionales, convirtiéndolos en un borrador de caso de prueba pendiente.

![Código de save_draft](CodigoFlujoAgentes/save_draft.png)

La herramienta `register_documentation_reference` guarda la referencia de la documentación utilizada, lo que permite saber de qué DRF o DDS salió el contenido generado.

![Código de register_documentation_reference](CodigoFlujoAgentes/register_documentation_reference.png)

La herramienta `save_use_case` guarda los casos de uso extraídos.

![Código de save_use_case](CodigoFlujoAgentes/save_use_case.png)

La herramienta `save_functional_requirement` guarda los requisitos funcionales. Este conjunto de herramientas mantiene la trazabilidad entre documento, caso de uso, requisito funcional y escenario Gherkin.

![Código de save_functional_requirement](CodigoFlujoAgentes/save_functional_requirement.png)

### review_publish_agent

Archivo: `agent/my_agent/agents/review_publish.py`

![Código de review_publish.py](CodigoFlujoAgentes/review_publish.py.png)

Es el agente de revisión humana. Permite listar borradores, ver el contenido Gherkin, rechazar borradores, añadir feedback, regenerar versiones y publicar únicamente cuando el actor acepta. De esta forma se introduce una barrera de control humano, ya que la IA no publica automáticamente.

La herramienta `list_drafts` lista los borradores pendientes.

![Código de list_drafts](CodigoFlujoAgentes/list_drafts.png)

La herramienta `get_draft` recupera un borrador concreto.

![Código de get_draft](CodigoFlujoAgentes/get_draft.png)

La herramienta `delete_draft` elimina un borrador rechazado.

![Código de delete_draft](CodigoFlujoAgentes/delete_draft.png)

La herramienta `add_feedback` añade feedback humano al borrador.

![Código de add_feedback](CodigoFlujoAgentes/add_feedback.png)

La herramienta `publish_draft` publica el borrador aprobado y llama internamente a Kiwi TCMS.

![Código de publish_draft](CodigoFlujoAgentes/publish_draft.png)

### requirements_manager_agent

Archivo: `agent/my_agent/agents/requirements_manager.py`

![Código de requirements_manager.py](CodigoFlujoAgentes/requirements_manager_agent.png)

Este agente permite mantener casos de uso, requisitos funcionales y referencias de documentación. Es importante porque no todo tiene que venir del pipeline automático: también se pueden consultar o modificar casos de uso y requisitos ya guardados.

Sus herramientas permiten consultar documentación registrada por proyecto, listar, consultar, crear, actualizar y eliminar casos de uso, y realizar las mismas operaciones sobre requisitos funcionales.

### Publicación en Kiwi

Archivo: `agent/my_agent/tools/kiwi_api.py`

![Código de kiwi_api.py](CodigoFlujoAgentes/kiwi_api.py.png)

Este archivo contiene la integración real con Kiwi TCMS. Recibe el Gherkin aprobado y crea un caso de prueba en Kiwi.

## Casos de uso más relevantes

### UC-03: Introducir documentación funcional

![Introducir documentación funcional](CasosUsoRelevantes/IntroducirDocumentacionFuncional/IntroducirDocumentacionFuncional.png)

![Introducir documentación funcional con detalle](CasosUsoRelevantes/IntroducirDocumentacionFuncional/IntroducirDocumentacionFuncional2.png)

### UC-39: Crear traspaso al flujo automático

![Crear traspaso al flujo automático](CasosUsoRelevantes/CrearTraspasoFlujoAuto/CrearTraspasoFlujoAuto.png)

![Confirmación del traspaso al flujo automático](CasosUsoRelevantes/CrearTraspasoFlujoAuto/CrearTraspasoFlujoAuto2.png)

![Resultado del traspaso al flujo automático](CasosUsoRelevantes/CrearTraspasoFlujoAuto/CrearTraspasoFlujoAuto3.png)

Si el usuario no elige el flujo automático, el trabajo continúa en modo manual.

### UC-12: Crear caso de uso

Parte manual:

![Crear caso de uso manualmente](CasosUsoRelevantes/CrearCasoUso/Manual/CrearCasosUso.png)

Parte de agentes de IA:

![Crear caso de uso con agentes](CasosUsoRelevantes/CrearCasoUso/Auto/CrearCasosUso.png)

### UC-17: Crear requisito funcional

Parte manual:

![Crear requisito funcional manualmente](CasosUsoRelevantes/CrearRF/Manual/CrearRF.png)

Parte de agentes de IA:

![Crear requisito funcional con agentes](CasosUsoRelevantes/CrearRF/Auto/CrearRF.png)

### UC-21: Crear escenario Gherkin

Parte manual:

![Crear escenario Gherkin manualmente](CasosUsoRelevantes/CrearEscenarioGherkin/Manual/CrearEscenarioGherkin.png)

Parte de agentes de IA:

![Crear escenario Gherkin con agentes](CasosUsoRelevantes/CrearEscenarioGherkin/Auto/CrearEscenarioGherkin.png)

### UC-25: Crear borrador de caso de prueba

Parte manual:

![Crear borrador de caso de prueba manualmente](CasosUsoRelevantes/CrearBorradorCasoPrueba/Manual/CrearBorradorCasoPrueba.png)

Parte de agentes de IA:

![Crear borrador de caso de prueba con agentes](CasosUsoRelevantes/CrearBorradorCasoPrueba/Auto/CrearBorradorCasoPrueba.png)

### UC-30: Aceptar y publicar caso de prueba a partir de borrador

Parte manual:

![Aceptar y publicar caso de prueba manualmente](CasosUsoRelevantes/AceptaryPublicarCasoPrueba/Manual/AceptaryPublicarCasoPrueba.png)

Parte de agentes de IA:

![Aceptar y publicar caso de prueba con agentes](CasosUsoRelevantes/AceptaryPublicarCasoPrueba/Auto/AceptaryPublicarCasoPrueba.png)

## Estructura de carpetas

La solución se organiza en dos bloques principales: la aplicación manual y el subsistema de agentes.

![Estructura de carpetas de agent](EstructuraCarpetas/Agent/Agent.png)

![Estructura de carpetas de app](EstructuraCarpetas/App/App.png)

## Plan contratado para la IA

Por motivos de confidencialidad y acuerdos internos de empresa, no se dispone de información detallada sobre el plan exacto contratado con OpenAI ni sobre la facturación.

No obstante, sí fue posible utilizar el entorno empresarial para ejecutar pruebas experimentales y observar determinadas métricas parciales relacionadas con el consumo de modelos LLM mediante API. Además, se pudo observar el consumo personal, que se detalla en el capítulo siguiente.

El sistema desarrollado utiliza modelos de OpenAI bajo una facturación donde el coste depende directamente del número de tokens procesados por los agentes de Inteligencia Artificial.
