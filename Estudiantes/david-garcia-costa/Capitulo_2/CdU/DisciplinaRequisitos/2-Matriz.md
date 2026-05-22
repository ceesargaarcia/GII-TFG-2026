| [-> ActoresCdU](./3-ActoresCdU.md) | [<- Requisitos](./1-Requisitos.md) |

# Matriz de trazabilidad entre requisitos y casos de uso

## Casos de uso considerados

### Actor Ingeniero de QA

- CU01. IniciarSesion
- CU02. CerrarSesion
- CU03. IntroducirDocumentacion
- CU04. AsociarDocumentacionAProyecto
- CU05. ConsultarReferenciasDocumentacionProyecto
- CU06. ListarDocumentacion
- CU07. ConsultarDocumentacion
- CU08. ActualizarDocumentacion
- CU09. EliminarDocumentacion
- CU10. ListarCdU
- CU11. ConsultarCdU
- CU12. CrearCdU
- CU13. ActualizarCdU
- CU14. EliminarCdU
- CU15. ListarRequisitosFuncionales
- CU16. ConsultarRequisitoFuncional
- CU17. CrearRequisitoFuncional
- CU18. ActualizarRequisitoFuncional
- CU19. EliminarRequisitoFuncional
- CU20. ListarEscenariosGherkin
- CU21. CrearEscenarioGherkin
- CU22. ConsultarEscenarioGherkin
- CU23. ActualizarEscenarioGherkin
- CU24. EliminarEscenarioGherkin
- CU25. CrearBorradorCasoPrueba
- CU26. ListarBorradores
- CU27. ConsultarBorrador
- CU28. AnadirFeedbackBorrador
- CU29. RechazarBorrador
- CU30. AceptarYPublicarCasoPruebaDesdeBorrador
- CU31. BuscarCasoPruebaKiwi
- CU32. VerCasoPruebaKiwi
- CU33. SeleccionarSesiones
- CU34. CrearNuevaSesion
- CU35. EliminarSesion
- CU36. GuardarResultados
- CU38. RegistrarCuenta
- CU39. CrearTraspasoHaciaAgentes

### Actor Kiwi TCMS

- CU37. RegistrarCasoPruebaKiwi

## Matriz de requisitos funcionales

| Requisito | Titulo | Caso de uso |
|---|---|---|
| RF01 | Autenticacion y registro | CU38. RegistrarCuenta |
| RF01 | Autenticacion y registro | CU01. IniciarSesion |
| RF01 | Autenticacion y registro | CU02. CerrarSesion |
| RF02 | Introduccion de documentacion funcional | CU03. IntroducirDocumentacion |
| RF03 | Asociacion de documentacion a proyecto | CU04. AsociarDocumentacionAProyecto |
| RF04 | Gestion de documentacion registrada | CU05. ConsultarReferenciasDocumentacionProyecto |
| RF04 | Gestion de documentacion registrada | CU06. ListarDocumentacion |
| RF04 | Gestion de documentacion registrada | CU07. ConsultarDocumentacion |
| RF04 | Gestion de documentacion registrada | CU08. ActualizarDocumentacion |
| RF04 | Gestion de documentacion registrada | CU09. EliminarDocumentacion |
| RF05 | Gestion de casos de uso locales | CU10. ListarCdU |
| RF05 | Gestion de casos de uso locales | CU11. ConsultarCdU |
| RF05 | Gestion de casos de uso locales | CU12. CrearCdU |
| RF05 | Gestion de casos de uso locales | CU13. ActualizarCdU |
| RF05 | Gestion de casos de uso locales | CU14. EliminarCdU |
| RF06 | Gestion de requisitos funcionales locales | CU15. ListarRequisitosFuncionales |
| RF06 | Gestion de requisitos funcionales locales | CU16. ConsultarRequisitoFuncional |
| RF06 | Gestion de requisitos funcionales locales | CU17. CrearRequisitoFuncional |
| RF06 | Gestion de requisitos funcionales locales | CU18. ActualizarRequisitoFuncional |
| RF06 | Gestion de requisitos funcionales locales | CU19. EliminarRequisitoFuncional |
| RF07 | Gestion de escenarios Gherkin | CU20. ListarEscenariosGherkin |
| RF07 | Gestion de escenarios Gherkin | CU21. CrearEscenarioGherkin |
| RF07 | Gestion de escenarios Gherkin | CU22. ConsultarEscenarioGherkin |
| RF07 | Gestion de escenarios Gherkin | CU23. ActualizarEscenarioGherkin |
| RF07 | Gestion de escenarios Gherkin | CU24. EliminarEscenarioGherkin |
| RF08 | Gestion de borradores | CU25. CrearBorradorCasoPrueba |
| RF08 | Gestion de borradores | CU26. ListarBorradores |
| RF08 | Gestion de borradores | CU27. ConsultarBorrador |
| RF08 | Gestion de borradores | CU28. AnadirFeedbackBorrador |
| RF08 | Gestion de borradores | CU29. RechazarBorrador |
| RF09 | Aceptacion y publicacion de casos de prueba | CU30. AceptarYPublicarCasoPruebaDesdeBorrador |
| RF09 | Aceptacion y publicacion de casos de prueba | CU37. RegistrarCasoPruebaKiwi |
| RF10 | Consulta en Kiwi TCMS | CU31. BuscarCasoPruebaKiwi |
| RF10 | Consulta en Kiwi TCMS | CU32. VerCasoPruebaKiwi |
| RF11 | Gestion de sesiones de trabajo | CU33. SeleccionarSesiones |
| RF11 | Gestion de sesiones de trabajo | CU34. CrearNuevaSesion |
| RF11 | Gestion de sesiones de trabajo | CU35. EliminarSesion |
| RF11 | Gestion de sesiones de trabajo | CU39. CrearTraspasoHaciaAgentes |
| RF12 | Almacenamiento de resultados | CU36. GuardarResultados |
| RF13 | Trazabilidad funcional | CU03. IntroducirDocumentacion |
| RF13 | Trazabilidad funcional | CU04. AsociarDocumentacionAProyecto |
| RF13 | Trazabilidad funcional | CU05. ConsultarReferenciasDocumentacionProyecto |
| RF13 | Trazabilidad funcional | CU07. ConsultarDocumentacion |
| RF13 | Trazabilidad funcional | CU11. ConsultarCdU |
| RF13 | Trazabilidad funcional | CU16. ConsultarRequisitoFuncional |
| RF13 | Trazabilidad funcional | CU20. ListarEscenariosGherkin |
| RF13 | Trazabilidad funcional | CU22. ConsultarEscenarioGherkin |
| RF13 | Trazabilidad funcional | CU25. CrearBorradorCasoPrueba |
| RF13 | Trazabilidad funcional | CU27. ConsultarBorrador |
| RF13 | Trazabilidad funcional | CU30. AceptarYPublicarCasoPruebaDesdeBorrador |
| RF13 | Trazabilidad funcional | CU31. BuscarCasoPruebaKiwi |
| RF13 | Trazabilidad funcional | CU32. VerCasoPruebaKiwi |
| RF13 | Trazabilidad funcional | CU33. SeleccionarSesiones |
| RF13 | Trazabilidad funcional | CU34. CrearNuevaSesion |
| RF13 | Trazabilidad funcional | CU36. GuardarResultados |
| RF13 | Trazabilidad funcional | CU39. CrearTraspasoHaciaAgentes |
| RF14 | Traspaso hacia los agentes | CU39. CrearTraspasoHaciaAgentes |

## Matriz de requisitos no funcionales

| Requisito | Titulo | Caso de uso |
|---|---|---|
| RNF01 | Consistencia | CU08. ActualizarDocumentacion |
| RNF01 | Consistencia | CU12. CrearCdU |
| RNF01 | Consistencia | CU13. ActualizarCdU |
| RNF01 | Consistencia | CU17. CrearRequisitoFuncional |
| RNF01 | Consistencia | CU18. ActualizarRequisitoFuncional |
| RNF01 | Consistencia | CU21. CrearEscenarioGherkin |
| RNF01 | Consistencia | CU23. ActualizarEscenarioGherkin |
| RNF01 | Consistencia | CU25. CrearBorradorCasoPrueba |
| RNF01 | Consistencia | CU28. AnadirFeedbackBorrador |
| RNF01 | Consistencia | CU30. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF01 | Consistencia | CU39. CrearTraspasoHaciaAgentes |
| RNF02 | Trazabilidad | CU03. IntroducirDocumentacion |
| RNF02 | Trazabilidad | CU04. AsociarDocumentacionAProyecto |
| RNF02 | Trazabilidad | CU05. ConsultarReferenciasDocumentacionProyecto |
| RNF02 | Trazabilidad | CU07. ConsultarDocumentacion |
| RNF02 | Trazabilidad | CU11. ConsultarCdU |
| RNF02 | Trazabilidad | CU16. ConsultarRequisitoFuncional |
| RNF02 | Trazabilidad | CU20. ListarEscenariosGherkin |
| RNF02 | Trazabilidad | CU22. ConsultarEscenarioGherkin |
| RNF02 | Trazabilidad | CU25. CrearBorradorCasoPrueba |
| RNF02 | Trazabilidad | CU27. ConsultarBorrador |
| RNF02 | Trazabilidad | CU30. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF02 | Trazabilidad | CU31. BuscarCasoPruebaKiwi |
| RNF02 | Trazabilidad | CU32. VerCasoPruebaKiwi |
| RNF02 | Trazabilidad | CU33. SeleccionarSesiones |
| RNF02 | Trazabilidad | CU34. CrearNuevaSesion |
| RNF02 | Trazabilidad | CU36. GuardarResultados |
| RNF02 | Trazabilidad | CU39. CrearTraspasoHaciaAgentes |
| RNF03 | Usabilidad | CU38. RegistrarCuenta |
| RNF03 | Usabilidad | CU01. IniciarSesion |
| RNF03 | Usabilidad | CU02. CerrarSesion |
| RNF03 | Usabilidad | CU03. IntroducirDocumentacion |
| RNF03 | Usabilidad | CU06. ListarDocumentacion |
| RNF03 | Usabilidad | CU07. ConsultarDocumentacion |
| RNF03 | Usabilidad | CU10. ListarCdU |
| RNF03 | Usabilidad | CU11. ConsultarCdU |
| RNF03 | Usabilidad | CU15. ListarRequisitosFuncionales |
| RNF03 | Usabilidad | CU16. ConsultarRequisitoFuncional |
| RNF03 | Usabilidad | CU20. ListarEscenariosGherkin |
| RNF03 | Usabilidad | CU22. ConsultarEscenarioGherkin |
| RNF03 | Usabilidad | CU26. ListarBorradores |
| RNF03 | Usabilidad | CU27. ConsultarBorrador |
| RNF03 | Usabilidad | CU31. BuscarCasoPruebaKiwi |
| RNF03 | Usabilidad | CU32. VerCasoPruebaKiwi |
| RNF03 | Usabilidad | CU33. SeleccionarSesiones |
| RNF03 | Usabilidad | CU34. CrearNuevaSesion |
| RNF03 | Usabilidad | CU36. GuardarResultados |
| RNF03 | Usabilidad | CU39. CrearTraspasoHaciaAgentes |
| RNF04 | Integracion | CU30. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF04 | Integracion | CU31. BuscarCasoPruebaKiwi |
| RNF04 | Integracion | CU32. VerCasoPruebaKiwi |
| RNF04 | Integracion | CU37. RegistrarCasoPruebaKiwi |
| RNF04 | Integracion | CU39. CrearTraspasoHaciaAgentes |
| RNF05 | Mantenibilidad | CU03. IntroducirDocumentacion |
| RNF05 | Mantenibilidad | CU04. AsociarDocumentacionAProyecto |
| RNF05 | Mantenibilidad | CU08. ActualizarDocumentacion |
| RNF05 | Mantenibilidad | CU12. CrearCdU |
| RNF05 | Mantenibilidad | CU17. CrearRequisitoFuncional |
| RNF05 | Mantenibilidad | CU21. CrearEscenarioGherkin |
| RNF05 | Mantenibilidad | CU25. CrearBorradorCasoPrueba |
| RNF05 | Mantenibilidad | CU30. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF05 | Mantenibilidad | CU36. GuardarResultados |
| RNF05 | Mantenibilidad | CU39. CrearTraspasoHaciaAgentes |
| RNF06 | Extensibilidad | CU03. IntroducirDocumentacion |
| RNF06 | Extensibilidad | CU04. AsociarDocumentacionAProyecto |
| RNF06 | Extensibilidad | CU21. CrearEscenarioGherkin |
| RNF06 | Extensibilidad | CU30. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF06 | Extensibilidad | CU39. CrearTraspasoHaciaAgentes |
| RNF07 | Identificacion de contexto | CU03. IntroducirDocumentacion |
| RNF07 | Identificacion de contexto | CU04. AsociarDocumentacionAProyecto |
| RNF07 | Identificacion de contexto | CU05. ConsultarReferenciasDocumentacionProyecto |
| RNF07 | Identificacion de contexto | CU20. ListarEscenariosGherkin |
| RNF07 | Identificacion de contexto | CU25. CrearBorradorCasoPrueba |
| RNF07 | Identificacion de contexto | CU30. AceptarYPublicarCasoPruebaDesdeBorrador |
| RNF07 | Identificacion de contexto | CU33. SeleccionarSesiones |
| RNF07 | Identificacion de contexto | CU34. CrearNuevaSesion |
| RNF07 | Identificacion de contexto | CU36. GuardarResultados |
| RNF07 | Identificacion de contexto | CU39. CrearTraspasoHaciaAgentes |

---

[<- Volver al Indice](../../../README.md)
