# Introducción

## Motivación

En este Trabajo de Fin de Grado se propone el diseño y la implementación de una aplicación de apoyo al proceso de testing de QA de CIC Consulting Informático, basada en el uso de agentes de Inteligencia Artificial.

La motivación principal surge de una necesidad detectada en el contexto de trabajo de CIC: reducir la carga manual asociada a la preparación de casos de prueba a partir de documentación funcional y facilitar su posterior publicación en Kiwi TCMS.

Actualmente, una parte importante del esfuerzo de los ingenieros de QA se dedica a tareas de elevada carga cognitiva y repetitiva, como la interpretación de documentación de requisitos funcionales y documentos de diseño del sistema, la identificación de requisitos funcionales y casos de uso, la redacción de escenarios de prueba en lenguaje Gherkin y la organización de la información necesaria para crear casos de prueba. En la situación de partida, estos escenarios se gestionan de forma dispersa, por ejemplo en ficheros locales o documentos auxiliares, y no existe un flujo integrado que conecte de forma ordenada la documentación funcional con Kiwi TCMS. Estas actividades requieren una inversión considerable de tiempo y conocimiento especializado, lo que limita la escalabilidad del proceso y genera dependencia del criterio individual de cada miembro del equipo.

## Problemática Identificada

En términos más específicos, el flujo de trabajo actual presenta las siguientes limitaciones:

- **Dependencia del análisis manual:** la extracción de requisitos, casos de uso y escenarios de prueba depende en gran medida de la lectura e interpretación manual de la documentación.
- **Falta de automatización en la fase preparatoria:** no existe un mecanismo integrado que transforme la documentación funcional en escenarios Gherkin o borradores de casos de prueba.
- **Escalabilidad limitada:** el tiempo necesario para preparar y revisar los casos de prueba crece de forma significativa cuando aumenta el volumen de documentación o el número de proyectos.
- **Trazabilidad mejorable:** la relación entre documentación, requisitos, casos de uso, escenarios y casos publicados en Kiwi TCMS puede perderse si se trabaja con ficheros o documentos independientes.

## Propuesta de Solución

Para resolver este problema, el presente trabajo plantea como primera aproximación el desarrollo de una aplicación con posibilidad de realizarlo de forma manual o con agentes de Inteligencia Artificial que ayuden al equipo de QA en las tareas previas a la creación y publicación de casos de prueba. La idea principal es que la aplicación pueda recibir documentación funcional, analizar su contenido y proponer una estructura inicial de requisitos, casos de uso, escenarios de prueba y borrador para caso de prueba.

La solución se concibe como un flujo dividido en tareas especializadas. Un agente podría encargarse de interpretar la documentación de entrada y localizar los elementos relevantes; otro podría transformar esa información en escenarios de prueba en lenguaje Gherkin; y otro podría preparar una propuesta revisable antes de que el equipo decida si debe incorporarse al sistema de gestión de pruebas.

Esta propuesta no busca sustituir completamente la intervención del equipo de QA, sino servir como herramienta de apoyo. El objetivo es reducir tareas repetitivas, estructurar mejor la información y mantener siempre una revisión humana antes de que los casos de prueba sean considerados válidos. De esta forma, el proyecto plantea un prototipo controlado que permita comprobar si este enfoque es viable y útil dentro de un contexto real.

---

[← Volver al Índice](../README.md)
