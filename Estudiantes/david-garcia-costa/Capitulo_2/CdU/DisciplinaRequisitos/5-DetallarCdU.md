| [<- PriorizarCdU](./4-PriorizarCdU.md) | [-> Requisitos](./1-Requisitos.md) |

# Detalle de Casos de Uso

En esta seccion se detallan los casos de uso del sistema manteniendo una descripcion homogénea de actor, precondiciones, flujo principal, errores, postcondiciones y diagrama asociado.

---

## 1. Iniciar sesion

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite al usuario autenticarse en el sistema para acceder al resto de funcionalidades.

**Precondiciones:**
- El usuario dispone de credenciales validas.
- El sistema se encuentra disponible.

**Flujo principal:**
1. El sistema muestra el formulario de acceso.
2. El actor introduce sus credenciales.
3. El sistema valida la informacion recibida.
4. El sistema inicia la sesion del usuario.

**Casos de Error:**
- E1. Credenciales invalidas

El sistema rechaza el acceso e informa de que los datos introducidos no son correctos.
- E2. Usuario no autorizado

El sistema detecta que el usuario no tiene permisos suficientes y no permite el acceso.

**Postcondiciones:**
- El usuario queda autenticado en el sistema.

**Diagrama:**

![Iniciar sesion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/IniciarSesion/IniciarSesion.svg)

---

## 2. Cerrar sesion

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite finalizar la sesion activa del usuario.

**Precondiciones:**
- Existe una sesion iniciada.

**Flujo principal:**
1. El actor solicita cerrar sesion.
2. El sistema invalida la sesion activa.
3. El sistema redirige a la pantalla de acceso.

**Casos de Error:**
- E1. Sesion inexistente

El sistema detecta que no hay una sesion activa y no realiza cambios.
- E2. Error al cerrar sesion

El sistema informa de un fallo interno al intentar finalizar la sesion.

**Postcondiciones:**
- La sesion del usuario queda cerrada.

**Diagrama:**

![Cerrar sesion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CerrarSesion/CerrarSesion.svg)

---

## 3. Introducir documentacion funcional

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite introducir documentacion funcional en formato texto libre, DRF, DDS o combinacion de ellos para su posterior procesamiento.

**Precondiciones:**
- El actor ha iniciado sesion.
- El sistema permite cargar o registrar documentacion.

**Flujo principal:**
1. El sistema solicita la documentacion de entrada.
2. El actor proporciona el contenido o archivo correspondiente.
3. El sistema valida el formato recibido.
4. El sistema registra la documentacion en el contexto de trabajo.

**Casos de Error:**
- E1. Documentacion no proporcionada

El usuario no introduce contenido y el sistema cancela la operacion.
- E2. Formato no soportado

El sistema rechaza la documentacion por no corresponder con los formatos admitidos.
- E3. Error de almacenamiento

El sistema no consigue registrar la documentacion y notifica el fallo.

**Postcondiciones:**
- La documentacion queda disponible para su consulta y procesamiento.

**Diagrama:**

![Introducir documentacion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/IntroducirDocumentacion/IntroducirDocumentacion.svg)

---

## 4. Asociar documentacion a proyecto

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite vincular la documentacion introducida a un proyecto determinado para preservar el contexto.

**Precondiciones:**
- Existe documentacion registrada.
- Existe un proyecto de referencia.

**Flujo principal:**
1. El sistema muestra la documentacion pendiente de asociacion.
2. El actor selecciona el proyecto de referencia.
3. El sistema vincula la documentacion al proyecto.
4. El sistema confirma la asociacion realizada.

**Casos de Error:**
- E1. Proyecto no encontrado

El sistema no localiza el proyecto seleccionado y cancela la asociacion.
- E2. Documentacion ya asociada

El sistema informa de que la documentacion ya dispone de una asociacion valida.

**Postcondiciones:**
- La documentacion queda asociada a un proyecto.

**Diagrama:**

![Asociar documentacion a proyecto](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/AsociarDocumentacionAProyecto/AsociarDocumentacionAProyecto.svg)

---

## 5. Consultar referencias de documentacion de proyecto

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite consultar la documentacion registrada y asociada a un proyecto.

**Precondiciones:**
- Existe un proyecto en el sistema.
- El proyecto dispone de documentacion asociada.

**Flujo principal:**
1. El actor selecciona el proyecto.
2. El sistema recupera las referencias de documentacion asociadas.
3. El sistema muestra la informacion disponible.

**Casos de Error:**
- E1. Proyecto sin documentacion

El sistema informa de que no existen referencias documentales asociadas.
- E2. Error de recuperacion

El sistema no consigue acceder a la informacion solicitada.

**Postcondiciones:**
- El actor visualiza la documentacion asociada al proyecto.

**Diagrama:**

![Consultar referencias de documentacion de proyecto](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ConsultarReferenciasDocumentacionProyecto/ConsultarReferenciasDocumentacionProyecto.svg)

---

## 6. Extraer casos de uso

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite extraer casos de uso a partir de la documentacion funcional introducida.

**Precondiciones:**
- Existe documentacion registrada y accesible.
- La documentacion esta asociada al contexto adecuado.

**Flujo principal:**
1. El sistema recupera la documentacion del proyecto.
2. El sistema analiza el contenido funcional.
3. El sistema identifica los casos de uso.
4. El sistema registra los casos de uso extraidos.

**Casos de Error:**
- E1. Documentacion insuficiente

El sistema no encuentra informacion suficiente para extraer casos de uso.
- E2. Extraccion inconsistente

El sistema detecta ambigüedades y no genera resultados validos.
- E3. Error de persistencia

Los casos de uso se extraen pero no pueden almacenarse.

**Postcondiciones:**
- Se registran casos de uso locales asociados al proyecto.

**Diagrama:**

![Extraer casos de uso](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ExtraerCdU/ExtraerCdU.svg)

---

## 7. Listar casos de uso

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite visualizar el conjunto de casos de uso locales disponibles en el proyecto.

**Precondiciones:**
- Existen casos de uso registrados o potencialmente consultables.

**Flujo principal:**
1. El actor solicita el listado de casos de uso.
2. El sistema recupera los casos de uso del proyecto.
3. El sistema muestra el listado resultante.

**Casos de Error:**
- E1. No existen casos de uso

El sistema informa de que no hay casos de uso disponibles.
- E2. Error de consulta

El sistema no puede recuperar el listado solicitado.

**Postcondiciones:**
- El actor dispone de una vista general de los casos de uso.

**Diagrama:**

![Listar casos de uso](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ListarCdU/ListarCdU.svg)

---

## 8. Consultar caso de uso

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite consultar el detalle de un caso de uso concreto.

**Precondiciones:**
- Existe al menos un caso de uso registrado.

**Flujo principal:**
1. El actor selecciona un caso de uso.
2. El sistema recupera su informacion detallada.
3. El sistema muestra el contenido del caso de uso.

**Casos de Error:**
- E1. Caso de uso inexistente

El sistema no localiza el elemento seleccionado.
- E2. Error de acceso

El sistema no puede recuperar el detalle del caso de uso.

**Postcondiciones:**
- El actor visualiza la informacion del caso de uso seleccionado.

**Diagrama:**

![Consultar caso de uso](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ConsultarCdU/ConsultarCdU.svg)

---

## 9. Crear caso de uso

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite crear manualmente un caso de uso local en el sistema.

**Precondiciones:**
- El actor dispone de permisos para editar artefactos funcionales.

**Flujo principal:**
1. El sistema muestra el formulario de creacion.
2. El actor introduce la informacion del caso de uso.
3. El sistema valida los datos.
4. El sistema registra el nuevo caso de uso.

**Casos de Error:**
- E1. Datos incompletos

El sistema detecta informacion obligatoria ausente y no crea el caso de uso.
- E2. Error de validacion

El contenido no cumple las reglas definidas y el sistema rechaza la operacion.

**Postcondiciones:**
- Se crea un nuevo caso de uso local.

**Diagrama:**

![Crear caso de uso](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CrearCdU/CrearCdU.svg)

---

## 10. Actualizar caso de uso

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite modificar un caso de uso existente.

**Precondiciones:**
- Existe un caso de uso previamente registrado.

**Flujo principal:**
1. El actor selecciona el caso de uso a modificar.
2. El sistema muestra la informacion actual.
3. El actor actualiza el contenido.
4. El sistema valida y guarda los cambios.

**Casos de Error:**
- E1. Caso de uso no encontrado

El sistema no localiza el artefacto a actualizar.
- E2. Cambios invalidos

El sistema rechaza la actualizacion por inconsistencias en el contenido.

**Postcondiciones:**
- El caso de uso queda actualizado.

**Diagrama:**

![Actualizar caso de uso](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ActualizarCdU/ActualizarCdU.svg)

---

## 11. Eliminar caso de uso

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite eliminar un caso de uso local que ya no resulta necesario.

**Precondiciones:**
- Existe un caso de uso registrado.

**Flujo principal:**
1. El actor selecciona el caso de uso a eliminar.
2. El sistema solicita confirmacion.
3. El actor confirma la eliminacion.
4. El sistema retira el caso de uso del conjunto activo.

**Casos de Error:**
- E1. Caso de uso inexistente

El sistema no encuentra el elemento indicado.
- E2. Eliminacion no permitida

El sistema impide la operacion por restricciones de integridad o trazabilidad.

**Postcondiciones:**
- El caso de uso deja de estar disponible en el conjunto activo.

**Diagrama:**

![Eliminar caso de uso](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/EliminarCdU/EliminarCdU.svg)

---

## 12. Extraer requisitos funcionales

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite extraer requisitos funcionales a partir de la documentacion registrada.

**Precondiciones:**
- Existe documentacion funcional accesible.
- El sistema puede analizar su contenido.

**Flujo principal:**
1. El sistema recupera la documentacion del proyecto.
2. El sistema analiza el contenido funcional.
3. El sistema identifica los requisitos funcionales.
4. El sistema registra los resultados obtenidos.

**Casos de Error:**
- E1. Documentacion insuficiente

El sistema no encuentra informacion suficiente para extraer requisitos funcionales.
- E2. Extraccion ambigua

El sistema detecta informacion contradictoria o incompleta.
- E3. Error de registro

Los requisitos se obtienen pero no pueden almacenarse correctamente.

**Postcondiciones:**
- Se registran requisitos funcionales locales asociados al proyecto.

**Diagrama:**

![Extraer requisitos funcionales](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ExtraerRequisitosFuncionales/ExtraerRequisitosFuncionales.svg)

---

## 13. Listar requisitos funcionales

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite visualizar el conjunto de requisitos funcionales disponibles.

**Precondiciones:**
- Existen requisitos funcionales registrados o consultables.

**Flujo principal:**
1. El actor solicita el listado de requisitos funcionales.
2. El sistema recupera la informacion almacenada.
3. El sistema muestra el listado resultante.

**Casos de Error:**
- E1. No existen requisitos funcionales

El sistema informa de la ausencia de resultados.
- E2. Error de consulta

El sistema no puede recuperar la informacion solicitada.

**Postcondiciones:**
- El actor dispone de una vista general de los requisitos funcionales.

**Diagrama:**

![Listar requisitos funcionales](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ListarRequisitosFuncionales/ListarRequisitosFuncionales.svg)

---

## 14. Consultar requisito funcional

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite consultar el detalle de un requisito funcional concreto.

**Precondiciones:**
- Existe al menos un requisito funcional registrado.

**Flujo principal:**
1. El actor selecciona el requisito funcional.
2. El sistema recupera su detalle.
3. El sistema muestra la informacion correspondiente.

**Casos de Error:**
- E1. Requisito no encontrado

El sistema no localiza el requisito solicitado.
- E2. Error de acceso

El sistema no puede recuperar la informacion del requisito.

**Postcondiciones:**
- El actor visualiza el contenido del requisito funcional.

**Diagrama:**

![Consultar requisito funcional](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ConsultarRequisitosFuncionales/ConsultarRequisitosFuncionales.svg)

---

## 15. Crear requisito funcional

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite crear manualmente un requisito funcional local.

**Precondiciones:**
- El actor dispone de permisos de edicion.

**Flujo principal:**
1. El sistema muestra el formulario de creacion.
2. El actor introduce el contenido del requisito.
3. El sistema valida la informacion.
4. El sistema registra el nuevo requisito funcional.

**Casos de Error:**
- E1. Datos obligatorios ausentes

El sistema no permite registrar el requisito.
- E2. Error de validacion

La informacion introducida no cumple las reglas definidas.

**Postcondiciones:**
- Se crea un nuevo requisito funcional local.

**Diagrama:**

![Crear requisito funcional](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CrearRequisitosFuncionales/CrearRequisitosFuncionales.svg)

---

## 16. Actualizar requisito funcional

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite modificar un requisito funcional existente.

**Precondiciones:**
- Existe un requisito funcional registrado.

**Flujo principal:**
1. El actor selecciona el requisito funcional.
2. El sistema muestra su informacion actual.
3. El actor modifica el contenido.
4. El sistema valida y guarda los cambios.

**Casos de Error:**
- E1. Requisito no encontrado

El sistema no localiza el requisito a actualizar.
- E2. Actualizacion invalida

El sistema detecta inconsistencias y rechaza la modificacion.

**Postcondiciones:**
- El requisito funcional queda actualizado.

**Diagrama:**

![Actualizar requisito funcional](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ActualizarRequisitoFuncional/ActualizarRequisitoFuncional.svg)

---

## 17. Eliminar requisito funcional

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite eliminar un requisito funcional local que ya no sea necesario.

**Precondiciones:**
- Existe un requisito funcional registrado.

**Flujo principal:**
1. El actor selecciona el requisito funcional.
2. El sistema solicita confirmacion.
3. El actor confirma la eliminacion.
4. El sistema retira el requisito del conjunto activo.

**Casos de Error:**
- E1. Requisito inexistente

El sistema no encuentra el requisito indicado.
- E2. Eliminacion restringida

El sistema impide la operacion por motivos de integridad o trazabilidad.

**Postcondiciones:**
- El requisito funcional deja de estar disponible en el conjunto activo.

**Diagrama:**

![Eliminar requisito funcional](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/EliminarRequisitoFuncional/EliminarRequisitoFuncional.svg)

---

## 18. Generar escenarios Gherkin

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite generar escenarios Gherkin a partir de los casos de uso y requisitos funcionales definidos.

**Precondiciones:**
- Existen casos de uso y requisitos funcionales disponibles.
- La informacion funcional es suficientemente consistente.

**Flujo principal:**
1. El sistema recupera casos de uso y requisitos funcionales.
2. El sistema analiza la informacion disponible.
3. El sistema genera escenarios en formato Gherkin.
4. El sistema registra los escenarios obtenidos.

**Casos de Error:**
- E1. Falta de informacion funcional

El sistema no dispone de artefactos suficientes para generar escenarios.
- E2. Inconsistencia funcional

El sistema detecta contradicciones y no produce escenarios validos.
- E3. Error de almacenamiento

Los escenarios se generan pero no pueden registrarse.

**Postcondiciones:**
- Se generan escenarios Gherkin asociados al proyecto.

**Diagrama:**

![Generar escenarios Gherkin](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/GenerarEscenariosGherkin/GenerarEscenariosGherkin.svg)

---

## 19. Listar escenarios Gherkin

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite visualizar los escenarios Gherkin generados para el proyecto.

**Precondiciones:**
- Existen escenarios Gherkin registrados o consultables.

**Flujo principal:**
1. El actor solicita el listado de escenarios.
2. El sistema recupera los escenarios registrados.
3. El sistema muestra el listado resultante.

**Casos de Error:**
- E1. No existen escenarios

El sistema informa de que no hay escenarios disponibles.
- E2. Error de consulta

El sistema no puede recuperar la informacion solicitada.

**Postcondiciones:**
- El actor dispone de una vista general de los escenarios Gherkin.

**Diagrama:**

![Listar escenarios Gherkin](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ListarEscenariosGherkin/ListarEscenariosGherkin.svg)

---

## 20. Crear borrador de caso de prueba

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite crear un borrador de caso de prueba a partir de los escenarios Gherkin disponibles.

**Precondiciones:**
- Existen escenarios Gherkin generados.

**Flujo principal:**
1. El actor inicia la creacion del borrador.
2. El sistema recupera los escenarios relacionados.
3. El sistema construye una propuesta inicial de borrador.
4. El sistema registra el borrador generado.

**Casos de Error:**
- E1. No existen escenarios disponibles

El sistema no puede crear el borrador por falta de informacion base.
- E2. Error de generacion

El sistema no consigue construir el borrador correctamente.
- E3. Error de almacenamiento

El borrador se genera pero no puede registrarse.

**Postcondiciones:**
- Se crea un borrador de caso de prueba asociado al proyecto.

**Diagrama:**

![Crear borrador de caso de prueba](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CrearBorradorCasoPrueba/CrearBorradorCasoPrueba.svg)

---

## 21. Listar borradores

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite visualizar los borradores de caso de prueba disponibles.

**Precondiciones:**
- Existen borradores registrados o consultables.

**Flujo principal:**
1. El actor solicita el listado de borradores.
2. El sistema recupera los borradores del proyecto.
3. El sistema muestra el listado resultante.

**Casos de Error:**
- E1. No existen borradores

El sistema informa de que no hay borradores registrados.
- E2. Error de consulta

El sistema no puede recuperar el listado solicitado.

**Postcondiciones:**
- El actor dispone de una vista general de los borradores.

**Diagrama:**

![Listar borradores](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ListarBorrador/ListarBorrador.svg)

---

## 22. Consultar borrador

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite consultar el detalle de un borrador concreto.

**Precondiciones:**
- Existe al menos un borrador registrado.

**Flujo principal:**
1. El actor selecciona el borrador.
2. El sistema recupera su contenido.
3. El sistema muestra la informacion detallada.

**Casos de Error:**
- E1. Borrador inexistente

El sistema no localiza el borrador solicitado.
- E2. Error de acceso

El sistema no puede recuperar el detalle del borrador.

**Postcondiciones:**
- El actor visualiza el contenido del borrador seleccionado.

**Diagrama:**

![Consultar borrador](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/ConsultarBorrador/ConsultarBorrador.svg)

---

## 23. Anadir feedback a borrador

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite incorporar observaciones o correcciones a un borrador para refinarlo.

**Precondiciones:**
- Existe un borrador disponible para revision.

**Flujo principal:**
1. El actor selecciona el borrador a revisar.
2. El sistema muestra su contenido actual.
3. El actor introduce feedback o comentarios.
4. El sistema registra el feedback asociado al borrador.

**Casos de Error:**
- E1. Borrador no encontrado

El sistema no localiza el borrador seleccionado.
- E2. Feedback invalido

El sistema detecta contenido no valido o vacio y no lo registra.

**Postcondiciones:**
- El borrador queda enriquecido con feedback para una revision posterior.

**Diagrama:**

![Anadir feedback a borrador](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/AñadirFeedbackBorrador/AñadirFeedbackBorrador.svg)

---

## 24. Regenerar borrador

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite generar una nueva version de un borrador teniendo en cuenta la informacion disponible y el feedback incorporado.

**Precondiciones:**
- Existe un borrador previo.
- El sistema dispone de informacion base para regenerarlo.

**Flujo principal:**
1. El actor solicita la regeneracion del borrador.
2. El sistema recupera el borrador y su feedback asociado.
3. El sistema genera una nueva version del contenido.
4. El sistema registra la version regenerada.

**Casos de Error:**
- E1. Borrador inexistente

El sistema no localiza el borrador a regenerar.
- E2. Error de regeneracion

El sistema no puede producir una nueva version valida.

**Postcondiciones:**
- El borrador queda actualizado con una nueva version.

**Diagrama:**

![Regenerar borrador](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/RegenerarBorrador/RegenerarBorrador.svg)

---

## 25. Aceptar borrador

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite aceptar un borrador cuando su contenido se considera valido para continuar el flujo.

**Precondiciones:**
- Existe un borrador disponible para revision.

**Flujo principal:**
1. El actor consulta el borrador.
2. El actor confirma su aceptacion.
3. El sistema actualiza el estado del borrador a aceptado.

**Casos de Error:**
- E1. Borrador no encontrado

El sistema no localiza el borrador indicado.
- E2. Estado no valido

El borrador no puede aceptarse por no encontrarse en un estado adecuado.

**Postcondiciones:**
- El borrador queda aceptado para generar el caso de prueba.

**Diagrama:**

![Aceptar borrador](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/AceptarBorrador/AceptarBorrador.svg)

---

## 26. Rechazar borrador

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite rechazar un borrador cuando su contenido no resulta adecuado.

**Precondiciones:**
- Existe un borrador disponible para revision.

**Flujo principal:**
1. El actor selecciona el borrador.
2. El actor solicita su rechazo.
3. El sistema actualiza el estado del borrador.

**Casos de Error:**
- E1. Borrador no encontrado

El sistema no localiza el borrador seleccionado.
- E2. Estado no valido

El sistema no permite rechazar el borrador en su estado actual.

**Postcondiciones:**
- El borrador queda marcado como rechazado.

**Diagrama:**

![Rechazar borrador](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/RechazarBorrador/RechazarBorrador.svg)

---

## 27. Generar caso de prueba a partir de borrador

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite generar un caso de prueba final a partir de un borrador previamente aceptado.

**Precondiciones:**
- Existe un borrador aceptado.

**Flujo principal:**
1. El actor selecciona el borrador aceptado.
2. El sistema transforma el borrador en caso de prueba.
3. El sistema registra el caso de prueba generado.

**Casos de Error:**
- E1. Borrador no aceptado

El sistema impide la generacion si el borrador no esta validado.
- E2. Error de transformacion

El sistema no puede convertir el borrador en caso de prueba valido.
- E3. Error de almacenamiento

El caso de prueba se genera pero no puede registrarse.

**Postcondiciones:**
- Se registra un caso de prueba final derivado del borrador.

**Diagrama:**

![Generar caso de prueba a partir de borrador](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/GenerarCasoPruebaDeBorrador/GenerarCasoPruebaDeBorrador.svg)

---

## 28. Publicar caso de prueba en Kiwi TCMS

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite publicar un caso de prueba final en Kiwi TCMS.

**Precondiciones:**
- Existe un caso de prueba listo para publicar.
- La integracion con Kiwi TCMS esta disponible.

**Flujo principal:**
1. El actor selecciona el caso de prueba a publicar.
2. El sistema prepara la informacion necesaria.
3. El sistema envia el caso de prueba a Kiwi TCMS.
4. El sistema confirma la publicacion realizada.

**Casos de Error:**
- E1. Caso de prueba incompleto

El sistema detecta que faltan datos obligatorios y no publica el caso.
- E2. Error de integracion

La comunicacion con Kiwi TCMS falla y la publicacion no se completa.
- E3. Rechazo de Kiwi TCMS

El sistema externo no acepta el caso de prueba recibido.

**Postcondiciones:**
- El caso de prueba queda publicado en Kiwi TCMS.

**Diagrama:**

![Publicar caso de prueba en Kiwi TCMS](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/PublicarCasoPruebaKiwi/PublicarCasoPruebaKiwi.svg)

---

## 29. Buscar casos de prueba en Kiwi TCMS

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite buscar casos de prueba registrados en Kiwi TCMS.

**Precondiciones:**
- La integracion con Kiwi TCMS esta disponible.

**Flujo principal:**
1. El actor introduce criterios de busqueda.
2. El sistema envia la consulta a Kiwi TCMS.
3. El sistema recupera los resultados.
4. El sistema muestra los casos encontrados.

**Casos de Error:**
- E1. Sin resultados

Kiwi TCMS no devuelve coincidencias para la consulta realizada.
- E2. Error de conexion

El sistema no puede comunicarse con Kiwi TCMS.

**Postcondiciones:**
- El actor obtiene los resultados de busqueda disponibles en Kiwi TCMS.

**Diagrama:**

![Buscar casos de prueba en Kiwi TCMS](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/BuscarCasoPruebaKiwi/BuscarCasoPruebaKiwi.svg)

---

## 30. Ver caso de prueba en Kiwi TCMS

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite consultar el detalle de un caso de prueba almacenado en Kiwi TCMS.

**Precondiciones:**
- Existe al menos un caso de prueba accesible en Kiwi TCMS.

**Flujo principal:**
1. El actor selecciona un caso de prueba de Kiwi TCMS.
2. El sistema recupera su detalle desde el sistema externo.
3. El sistema muestra la informacion obtenida.

**Casos de Error:**
- E1. Caso no encontrado

Kiwi TCMS no localiza el caso de prueba solicitado.
- E2. Error de consulta externa

El sistema no puede recuperar la informacion desde Kiwi TCMS.

**Postcondiciones:**
- El actor visualiza el detalle del caso de prueba almacenado en Kiwi TCMS.

**Diagrama:**

![Ver caso de prueba en Kiwi TCMS](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/VerCasoPruebaKiwi/VerCasoPruebaKiwi.svg)

---

## 31. Seleccionar sesiones

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite seleccionar una sesion de trabajo existente para recuperar su contexto y continuar el trabajo previo.

**Precondiciones:**
- El actor ha iniciado sesion.
- Existen sesiones registradas y accesibles para el actor.

**Flujo principal:**
1. El sistema muestra las sesiones disponibles.
2. El actor selecciona una sesion concreta.
3. El sistema recupera el contexto y los artefactos asociados.
4. El sistema activa la sesion seleccionada.

**Casos de Error:**
- E1. Sesion no encontrada

El sistema no localiza la sesion solicitada.
- E2. Error de recuperacion

El sistema no puede cargar el contexto asociado a la sesion.

**Postcondiciones:**
- La sesion seleccionada queda activa con sus artefactos disponibles.

**Diagrama:**

![Seleccionar sesiones](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/SeleccionarSesiones/SeleccionarSesiones.svg)

---

## 32. Crear nueva sesion

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite crear una nueva sesion de trabajo para iniciar un contexto independiente de gestion de artefactos.

**Precondiciones:**
- El actor ha iniciado sesion.
- El sistema se encuentra disponible para registrar una nueva sesion.

**Flujo principal:**
1. El actor solicita crear una nueva sesion.
2. El sistema solicita los datos basicos de identificacion.
3. El actor introduce la informacion de la sesion.
4. El sistema registra y activa la nueva sesion.

**Casos de Error:**
- E1. Datos incompletos

El sistema no permite crear la sesion por faltar informacion obligatoria.
- E2. Error de creacion

El sistema no consigue registrar correctamente la nueva sesion.

**Postcondiciones:**
- Se crea y activa una nueva sesion de trabajo.

**Diagrama:**

![Crear nueva sesion](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/CrearNuevaSesion/CrearNuevaSesion.svg)

---

## 33. Guardar resultados

**Actor:** Ingeniero de QA  

**Descripcion:**  
Permite guardar los resultados generados o editados durante la sesion actual, incluyendo casos de uso, requisitos funcionales y borradores.

**Precondiciones:**
- Existe una sesion activa.
- Hay artefactos generados o modificados pendientes de persistencia.

**Flujo principal:**
1. El actor solicita guardar los resultados de la sesion.
2. El sistema identifica los casos de uso, requisitos funcionales y borradores pendientes.
3. El sistema valida la consistencia de los artefactos.
4. El sistema persiste los resultados y confirma la operacion.

**Casos de Error:**
- E1. No existen resultados pendientes

El sistema informa de que no hay cambios que guardar.
- E2. Error de persistencia

El sistema no consigue almacenar uno o varios artefactos de la sesion.

**Postcondiciones:**
- Los resultados de la sesion quedan guardados y asociados a su contexto de trabajo.

**Diagrama:**

![Guardar resultados](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/GuardarResultados/GuardarResultados.svg)

---

## 34. Registrar caso de prueba

**Actor:** Kiwi TCMS  

**Descripcion:**  
Permite al sistema externo recibir y registrar un caso de prueba publicado desde la solucion.

**Precondiciones:**
- El sistema ha enviado correctamente la informacion de un caso de prueba.
- Kiwi TCMS se encuentra operativo.

**Flujo principal:**
1. Kiwi TCMS recibe la solicitud de registro.
2. Kiwi TCMS valida la informacion recibida.
3. Kiwi TCMS almacena el caso de prueba.
4. Kiwi TCMS devuelve la confirmacion de registro.

**Casos de Error:**
- E1. Datos no validos

Kiwi TCMS rechaza la solicitud por contener informacion incorrecta o incompleta.
- E2. Error de almacenamiento externo

Kiwi TCMS no puede registrar el caso de prueba por un fallo interno.

**Postcondiciones:**
- El caso de prueba queda registrado en Kiwi TCMS.

**Diagrama:**

![Registrar caso de prueba](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/KiwiTCMS/RegistrarCasoPrueba/RegistrarCasoPrueba.svg)

---
