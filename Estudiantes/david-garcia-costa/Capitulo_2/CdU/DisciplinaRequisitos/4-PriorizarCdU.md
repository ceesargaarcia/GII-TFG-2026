| [<- ActoresCdU](./3-ActoresCdU.md) | [-> DetallarCdU](./5-DetallarCdU.md) |

# Priorizacion de Casos de Uso por Actor

## Tabla de prioridad

| ID | Caso de Uso | Actor(es) | Prioridad |q
|---|---|---|---|
| CU03 | Introducir documentacion funcional | Ingeniero de QA | Alta |
| CU06 | Extraer casos de uso | Ingeniero de QA | Alta |
| CU12 | Extraer requisitos funcionales | Ingeniero de QA | Alta |
| CU18 | Generar escenarios Gherkin | Ingeniero de QA | Alta |
| CU28 | Publicar caso de prueba en Kiwi TCMS | Ingeniero de QA | Alta |
| CU04 | Asociar documentacion a proyecto | Ingeniero de QA | Alta |
| CU09 | Crear caso de uso | Ingeniero de QA | Alta |
| CU10 | Actualizar caso de uso | Ingeniero de QA | Alta |
| CU15 | Crear requisito funcional | Ingeniero de QA | Alta |
| CU16 | Actualizar requisito funcional | Ingeniero de QA | Alta |
| CU20 | Crear borrador de caso de prueba | Ingeniero de QA | Alta |
| CU23 | Añadir feedback a borrador | Ingeniero de QA | Alta |
| CU25 | Aceptar borrador | Ingeniero de QA | Alta |
| CU27 | Generar caso de prueba a partir de borrador | Ingeniero de QA | Alta |
| CU01 | Iniciar sesion | Ingeniero de QA | Alta |
| CU31 | Seleccionar sesiones | Ingeniero de QA | Media |
| CU32 | Crear nueva sesion | Ingeniero de QA | Media |
| CU33 | Guardar resultados | Ingeniero de QA | Media |
| CU02 | Cerrar sesion | Ingeniero de QA | Media |
| CU05 | Consultar referencias de documentacion de proyecto | Ingeniero de QA | Media |
| CU07 | Listar casos de uso | Ingeniero de QA | Media |
| CU08 | Consultar caso de uso | Ingeniero de QA | Media |
| CU11 | Eliminar caso de uso | Ingeniero de QA | Media |
| CU13 | Listar requisitos funcionales | Ingeniero de QA | Media |
| CU14 | Consultar requisito funcional | Ingeniero de QA | Media |
| CU17 | Eliminar requisito funcional | Ingeniero de QA | Media |
| CU19 | Listar escenarios Gherkin | Ingeniero de QA | Media |
| CU21 | Listar borradores | Ingeniero de QA | Media |
| CU22 | Consultar borrador | Ingeniero de QA | Media |
| CU24 | Regenerar borrador | Ingeniero de QA | Media |
| CU26 | Rechazar borrador | Ingeniero de QA | Media |
| CU29 | Buscar casos de prueba en Kiwi TCMS | Ingeniero de QA | Media |
| CU30 | Ver caso de prueba en Kiwi TCMS | Ingeniero de QA | Media |
| CU34 | Registrar caso de prueba | Kiwi TCMS | Alta |

---

## Justificacion por actor

### Ingeniero de QA

El actor `Ingeniero de QA` concentra el nucleo funcional del sistema. Se consideran especialmente representativos `CU03`, `CU06`, `CU12`, `CU18` y `CU28`, ya que reflejan el flujo principal del sistema: entrada de documentacion, extraccion de artefactos funcionales, generacion de escenarios verificables y publicacion final en la herramienta externa.

Dentro de los casos de uso con prioridad `Alta`, esos cinco casos de uso se sitúan en primer lugar por ser los más representativos del flujo principal. A continuacion aparecen el resto de casos de uso de prioridad alta necesarios para completar, mantener o consolidar dicho flujo, incluyendo la incorporacion de feedback al borrador como parte clave de la revision antes de generar el caso de prueba final.

Se consideran de prioridad `Media` los casos de uso orientados a consulta, listado, cierre de sesion o refinamiento incremental, ya que aportan soporte al proceso pero no desbloquean por si mismos la generacion principal de artefactos.

Ademas, `CU31`, `CU32` y `CU33` se consideran de prioridad `Media` porque aportan soporte a la continuidad del trabajo y a la persistencia de artefactos, pero no desbloquean por si mismos la generacion principal de resultados funcionales y de prueba.

### Kiwi TCMS

El sistema `Kiwi TCMS` actua como sistema externo participante. Su caso de uso `Registrar caso de prueba` tiene prioridad `Alta` porque permite completar la integracion y materializar la publicacion final de los casos de prueba generados.
