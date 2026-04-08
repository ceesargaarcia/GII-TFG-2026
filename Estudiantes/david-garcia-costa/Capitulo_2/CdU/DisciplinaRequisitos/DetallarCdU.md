|[ ← PriorizarCdU](./PriorizarCdU.md) | [ → Requisitos](./Requisitos.md) |

# Detalle de Casos de Uso

En esta sección se detallan los casos de uso más relevantes del sistema, seleccionados por su importancia dentro del flujo funcional principal.

---

## 1. Incorporar documentación

**Actor:** Ingeniero de QA  

**Descripción:**  
Permite al usuario incorporar documentación (como DRF o DDS) al sistema para su posterior procesamiento.

**Precondiciones:**
- El actor tiene acceso al sistema.
- Existe un proyecto al que asociar la documentación (opcional dependiendo del flujo).

**Flujo principal:**

1. El sistema solicita el documento.
2. El actor proporciona el documento.
3. El sistema valida el formato.
4. El sistema almacena la documentación.

**Casos de Error:**
- E1. Archivo no proporcionado

El usuario inicia la operación, pero no selecciona ningún archivo. El sistema cancela la incorporación y muestra un mensaje indicando que debe seleccionarse un documento.
- E2. Formato de archivo no válido

El usuario proporciona un archivo en un formato no soportado. El sistema rechaza el documento e informa del error.
- E3. Documento corrupto o ilegible

El archivo no puede abrirse o procesarse correctamente. El sistema notifica que el contenido no es accesible.
- E4. Error al asociar el documento al proyecto

El sistema no encuentra el proyecto o no puede vincular el documento al contexto seleccionado. La operación queda cancelada.
- E5. Error de almacenamiento

El sistema no puede guardar la documentación por un fallo interno o de persistencia. Se informa al usuario y no se registra el documento.


**Postcondiciones:**
- Se puede utilizar para extraer información posteriormente.

**Diagrama:**

![Incorporar documentación](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA//IncorporarDocumentacion.svg)

---

## 2. Generar escenarios de prueba

**Actor:** Ingeniero de QA  

**Descripción:**  
Permite generar escenarios de prueba en formato Gherkin a partir de los casos de uso y requisitos funcionales previamente definidos.

**Precondiciones:**
- Existen casos de uso y requisitos funcionales en el sistema.
- Están correctamente definidos.

**Flujo principal:**
1. El sistema recupera los casos de uso y requisitos.
2. El sistema procesa la información.
3. El sistema genera escenarios en formato Gherkin.

**Casos de Error:**
- E1. No existen casos de uso o requisitos funcionales

El sistema no dispone de información suficiente para generar escenarios. Se cancela la operación y se informa al usuario.
- E2. Información incompleta o inconsistente

Los casos de uso o requisitos funcionales existen, pero contienen datos insuficientes, ambiguos o contradictorios. El sistema notifica el problema y no genera escenarios válidos.
- E3. Error en el procesamiento de la información

Durante la transformación de requisitos a escenarios de prueba, el sistema no puede completar el análisis. La generación se interrumpe.
- E4. Escenarios generados no válidos

El sistema produce escenarios con estructura incorrecta o no alineados con el formato esperado. Los escenarios no se almacenan como válidos y se solicita revisión.
- E5. Error al guardar los escenarios

Aunque la generación se complete, el sistema no consigue persistir los borradores. Se informa del fallo y no se conservan los resultados.

**Postcondiciones:**
- Se generan escenarios de prueba asociados al proyecto.
- Los escenarios pueden ser revisados o modificados.

**Diagrama:**

![Generar escenarios](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/GenerarEscenariosPrueba.svg)

---

## 3. Generar casos de prueba

**Actor:** Ingeniero de QA  

**Descripción:**  
Permite generar casos de prueba finales a partir de los escenarios previamente definidos.

**Precondiciones:**
- Existen escenarios de prueba válidos.
- Han sido revisados o aprobados.

**Flujo principal:**
1. El sistema recupera los escenarios.
2. El sistema transforma los escenarios en casos de prueba.
3. El sistema guarda los casos generados.

**Casos de Error:**
- E1. No existen escenarios de prueba previos

El usuario solicita la generación de casos de prueba, pero el sistema no encuentra escenarios disponibles. La operación se cancela.
- E2. Escenarios no validados o incompletos

Los escenarios existen, pero no están en un estado adecuado para generar casos de prueba. El sistema informa de que deben revisarse antes.
- E3. Error en la transformación a casos de prueba

El sistema no puede convertir correctamente los escenarios en casos de prueba. Se detiene el proceso y se notifica el error.
- E4. Casos de prueba generados con información insuficiente

Se detecta que los casos generados no contienen datos mínimos necesarios, como resumen o pasos claros. El sistema marca el resultado como no válido.
- E5. Error al almacenar o preparar la publicación

Los casos de prueba se generan, pero no pueden guardarse o prepararse para su publicación posterior. El sistema informa del fallo.

**Postcondiciones:**
- Se generan casos de prueba listos para publicación.
- Pueden enviarse a Kiwi TCMS.

**Diagrama:**

![Generar casos de prueba](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DetallarCdU/QA/GenerarCasosPrueba.svg)

---