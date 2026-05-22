# Conclusiones

Una vez la solución fue aprobada por el departamento de Hiperautomatización, se desplegó en el entorno de preproducción, donde se realizaron las pruebas finales de validación. Tras obtener resultados satisfactorios durante esta fase, el sistema fue promovido al entorno de producción.

Desde ese momento, la solución se encuentra en funcionamiento y cumple con los requisitos funcionales definidos durante las fases iniciales del proyecto.

| ID | Requisito funcional | Implementación |
|---|---|---|
| RF1 | Integrarse con el buzón de correo corporativo | El sistema se integra con el buzón compartido `VARIACIONES_PLANTA_EXTERIOR` mediante Exchange Online y el conector **Office 365 Outlook** de Power Automate. |
| RF2 | Recibir solicitud | Power Automate detecta automáticamente los correos entrantes dirigidos al buzón compartido y activa el flujo de procesamiento. |
| RF3 | Procesar solicitud recibida | El sistema analiza el correo recibido, obtiene atributos como el asunto y, en el caso por defecto, utiliza un agente para extraer la intención y los números incluidos en el mensaje. |
| RF4 | Generar respuesta automática | En función de la intención detectada y de los datos obtenidos en las consultas, el sistema construye automáticamente una respuesta adaptada al caso correspondiente. |
| RF5 | Enviar respuesta | La respuesta generada se envía al remitente mediante el buzón compartido utilizando las acciones de correo de Power Automate. |
| RF6 | Incorporar sistema de ampliación de detalle | El sistema permite que el usuario amplíe la información mediante un formulario de Microsoft Forms, especialmente en casos de documentación, desacuerdo o reclamación. |
| RF7 | Integrarse con sistema de formularios | Power Automate se integra con Microsoft Forms mediante el disparador `When a new response is submitted` y la acción `Get response details`. |
| RF8 | Recibir detalle | El flujo recibe los datos introducidos en el formulario, como nombre, correo, tipo de peticionario, número de expediente, tipo de solicitud, explicación y documentación adicional. |
| RF9 | Registrar información recibida | La información procedente de correos, números consultados y formularios se almacena en el dataset semántico `GestionVariaciones` de Power BI, en las tablas `CorreoRecibido`, `Numero` y `Formulario`. |
| RF10 | Ver solicitudes | El técnico dispone de una vista en Power BI que permite consultar solicitudes, filtrar por intención, técnico, número y estado, y revisar los formularios pendientes o resueltos. |
| RF11 | Actualizar estado | El técnico puede actualizar el estado de una solicitud mediante el procedimiento definido, enviando el correo interno `TE:FORMRESUELTO`, que activa el flujo encargado de actualizar el formulario. |

# Costes

El objetivo principal del proyecto era mejorar la eficiencia en la gestión del buzón, reduciendo tanto los tiempos de respuesta como los costes asociados al proceso. Aunque sigue siendo necesaria la intervención humana en determinadas situaciones, las tareas relacionadas con la interpretación de solicitudes y la consulta de aplicaciones corporativas han sido automatizadas mediante el sistema desarrollado.

Como resultado, el tiempo medio de gestión por solicitud se ha reducido de aproximadamente **15 minutos** a **menos de un minuto** en la mayoría de los casos. Este tiempo puede variar dependiendo del número de referencias o solicitudes incluidas en un mismo correo y de las consultas necesarias para su procesamiento.

![Tiempo](./imagen/Tiempo.png)

En cuanto al coste humano, también se ha producido una mejora significativa. Inicialmente, el proceso requería aproximadamente 15 personas trabajando de forma continua para cubrir las necesidades del buzón. Tras la reducción de plantilla derivada del ERE, el equipo pasó a estar formado por 6 personas, que se encontraban saturadas y no conseguían mantener el buzón actualizado, acumulándose correos sin responder.

Una vez implantada la solución en producción, el trabajo de estos empleados cambió significativamente. En lugar de dedicar su tiempo a responder manualmente las consultas, pasaron a centrarse en supervisar la información mostrada en Power BI y gestionar únicamente aquellos casos pendientes o que requerían intervención manual.

Por último, desde el punto de vista económico, la automatización ha supuesto una reducción considerable de costes. Se ha pasado de un modelo que requería una dedicación continua de un equipo amplio a un escenario donde un menor número de personas puede supervisar el servicio mientras realiza otras tareas paralelas.

Además del ahorro derivado de la reducción de tiempos y recursos humanos, el sistema incorpora una serie de costes asociados a su funcionamiento. La mayor parte del coste operativo proviene del uso de inteligencia artificial mediante **Power Apps / Power Automate con Prompt de Microsoft GPT-4.1 mini**, cuyo consumo depende directamente del número de correos procesados y de la complejidad de las consultas realizadas.

El coste aproximado de los componentes utilizados es el siguiente:

| Servicio | Métrica utilizada | Consumo estimado | Coste mensual |
|---|---:|---:|---:|
| Power Apps / Power Automate con Prompt de Microsoft GPT-4.1 mini | Message consumption | 1,1 mensajes por correo | ≈ 330 € |
| Power Platform Environment | Incluido | — | 0 € |
| Power Automate Premium | 1 licencia | Usuario genérico | 13 € |
| Power BI Pro | 1 licencia | Usuario | 9,40 € |

Para el cálculo del coste de IA se ha tomado como referencia una carga aproximada de **1.000 correos diarios**, equivalente a unos **30.000 correos mensuales**, obteniendo la siguiente estimación:

| Periodo | Correos procesados (aprox.) | Coste IA |
|---|---:|---:|
| Día | 1.000 | 11 € |
| Mes | 30.000 | 330 € |
| Año | 360.000 | 3.960 € |

El coste total aproximado del sistema en operación asciende a:

| Concepto | Coste mensual |
|---|---:|
| Consumo IA GPT-4.1 mini | 330 € |
| Power Automate Premium | 13 € |
| Power BI Pro x 5 personas | 47 € |
| Entorno | 0 € |
| **Total mensual** | **390 €** |

A pesar de estos costes operativos, el ahorro generado en tiempo de procesamiento y recursos humanos compensa ampliamente la inversión realizada, permitiendo mantener el servicio con una menor carga de trabajo manual y mejorando significativamente los tiempos de respuesta.

| Indicador                  |                           Antes |                   Después |
| -------------------------- | ------------------------------: | ------------------------: |
| Personal dedicado          | 15 personas contestando correos | 6 personas en supervisión |
| Tiempo medio por solicitud |                     ~15 minutos |                < 1 minuto |
| Coste mensual estimado     |                      ~17.010 €* |                 ~390 € |
| Coste anual estimado       |                      ~204.120 € |               ~4.228,80 € |

Los resultados muestran una reducción significativa tanto del tiempo de procesamiento como de los costes operativos, permitiendo transformar un proceso manual intensivo en recursos en un sistema principalmente automatizado y supervisado.

# Líneas futuras

Aunque el sistema desarrollado cumple los objetivos planteados y se encuentra actualmente en funcionamiento, existen diferentes líneas de mejora y ampliación que podrían implementarse en futuras versiones para aumentar sus capacidades y mejorar aún más la eficiencia del proceso.

Una posible mejora consistiría en habilitar un mecanismo para que otros departamentos o empleados pudieran consultar el estado de actuaciones y obras mediante un asunto específico en el correo. Esto permitiría agilizar procesos internos y reducir el tiempo invertido en tareas de investigación. Sin embargo, esta funcionalidad podría incrementar significativamente el volumen de correos recibidos y generar una saturación del buzón. Como alternativa, podría desarrollarse una integración mediante Microsoft Teams, permitiendo realizar consultas desde un entorno más adecuado para comunicaciones internas.

Otra línea de mejora sería la incorporación de un sistema de priorización automática de solicitudes. El sistema podría identificar casos que requieran una mayor atención mediante distintos criterios, como el número de consultas repetidas realizadas por un mismo usuario, reclamaciones previas, desacuerdos registrados o incidencias reiteradas. De este modo, determinadas solicitudes podrían escalarse automáticamente o situarse en una cola prioritaria para revisión manual.

También sería posible implementar un mecanismo de control de consultas repetidas. Actualmente, un usuario puede realizar múltiples consultas sobre una misma solicitud en un corto intervalo de tiempo, generando procesamiento innecesario y aumentando el consumo de recursos y costes asociados a la inteligencia artificial. Como mejora, el sistema podría limitar consultas repetidas sobre una misma referencia durante un periodo determinado, por ejemplo 24 o 48 horas, reutilizando la respuesta previamente generada cuando no existan cambios en la información disponible.

Además, podría incorporarse un sistema de caché o almacenamiento temporal de respuestas generadas. Esto permitiría reutilizar resultados recientes cuando diferentes usuarios soliciten información idéntica, reduciendo tiempos de procesamiento y costes derivados del uso de inteligencia artificial.

Por último, otra posible evolución sería incorporar métricas avanzadas y modelos predictivos sobre los datos almacenados en Power BI. A partir del histórico de solicitudes, reclamaciones y tiempos de resolución, podrían identificarse patrones, detectar cuellos de botella y anticipar situaciones de saturación o incidencias recurrentes.

# Conclusión final

El desarrollo de este proyecto ha permitido diseñar e implantar una solución de automatización basada en tecnologías Microsoft Power Platform e inteligencia artificial, orientada a optimizar la gestión del buzón de Variaciones de Planta Exterior. A lo largo del proyecto se han analizado las necesidades existentes, identificado los principales problemas operativos y desarrollado una solución capaz de automatizar gran parte del proceso de gestión de solicitudes. Parte del contenido y resultados del proyecto se encuentran recogidos en la documentación desarrollada.

La solución implementada ha demostrado cumplir los objetivos inicialmente planteados, permitiendo automatizar tareas repetitivas como la interpretación de correos, extracción de información, consultas a sistemas corporativos, generación automática de respuestas y registro de información para seguimiento y control. Esto ha supuesto una mejora significativa en la eficiencia del proceso, reduciendo drásticamente los tiempos de respuesta y disminuyendo la carga de trabajo manual necesaria.

Además de la reducción de tiempos, el proyecto ha demostrado el potencial de la inteligencia artificial aplicada a procesos empresariales reales, mostrando cómo tecnologías accesibles dentro del ecosistema Microsoft pueden integrarse para resolver problemas operativos complejos y aportar valor dentro de una organización de gran tamaño como Telefónica.

Los resultados obtenidos muestran que la automatización no solo reduce costes y mejora la productividad, sino que también transforma la forma de trabajar de los equipos implicados, permitiendo que los recursos humanos puedan centrarse en tareas de mayor valor añadido y dejando los procesos repetitivos a sistemas automáticos.

Finalmente, aunque el sistema ya se encuentra en producción y funcionando correctamente, las posibles líneas futuras identificadas evidencian que existe margen para seguir ampliando y evolucionando la solución. Esto demuestra que el proyecto no debe entenderse como un desarrollo cerrado, sino como una base sólida sobre la que continuar construyendo nuevas funcionalidades y capacidades en el futuro.
