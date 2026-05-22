| [-> Matriz](./2-Matriz.md) |

# Requisitos funcionales

## RF01. Autenticacion y registro

El sistema debe permitir registrar una cuenta de usuario, iniciar sesion, consultar la sesion activa y cerrar sesion.

## RF02. Introduccion de documentacion funcional

El sistema debe permitir introducir documentacion funcional en formato texto, clasificandola como DRF o DDS y asociandola a una sesion de trabajo.

## RF03. Asociacion de documentacion a proyecto

El sistema debe permitir asociar la documentacion introducida a un proyecto mediante un nombre de proyecto identificable.

## RF04. Gestion de documentacion registrada

El sistema debe permitir consultar referencias, listar, consultar, actualizar y eliminar documentacion funcional registrada.

## RF05. Gestion de casos de uso locales

El sistema debe permitir listar, consultar, crear, actualizar y eliminar casos de uso locales en el flujo manual.

## RF06. Gestion de requisitos funcionales locales

El sistema debe permitir listar, consultar, crear, actualizar y eliminar requisitos funcionales locales en el flujo manual.

## RF07. Gestion de escenarios Gherkin

El sistema debe permitir listar, crear, consultar, actualizar y eliminar escenarios Gherkin asociados a una sesion y trazables a casos de uso y requisitos funcionales.

## RF08. Gestion de borradores

El sistema debe permitir crear, listar, consultar, actualizar, enriquecer con feedback, marcar feedback como resuelto y rechazar borradores de caso de prueba.

## RF09. Aceptacion y publicacion de casos de prueba

El sistema debe permitir aceptar un borrador valido y publicarlo en Kiwi TCMS como caso de prueba.

## RF10. Consulta en Kiwi TCMS

El sistema debe permitir buscar y ver casos de prueba en Kiwi TCMS.

## RF11. Gestion de sesiones de trabajo

El sistema debe permitir seleccionar sesiones existentes, crear nuevas sesiones, actualizar sus datos, eliminar sesiones y guardar resultados para continuar, iniciar o depurar el trabajo en un contexto diferenciado.

## RF12. Almacenamiento de resultados

El sistema debe permitir guardar una instantanea de los resultados generados o editados durante una sesion, incluyendo documentacion, casos de uso, requisitos funcionales, escenarios Gherkin y borradores.

## RF13. Trazabilidad funcional

El sistema debe mantener trazabilidad entre proyecto, sesion, documentacion, casos de uso, requisitos funcionales, escenarios Gherkin, borradores y casos de prueba publicados.

## RF14. Traspaso hacia los agentes

El sistema debe permitir transferir una sesion con proyecto y documentacion al flujo automatico para que se preparen artefactos de prueba (CU,RF,Escenarios) y borradores sin continuar el flujo manual.

---

# Requisitos no funcionales

## RNF01. Consistencia

El sistema debe mantener coherencia entre los artefactos generados y gestionados durante todo el flujo funcional, tanto en modo manual como en modo automatico.

## RNF02. Trazabilidad

El sistema debe conservar referencias de origen y relaciones entre artefactos funcionales y de prueba.

## RNF03. Usabilidad

El sistema debe ofrecer operaciones claras, diferenciadas y comprensibles para el flujo manual, el flujo automatico y la integracion con Kiwi TCMS.

## RNF04. Integracion

El sistema debe integrarse con Kiwi TCMS y con el flujo automatico mediante interfaces definidas.

## RNF05. Mantenibilidad

El sistema debe organizar sus responsabilidades funcionales de manera modular.

## RNF06. Extensibilidad

El sistema debe permitir incorporar nuevas fuentes, nuevas operaciones y nuevos flujos automaticos sin alterar la estructura funcional general.

## RNF07. Identificacion de contexto

El sistema debe preservar la relacion entre cada artefacto, su sesion y su proyecto de referencia.

---

[<- Volver al Indice](../../../README.md)
