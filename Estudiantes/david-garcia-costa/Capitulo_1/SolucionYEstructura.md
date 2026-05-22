## Metodología - Rational Unified Process (RUP)
Se utilizará RUP como marco metodológico para el desarrollo del proyecto. RUP es un marco de trabajo iterativo, incremental y centrado en la arquitectura, por lo que encaja con un proyecto en el que primero se define el problema, después se analiza el dominio, se diseña una solución técnica y finalmente se construye y valida un prototipo funcional.

## Estructura del Proyecto 
La metodología estructura el desarrollo en cuatro fases:

- Inicio: definición del problema y alcance.

- Elaboración: análisis, modelo de dominio y disciplina de requisitos.

- Construcción: diseño e implementación del prototipo controlado.

- Transición: validación y evaluación del prototipo.

---------------------------------------------------------

## Solución

El inicio del proyecto se recoge en este capítulo, donde se presenta la motivación, el problema detectado, los objetivos y la propuesta general de solución. En el segundo capítulo se desarrollará el análisis correspondiente, incluyendo el modelo de dominio y la disciplina de requisitos.

Debido a lo comentado en los apartados anteriores, la solución propuesta consiste en diseñar e implementar una aplicación con agentes de Inteligencia Artificial que apoye distintas fases del proceso de testing de QA de CIC Consulting Informático. En este primer planteamiento, la solución se entiende como una aproximación al problema, por lo que se centra en definir qué se quiere resolver y qué tipo de ayuda puede aportar una aplicación de este tipo.

La idea inicial es que la aplicación permita trabajar a partir de documentación funcional y ayude a extraer de ella información útil para el proceso de pruebas. A partir de esa documentación, se pretende identificar requisitos funcionales, casos de uso y posibles escenarios de prueba, de forma que el equipo de QA disponga de una primera base sobre la que revisar y completar el trabajo.

La propuesta se plantea como un flujo dividido en varias responsabilidades. Un agente podría analizar documentos como DRF o DDS para localizar requisitos y casos de uso relevantes; otro podría transformar esa información en escenarios Gherkin; y otro podría preparar una propuesta de caso de prueba para que sea revisada por una persona antes de considerarse definitiva.

La idea central es dividir el proceso en tareas especializadas y mantener la trazabilidad entre la documentación original, los requisitos detectados, los casos de uso y los escenarios de prueba propuestos. Esta división permitiría reducir la carga manual sin perder el control sobre las decisiones importantes del proceso.

La propuesta se plantea como un prototipo controlado, de manera que sea posible comprobar su viabilidad técnica y valorar su utilidad dentro de un contexto real. El objetivo no es eliminar la revisión del equipo de QA, sino reducir el trabajo repetitivo, acelerar la fase preparatoria y ofrecer una base más estructurada sobre la que el usuario pueda validar y corregir casos de prueba.

---

[← Volver al Índice](../README.md)
