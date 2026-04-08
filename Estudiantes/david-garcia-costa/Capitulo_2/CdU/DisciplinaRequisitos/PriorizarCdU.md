|[ ← ActoresCdU](./ActoresCdU.md) |[ → DetallarCdU](./DetallarCdU.md) |

# Priorizacion de Casos de Uso por Actor

## Tabla de prioridad

| Caso de uso | Actor | Prioridad | Motivo |
|---|---|---|---|
| Incorporar documentación |Ingeniero de QA | Alta | Es el punto de entrada del sistema; sin documentación no existe flujo de trabajo. |
| Asociar documentación a proyecto | Ingeniero de QA | Alta | Permite mantener la trazabilidad de los artefactos dentro del contexto organizativo. |
| Extraer casos de uso | Ingeniero de QA | Alta | Permite obtener el comportamiento funcional a partir de la documentación. |
| Extraer requisitos funcionales | Ingeniero de QA | Alta | Permite identificar las necesidades funcionales que deben validarse. |
| Gestionar casos de uso | Ingeniero de QA | Alta | Permite revisar, corregir y mantener actualizados los casos de uso. |
| Gestionar requisitos funcionales | Ingeniero de QA | Alta | Garantiza la consistencia y trazabilidad de los requisitos funcionales. |
| Generar escenarios de prueba | Ingeniero de QA | Alta | Es una de las salidas principales del sistema y base para la validación funcional. |
| Gestionar escenarios de prueba | Ingeniero de QA | Media | Permite refinar y mantener escenarios, pero depende de su generación previa. |
| Incorporar feedback a escenarios | Ingeniero de QA | Media | Permite mejorar los escenarios, aunque no es el núcleo del flujo principal. |
| Generar casos de prueba | Ingeniero de QA | Alta | Convierte los escenarios en un artefacto final listo para validación o publicación. |
| Publicar casos de prueba en Kiwi TCMS | Ingeniero de QA | Alta | Cierra el flujo funcional integrando el sistema con la herramienta externa. |
| Consultar casos de prueba en Kiwi TCMS | Ingeniero de QA | Media | Permite verificar y reutilizar casos ya publicados, pero no impulsa el flujo principal. |
| Recibir casos de prueba | Kiwi TCMS | Alta | Sin esta capacidad no existe integración efectiva con el sistema externo. |
| Almacenar casos de prueba | Kiwi TCMS | Alta | Garantiza la persistencia y reutilización de los casos de prueba. |
| Permitir consulta de casos de prueba | Kiwi TCMS | Media | Facilita el acceso posterior a los casos, pero depende de su almacenamiento previo. |

---

## Justificacion por actor

### Ingeniero de QA

El actor `Ingeniero de QA` concentra el núcleo funcional del sistema.

Las funcionalidades `Incorporar documentación` y `Asociar documentación a proyecto` tienen prioridad `Alta`, ya que representan el punto de entrada del sistema y permiten establecer la trazabilidad desde el contexto organizativo externo.

A partir de ahí, `Extraer casos de uso` y `Extraer requisitos funcionales` también tienen prioridad `Alta`, ya que constituyen la primera fase de transformación de la información documental en artefactos estructurados.

Las funcionalidades `Gestionar casos de uso` y `Gestionar requisitos funcionales` se consideran igualmente de prioridad `Alta`, al permitir mantener la calidad, consistencia y corrección de la información funcional que servirá como base para la generación de pruebas.

En la fase de generación, `Generar escenarios de prueba` y `Generar casos de prueba` tienen prioridad `Alta` porque representan las principales salidas del sistema. En particular, los escenarios actúan como paso intermedio y los casos de prueba como artefacto final.

Por otro lado, `Gestionar escenarios de prueba` e `Incorporar feedback a escenarios` se clasifican como `Media`, ya que son actividades de refinamiento y mejora, importantes pero dependientes de la existencia previa de escenarios.

Finalmente, `Publicar casos de prueba en Kiwi TCMS` tiene prioridad `Alta` al cerrar el flujo funcional completo, permitiendo integrar los artefactos generados con el sistema externo de testing. La funcionalidad `Consultar casos de prueba en Kiwi TCMS` se clasifica como `Media`, ya que aporta valor en la reutilización y verificación, pero no es imprescindible para la generación inicial.

---

### Kiwi TCMS

El sistema `Kiwi TCMS` actúa como sistema externo participante que completa el flujo funcional del sistema.

Las funcionalidades `Recibir casos de prueba` y `Almacenar casos de prueba` tienen prioridad `Alta`, ya que son imprescindibles para que la integración tenga efecto y los casos de prueba puedan persistir y ser utilizados.

La funcionalidad `Permitir consulta de casos de prueba` se considera de prioridad `Media`, ya que facilita el acceso posterior a los artefactos, pero depende de que estos hayan sido previamente publicados y almacenados.

