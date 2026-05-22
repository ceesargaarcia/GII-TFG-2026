| [<- ActoresCdU](./3-ActoresCdU.md) | [-> DetallarCdU](./5-DetallarCdU.md) |

# Priorizacion de casos de uso por actor

## Tabla de prioridad

| ID | Caso de uso | Actor(es) | Prioridad |
|---|---|---|---|
| CU03 | Introducir documentacion funcional | Ingeniero de QA | Alta |
| CU12 | Crear caso de uso | Ingeniero de QA | Alta |
| CU17 | Crear requisito funcional | Ingeniero de QA | Alta |
| CU21 | Crear escenario Gherkin | Ingeniero de QA | Alta |
| CU25 | Crear borrador de caso de prueba | Ingeniero de QA | Alta |
| CU30 | Aceptar y publicar caso de prueba a partir de borrador | Ingeniero de QA | Alta |
| CU39 | Crear traspaso hacia los agentes | Ingeniero de QA | Alta |
| CU37 | Registrar caso de prueba en Kiwi TCMS | Kiwi TCMS | Alta |
| CU04 | Asociar documentacion a proyecto | Ingeniero de QA | Alta |
| CU08 | Actualizar documentacion | Ingeniero de QA | Alta |
| CU13 | Actualizar caso de uso | Ingeniero de QA | Alta |
| CU18 | Actualizar requisito funcional | Ingeniero de QA | Alta |
| CU28 | Anadir feedback a borrador | Ingeniero de QA | Alta |
| CU38 | Registrar cuenta | Ingeniero de QA | Alta |
| CU01 | Iniciar sesion | Ingeniero de QA | Alta |
| CU05 | Consultar referencias de documentacion de proyecto | Ingeniero de QA | Media |
| CU06 | Listar documentacion | Ingeniero de QA | Media |
| CU07 | Consultar documentacion | Ingeniero de QA | Media |
| CU09 | Eliminar documentacion | Ingeniero de QA | Media |
| CU10 | Listar casos de uso | Ingeniero de QA | Media |
| CU11 | Consultar caso de uso | Ingeniero de QA | Media |
| CU14 | Eliminar caso de uso | Ingeniero de QA | Media |
| CU15 | Listar requisitos funcionales | Ingeniero de QA | Media |
| CU16 | Consultar requisito funcional | Ingeniero de QA | Media |
| CU19 | Eliminar requisito funcional | Ingeniero de QA | Media |
| CU20 | Listar escenarios Gherkin | Ingeniero de QA | Media |
| CU22 | Consultar escenario Gherkin | Ingeniero de QA | Media |
| CU23 | Actualizar escenario Gherkin | Ingeniero de QA | Media |
| CU24 | Eliminar escenario Gherkin | Ingeniero de QA | Media |
| CU26 | Listar borradores | Ingeniero de QA | Media |
| CU27 | Consultar borrador | Ingeniero de QA | Media |
| CU29 | Rechazar borrador | Ingeniero de QA | Media |
| CU31 | Buscar casos de prueba en Kiwi TCMS | Ingeniero de QA | Media |
| CU32 | Ver caso de prueba en Kiwi TCMS | Ingeniero de QA | Media |
| CU33 | Seleccionar sesiones | Ingeniero de QA | Media |
| CU34 | Crear nueva sesion | Ingeniero de QA | Media |
| CU35 | Eliminar sesion | Ingeniero de QA | Media |
| CU36 | Guardar resultados | Ingeniero de QA | Media |
| CU02 | Cerrar sesion | Ingeniero de QA | Media |

---

## Justificacion por actor

### Ingeniero de QA

El actor `Ingeniero de QA` concentra el nucleo funcional del sistema. Se consideran especialmente representativos `CU03`, `CU39`, `CU12`, `CU17`, `CU21`, `CU25` y `CU30`, ya que reflejan el flujo principal real: introducir documentacion, decidir si continua en modo manual o automatico, mantener artefactos funcionales, construir escenarios verificables, elaborar borradores y aceptar la publicacion final en Kiwi TCMS.

Se consideran de prioridad `Media` los casos de uso orientados a consulta, listado, rechazo o soporte operativo. Aportan continuidad al proceso, pero no desbloquean por si mismos la generacion principal de artefactos y resultados de prueba.

### Kiwi TCMS

El sistema `Kiwi TCMS` actua como sistema externo participante. Su caso de uso `CU37 Registrar caso de prueba en Kiwi TCMS` tiene prioridad `Alta` porque materializa la integracion y completa la publicacion final de los casos de prueba generados.

---

[<- Volver al Indice](../../../README.md)
