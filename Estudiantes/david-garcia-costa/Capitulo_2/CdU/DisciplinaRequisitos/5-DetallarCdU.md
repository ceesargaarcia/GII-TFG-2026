| [<- PriorizarCdU](./4-PriorizarCdU.md) | [-> Requisitos](./1-Requisitos.md) |

# Detalle de casos de uso

En esta seccion se detallan los casos de uso del sistema desde el punto de vista de requisitos. Los flujos describen lo que debe ocurrir funcionalmente, sin entrar en decisiones tecnicas de implementacion.

---

## CU01. Iniciar sesion
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite autenticar al usuario para acceder al sistema.
- **Precondiciones:** El usuario dispone de una cuenta registrada.
- **Flujo principal:** 1. El sistema muestra el formulario de acceso. 2. El actor introduce usuario y contrasena. 3. El sistema valida las credenciales. 4. El sistema abre la sesion de trabajo del usuario.
- **Casos de Error:** Credenciales invalidas o usuario no registrado.
- **Postcondiciones:** El usuario queda autenticado.
- **Diagrama:** ![Iniciar sesion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/IniciarSesion/IniciarSesion.svg)

---

## CU02. Cerrar sesion
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite finalizar la sesion activa.
- **Precondiciones:** Existe una sesion iniciada.
- **Flujo principal:** 1. El actor solicita cerrar sesion. 2. El sistema invalida la sesion activa. 3. El sistema vuelve a mostrar la pantalla de acceso.
- **Casos de Error:** No existe sesion activa o se produce un fallo al cerrarla.
- **Postcondiciones:** La sesion queda cerrada.
- **Diagrama:** ![Cerrar sesion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CerrarSesion/CerrarSesion.svg)

---

## CU03. Introducir documentacion funcional
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite introducir documentacion funcional en texto y clasificarla como DRF o DDS.
- **Precondiciones:** El actor ha iniciado sesion y existe una sesion de trabajo.
- **Flujo principal:** 1. El actor introduce proyecto, tipo de documento, etiqueta, titulo y contenido. 2. El sistema valida los datos obligatorios. 3. El sistema registra la documentacion en la sesion. 4. El sistema deja la documentacion disponible para el flujo manual o automatico.
- **Casos de Error:** Proyecto vacio, contenido vacio, tipo de documento no valido o fallo de almacenamiento.
- **Postcondiciones:** La documentacion queda disponible para consulta y procesamiento.
- **Diagrama:** ![Introducir documentacion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/IntroducirDocumentacion/IntroducirDocumentacion.svg)

---

## CU04. Asociar documentacion a proyecto
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite vincular documentacion registrada a un proyecto concreto.
- **Precondiciones:** Existe documentacion registrada o se esta registrando una nueva documentacion.
- **Flujo principal:** 1. El actor indica el proyecto de referencia. 2. El sistema valida que el proyecto este informado. 3. El sistema guarda la relacion entre documentacion y proyecto. 4. El sistema usa esa relacion en consultas y trazabilidad.
- **Casos de Error:** Proyecto vacio o fallo al guardar la asociacion.
- **Postcondiciones:** La documentacion queda vinculada a un proyecto.
- **Diagrama:** ![Asociar documentacion a proyecto](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/AsociarDocumentacionAProyecto/AsociarDocumentacionAProyecto.svg)

---

## CU05. Consultar referencias de documentacion de proyecto
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite consultar las referencias documentales asociadas a uno o varios proyectos.
- **Precondiciones:** Existe documentacion asociada a proyectos.
- **Flujo principal:** 1. El actor solicita consultar referencias documentales. 2. El sistema recupera los proyectos disponibles. 3. El actor puede filtrar por proyecto. 4. El sistema muestra las referencias de documentacion disponibles.
- **Casos de Error:** No hay documentacion registrada o falla la recuperacion.
- **Postcondiciones:** El actor visualiza referencias documentales trazables a proyecto.
- **Diagrama:** ![Consultar referencias de documentacion de proyecto](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ConsultarReferenciasDocumentacionProyecto/ConsultarReferenciasDocumentacionProyecto.svg)

---

## CU06. Listar documentacion
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite visualizar la documentacion funcional registrada.
- **Precondiciones:** El actor ha iniciado sesion.
- **Flujo principal:** 1. El actor solicita el listado. 2. El sistema recupera los documentos disponibles para el usuario. 3. El sistema muestra proyecto, tipo, etiqueta, titulo, sesion y fecha.
- **Casos de Error:** No existe documentacion registrada o falla la consulta.
- **Postcondiciones:** El actor dispone de una vista general de la documentacion.
- **Diagrama:** ![Listar documentacion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ListarDocumentacion/ListarDocumentacion.svg)

---

## CU07. Consultar documentacion
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite consultar el detalle de un documento funcional.
- **Precondiciones:** Existe documentacion registrada.
- **Flujo principal:** 1. El actor selecciona un documento. 2. El sistema recupera sus metadatos y contenido. 3. El sistema muestra la informacion detallada.
- **Casos de Error:** Documento no encontrado o fallo de recuperacion.
- **Postcondiciones:** El actor visualiza el documento seleccionado.
- **Diagrama:** ![Consultar documentacion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ConsultarDocumentacion/ConsultarDocumentacion.svg)

---

## CU08. Actualizar documentacion
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite modificar la informacion de un documento registrado.
- **Precondiciones:** Existe documentacion registrada y editable.
- **Flujo principal:** 1. El actor selecciona un documento. 2. El sistema muestra sus datos actuales. 3. El actor modifica metadatos o contenido. 4. El sistema valida y guarda los cambios.
- **Casos de Error:** Documento no encontrado, datos obligatorios vacios o cambios invalidos.
- **Postcondiciones:** La documentacion queda actualizada.
- **Diagrama:** ![Actualizar documentacion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ActualizarDocumentacion/ActualizarDocumentacion.svg)

---

## CU09. Eliminar documentacion
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite retirar documentacion registrada.
- **Precondiciones:** Existe documentacion registrada.
- **Flujo principal:** 1. El actor solicita eliminar un documento. 2. El sistema solicita confirmacion. 3. El actor confirma. 4. El sistema elimina la documentacion.
- **Casos de Error:** Documento inexistente o fallo de eliminacion.
- **Postcondiciones:** La documentacion deja de estar disponible.
- **Diagrama:** ![Eliminar documentacion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/EliminarDocumentacion/EliminarDocumentacion.svg)

---

## CU10. Listar casos de uso
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite visualizar los casos de uso locales registrados.
- **Precondiciones:** El actor ha iniciado sesion.
- **Flujo principal:** 1. El actor accede a la seccion de casos de uso. 2. El sistema recupera los casos disponibles. 3. El sistema muestra identificador, nombre y datos principales.
- **Casos de Error:** No existen casos de uso o falla la consulta.
- **Postcondiciones:** El actor dispone de una vista general de casos de uso.
- **Diagrama:** ![Listar casos de uso](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ListarCdU/ListarCdU.svg)

---

## CU11. Consultar caso de uso
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite consultar el detalle de un caso de uso concreto.
- **Precondiciones:** Existe al menos un caso de uso registrado.
- **Flujo principal:** 1. El actor selecciona un caso de uso. 2. El sistema recupera sus datos. 3. El sistema muestra identificador, nombre, descripcion, actores, condiciones y flujos.
- **Casos de Error:** Caso de uso inexistente o error de acceso.
- **Postcondiciones:** El actor visualiza el caso de uso seleccionado.
- **Diagrama:** ![Consultar caso de uso](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ConsultarCdU/ConsultarCdU.svg)

---

## CU12. Crear caso de uso
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite crear manualmente un caso de uso local.
- **Precondiciones:** El actor ha iniciado sesion y la sesion permite flujo manual.
- **Flujo principal:** 1. El actor abre el formulario. 2. Introduce identificador, nombre, descripcion, actores, condiciones y flujos. 3. El sistema valida los datos. 4. El sistema registra el caso de uso y actualiza la trazabilidad de la sesion.
- **Casos de Error:** Identificador vacio, nombre vacio, sesion inexistente o sesion en modo automatico.
- **Postcondiciones:** Se crea un nuevo caso de uso local.
- **Diagrama:** ![Crear caso de uso](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CrearCdU/CrearCdU.svg)

---

## CU13. Actualizar caso de uso
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite modificar un caso de uso existente.
- **Precondiciones:** Existe un caso de uso registrado y la sesion permite flujo manual.
- **Flujo principal:** 1. El actor selecciona el caso de uso. 2. El sistema muestra su informacion actual. 3. El actor modifica el contenido. 4. El sistema valida, guarda e incrementa la version del caso.
- **Casos de Error:** Caso de uso no encontrado, datos invalidos o sesion en modo automatico.
- **Postcondiciones:** El caso de uso queda actualizado.
- **Diagrama:** ![Actualizar caso de uso](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ActualizarCdU/ActualizarCdU.svg)

---

## CU14. Eliminar caso de uso
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite eliminar un caso de uso local.
- **Precondiciones:** Existe un caso de uso registrado.
- **Flujo principal:** 1. El actor solicita eliminar el caso. 2. El sistema solicita confirmacion. 3. El actor confirma. 4. El sistema elimina el caso y actualiza la sesion.
- **Casos de Error:** Caso inexistente o fallo de eliminacion.
- **Postcondiciones:** El caso de uso deja de estar disponible.
- **Diagrama:** ![Eliminar caso de uso](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/EliminarCdU/EliminarCdU.svg)

---

## CU15. Listar requisitos funcionales
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite visualizar requisitos funcionales locales.
- **Precondiciones:** El actor ha iniciado sesion.
- **Flujo principal:** 1. El actor accede a la seccion de requisitos. 2. El sistema recupera los requisitos disponibles. 3. El sistema muestra identificador, texto, prioridad y caso de uso asociado.
- **Casos de Error:** No existen requisitos o falla la consulta.
- **Postcondiciones:** El actor dispone de una vista general de requisitos funcionales.
- **Diagrama:** ![Listar requisitos funcionales](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ListarRequisitosFuncionales/ListarRequisitosFuncionales.svg)

---

## CU16. Consultar requisito funcional
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite consultar el detalle de un requisito funcional.
- **Precondiciones:** Existe al menos un requisito funcional registrado.
- **Flujo principal:** 1. El actor selecciona un requisito. 2. El sistema recupera sus datos. 3. El sistema muestra identificador, texto, prioridad, caso de uso asociado, cita fuente y notas.
- **Casos de Error:** Requisito no encontrado o error de acceso.
- **Postcondiciones:** El actor visualiza el requisito seleccionado.
- **Diagrama:** ![Consultar requisito funcional](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ConsultarRequisitosFuncionales/ConsultarRequisitosFuncionales.svg)

---

## CU17. Crear requisito funcional
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite crear manualmente un requisito funcional local.
- **Precondiciones:** El actor ha iniciado sesion y la sesion permite flujo manual.
- **Flujo principal:** 1. El actor abre el formulario. 2. Introduce identificador, texto, prioridad, caso de uso asociado, cita fuente y notas. 3. El sistema valida los datos y la trazabilidad. 4. El sistema registra el requisito.
- **Casos de Error:** Identificador vacio, texto vacio, caso de uso asociado inexistente o sesion en modo automatico.
- **Postcondiciones:** Se crea un nuevo requisito funcional.
- **Diagrama:** ![Crear requisito funcional](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CrearRequisitosFuncionales/CrearRequisitosFuncionales.svg)

---

## CU18. Actualizar requisito funcional
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite modificar un requisito funcional existente.
- **Precondiciones:** Existe un requisito funcional registrado y la sesion permite flujo manual.
- **Flujo principal:** 1. El actor selecciona el requisito. 2. El sistema muestra su informacion actual. 3. El actor modifica los datos. 4. El sistema valida, guarda e incrementa la version.
- **Casos de Error:** Requisito no encontrado, caso de uso asociado inexistente o sesion en modo automatico.
- **Postcondiciones:** El requisito funcional queda actualizado.
- **Diagrama:** ![Actualizar requisito funcional](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ActualizarRequisitoFuncional/ActualizarRequisitoFuncional.svg)

---

## CU19. Eliminar requisito funcional
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite eliminar un requisito funcional local.
- **Precondiciones:** Existe un requisito funcional registrado.
- **Flujo principal:** 1. El actor solicita eliminar el requisito. 2. El sistema solicita confirmacion. 3. El actor confirma. 4. El sistema elimina el requisito y actualiza la sesion.
- **Casos de Error:** Requisito inexistente o fallo de eliminacion.
- **Postcondiciones:** El requisito funcional deja de estar disponible.
- **Diagrama:** ![Eliminar requisito funcional](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/EliminarRequisitoFuncional/EliminarRequisitoFuncional.svg)

---

## CU20. Listar escenarios Gherkin
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite visualizar escenarios Gherkin registrados.
- **Precondiciones:** El actor ha iniciado sesion.
- **Flujo principal:** 1. El actor accede a la seccion de escenarios. 2. El sistema recupera los escenarios disponibles. 3. El sistema muestra titulo, feature, etiquetas, trazabilidad y pasos.
- **Casos de Error:** No existen escenarios o falla la consulta.
- **Postcondiciones:** El actor dispone de una vista general de escenarios Gherkin.
- **Diagrama:** ![Listar escenarios Gherkin](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ListarEscenariosGherkin/ListarEscenariosGherkin.svg)

---

## CU21. Crear escenario Gherkin
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite crear manualmente un escenario Gherkin asociado a artefactos funcionales.
- **Precondiciones:** La sesion permite flujo manual.
- **Flujo principal:** 1. El actor abre el formulario de escenario. 2. Introduce titulo, feature, etiquetas, caso de uso, requisitos asociados, background, pasos y ejemplos. 3. El sistema valida datos y trazabilidad. 4. El sistema registra el escenario.
- **Casos de Error:** Titulo vacio, pasos vacios, referencias inexistentes o sesion en modo automatico.
- **Postcondiciones:** Se crea un nuevo escenario Gherkin.
- **Diagrama:** ![Crear escenario Gherkin](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CrearEscenarioGherkin/CrearEscenarioGherkin.svg)

---

## CU22. Consultar escenario Gherkin
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite consultar el detalle de un escenario Gherkin.
- **Precondiciones:** Existe al menos un escenario Gherkin registrado.
- **Flujo principal:** 1. El actor selecciona un escenario. 2. El sistema recupera su detalle. 3. El sistema muestra la informacion estructurada y el texto Gherkin.
- **Casos de Error:** Escenario no encontrado o error de acceso.
- **Postcondiciones:** El actor visualiza el escenario seleccionado.
- **Diagrama:** ![Consultar escenario Gherkin](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ConsultarEscenarioGherkin/ConsultarEscenarioGherkin.svg)

---

## CU23. Actualizar escenario Gherkin
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite modificar un escenario Gherkin existente.
- **Precondiciones:** Existe un escenario Gherkin y la sesion permite flujo manual.
- **Flujo principal:** 1. El actor selecciona el escenario. 2. El sistema muestra su informacion actual. 3. El actor modifica datos y pasos. 4. El sistema valida y guarda los cambios.
- **Casos de Error:** Escenario no encontrado, pasos invalidos o referencias inexistentes.
- **Postcondiciones:** El escenario queda actualizado.
- **Diagrama:** ![Actualizar escenario Gherkin](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ActualizarEscenarioGherkin/ActualizarEscenarioGherkin.svg)

---

## CU24. Eliminar escenario Gherkin
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite eliminar un escenario Gherkin local.
- **Precondiciones:** Existe un escenario registrado.
- **Flujo principal:** 1. El actor solicita eliminar el escenario. 2. El sistema solicita confirmacion. 3. El actor confirma. 4. El sistema elimina el escenario.
- **Casos de Error:** Escenario inexistente o fallo de eliminacion.
- **Postcondiciones:** El escenario deja de estar disponible.
- **Diagrama:** ![Eliminar escenario Gherkin](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/EliminarEscenarioGherkin/EliminarEscenarioGherkin.svg)

---

## CU25. Crear borrador de caso de prueba
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite construir un borrador de caso de prueba a partir de los artefactos disponibles.
- **Precondiciones:** Existe una sesion manual con artefactos funcionales o escenarios suficientes.
- **Flujo principal:** 1. El actor solicita crear o ensamblar un borrador. 2. Indica resumen, categoria y prioridad. 3. El sistema recopila documentacion, casos de uso, requisitos y escenarios asociados. 4. El sistema construye y registra el borrador.
- **Casos de Error:** Sesion no encontrada, sesion en modo automatico o fallo al guardar el borrador.
- **Postcondiciones:** Se crea o actualiza un borrador pendiente de revision.
- **Diagrama:** ![Crear borrador de caso de prueba](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CrearBorradorCasoPrueba/CrearBorradorCasoPrueba.svg)

---

## CU26. Listar borradores
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite visualizar los borradores disponibles.
- **Precondiciones:** El actor ha iniciado sesion.
- **Flujo principal:** 1. El actor solicita el listado. 2. El sistema recupera borradores asociados al usuario o a la sesion. 3. El sistema muestra resumen, proyecto, sesion, estado y version.
- **Casos de Error:** No existen borradores o falla la consulta.
- **Postcondiciones:** El actor dispone de una vista general de borradores.
- **Diagrama:** ![Listar borradores](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ListarBorrador/ListarBorrador.svg)

---

## CU27. Consultar borrador
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite consultar el contenido y estado de un borrador.
- **Precondiciones:** Existe al menos un borrador registrado.
- **Flujo principal:** 1. El actor selecciona un borrador. 2. El sistema recupera su contenido. 3. El sistema muestra contenido, prioridad, version, estado, resultado de publicacion y feedback.
- **Casos de Error:** Borrador inexistente o error de acceso.
- **Postcondiciones:** El actor visualiza el borrador seleccionado.
- **Diagrama:** ![Consultar borrador](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ConsultarBorrador/ConsultarBorrador.svg)

---

## CU28. Anadir feedback a borrador
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite incorporar observaciones a un borrador y controlar si quedan resueltas.
- **Precondiciones:** Existe un borrador disponible para revision.
- **Flujo principal:** 1. El actor escribe una observacion. 2. El sistema valida que el feedback no este vacio. 3. El sistema registra la observacion y actualiza el borrador. 4. El actor puede marcar una observacion como resuelta o pendiente.
- **Casos de Error:** Borrador no encontrado, feedback vacio o sesion en modo automatico.
- **Postcondiciones:** El borrador conserva el feedback y su estado de revision.
- **Diagrama:** ![Anadir feedback a borrador](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/AñadirFeedbackBorrador/AñadirFeedbackBorrador.svg)

---

## CU29. Rechazar borrador
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite rechazar un borrador cuando no resulta adecuado.
- **Precondiciones:** Existe un borrador disponible para revision.
- **Flujo principal:** 1. El actor solicita rechazar el borrador. 2. El sistema actualiza su estado a rechazado. 3. El sistema impide su publicacion mientras permanezca rechazado.
- **Casos de Error:** Borrador no encontrado o sesion en modo automatico.
- **Postcondiciones:** El borrador queda marcado como rechazado.
- **Diagrama:** ![Rechazar borrador](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/RechazarBorrador/RechazarBorrador.svg)

---

## CU30. Aceptar y publicar caso de prueba a partir de borrador
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite aceptar un borrador y publicarlo como caso de prueba en Kiwi TCMS.
- **Precondiciones:** Existe un borrador con contenido y la integracion con Kiwi TCMS esta disponible.
- **Flujo principal:** 1. El actor revisa el borrador. 2. El sistema confirma la publicacion. 3. El sistema envia el contenido del borrador a Kiwi TCMS. 4. Kiwi TCMS registra el caso. 5. El sistema guarda el identificador devuelto y marca el borrador como publicado.
- **Casos de Error:** Borrador no encontrado, borrador sin contenido, fallo de integracion o error de publicacion.
- **Postcondiciones:** El caso de prueba queda publicado y el borrador queda marcado como publicado.
- **Diagrama:** ![Aceptar y publicar caso de prueba a partir de borrador](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/AceptarYPublicarCasoPruebaBorrador/AceptarYPublicarCasoPruebaBorrador.svg)

---

## CU31. Buscar casos de prueba en Kiwi TCMS
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite buscar casos de prueba registrados en Kiwi TCMS.
- **Precondiciones:** La integracion con Kiwi TCMS esta disponible.
- **Flujo principal:** 1. El actor introduce criterios de busqueda. 2. El sistema envia la consulta a Kiwi TCMS. 3. Kiwi TCMS devuelve coincidencias. 4. El sistema muestra los casos encontrados.
- **Casos de Error:** Sin resultados, criterios invalidos o error de conexion.
- **Postcondiciones:** El actor obtiene resultados de busqueda en Kiwi TCMS.
- **Diagrama:** ![Buscar casos de prueba en Kiwi TCMS](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/BuscarCasoPruebaKiwi/BuscarCasoPruebaKiwi.svg)

---

## CU32. Ver caso de prueba en Kiwi TCMS
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite consultar el detalle de un caso de prueba almacenado en Kiwi TCMS.
- **Precondiciones:** Existe al menos un caso de prueba accesible en Kiwi TCMS.
- **Flujo principal:** 1. El actor selecciona un caso de prueba. 2. El sistema solicita el detalle a Kiwi TCMS. 3. Kiwi TCMS devuelve la informacion del caso. 4. El sistema muestra el detalle obtenido.
- **Casos de Error:** Caso no encontrado, permisos insuficientes o error de consulta externa.
- **Postcondiciones:** El actor visualiza el detalle del caso de prueba.
- **Diagrama:** ![Ver caso de prueba en Kiwi TCMS](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/VerCasoPruebaKiwi/VerCasoPruebaKiwi.svg)

---

## CU33. Seleccionar sesiones
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite seleccionar una sesion de trabajo existente.
- **Precondiciones:** Existen sesiones registradas y accesibles para el actor.
- **Flujo principal:** 1. El sistema muestra sesiones disponibles. 2. El actor selecciona una sesion. 3. El sistema recupera su contexto. 4. El sistema activa la sesion seleccionada.
- **Casos de Error:** Sesion no encontrada o error de recuperacion.
- **Postcondiciones:** La sesion seleccionada queda activa.
- **Diagrama:** ![Seleccionar sesiones](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/SeleccionarSesiones/SeleccionarSesiones.svg)

---

## CU34. Crear nueva sesion
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite crear una nueva sesion de trabajo.
- **Precondiciones:** El actor ha iniciado sesion.
- **Flujo principal:** 1. El actor solicita crear una sesion. 2. Introduce nombre, proyecto, descripcion y modo de trabajo. 3. El sistema valida los datos. 4. El sistema registra la nueva sesion y la deja disponible.
- **Casos de Error:** Nombre vacio, datos invalidos o error de creacion.
- **Postcondiciones:** Se crea y activa una nueva sesion.
- **Diagrama:** ![Crear nueva sesion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CrearNuevaSesion/CrearNuevaSesion.svg)

---

## CU35. Eliminar sesion
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite eliminar una sesion de trabajo y sus artefactos asociados.
- **Precondiciones:** Existe una sesion registrada y accesible.
- **Flujo principal:** 1. El actor solicita eliminar la sesion. 2. El sistema solicita confirmacion. 3. El actor confirma. 4. El sistema elimina la sesion y los artefactos asociados.
- **Casos de Error:** Sesion no encontrada o eliminacion no permitida.
- **Postcondiciones:** La sesion deja de estar disponible.
- **Diagrama:** ![Eliminar sesion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/EliminarSesion/EliminarSesion.svg)

---

## CU36. Guardar resultados
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite guardar una instantanea de los resultados de la sesion.
- **Precondiciones:** Existe una sesion activa con artefactos asociados.
- **Flujo principal:** 1. El actor solicita guardar resultados. 2. El sistema recopila documentacion, casos de uso, requisitos, escenarios y borrador. 3. El sistema valida la consistencia basica. 4. El sistema guarda la instantanea en la sesion.
- **Casos de Error:** Sesion no encontrada o fallo al guardar resultados.
- **Postcondiciones:** Los resultados quedan guardados y asociados a la sesion.
- **Diagrama:** ![Guardar resultados](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/GuardarResultados/GuardarResultados.svg)

---

## CU37. Registrar caso de prueba en Kiwi TCMS
- **Actor:** Kiwi TCMS
- **Descripcion:** Representa la recepcion y registro externo del caso de prueba publicado desde el sistema.
- **Precondiciones:** El sistema ha enviado la informacion de un caso de prueba a Kiwi TCMS.
- **Flujo principal:** 1. Kiwi TCMS recibe la solicitud de registro. 2. Valida la informacion recibida. 3. Almacena el caso de prueba. 4. Devuelve una confirmacion con el identificador del caso.
- **Casos de Error:** Datos invalidos, autenticacion fallida o error de almacenamiento externo.
- **Postcondiciones:** El caso de prueba queda registrado en Kiwi TCMS.
- **Diagrama:** ![Registrar caso de prueba en Kiwi TCMS](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/KiwiTCMS/RegistrarCasoPrueba/RegistrarCasoPrueba.svg)

---

## CU38. Registrar cuenta
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite crear una cuenta de usuario para acceder al sistema.
- **Precondiciones:** El actor no tiene una sesion iniciada o accede desde la pantalla de registro.
- **Flujo principal:** 1. El actor solicita registrarse. 2. Introduce usuario, email y contrasena. 3. El sistema valida que los datos sean aceptables y que el usuario no exista. 4. El sistema crea la cuenta e inicia la sesion del usuario.
- **Casos de Error:** Usuario ya existente, datos incompletos o fallo al crear la cuenta.
- **Postcondiciones:** La cuenta queda creada y el usuario accede al sistema.
- **Diagrama:** ![Registrar cuenta](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/RegistrarCuenta/RegistrarCuenta.svg)

---

## CU39. Crear traspaso hacia los agentes
- **Actor:** Ingeniero de QA
- **Descripcion:** Permite transferir una sesion con documentacion al entorno de agentes para preparar artefactos y borradores de forma automatica.
- **Precondiciones:** El actor ha iniciado sesion, existe una sesion con proyecto y hay documentacion asociada.
- **Flujo principal:** 1. El actor elige continuar con el flujo de agentes. 2. El sistema valida que la sesion tenga proyecto y documentacion. 3. El sistema crea el traspaso al entorno de agentes. 4. El entorno de agentes recupera la documentacion de la sesion y prepara artefactos derivados. 5. El sistema guarda la referencia al entorno de agentes y marca la sesion como automatica. 6. El actor puede abrir el entorno de agentes para continuar la revision.
- **Casos de Error:** Sesion no encontrada, proyecto vacio, sesion sin documentacion o entorno de agentes no disponible.
- **Postcondiciones:** La sesion queda en modo automatico y el actor puede continuar el trabajo desde el entorno de agentes.
- **Diagrama:** ![Crear traspaso hacia los agentes](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CrearTraspasoHaciaAgentes/CrearTraspasoHaciaAgentes.svg)

---

[<- Volver al Indice](../../../README.md)
