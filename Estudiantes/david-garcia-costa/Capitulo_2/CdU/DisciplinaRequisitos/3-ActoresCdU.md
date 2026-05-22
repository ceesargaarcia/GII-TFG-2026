| [<- Matriz](./2-Matriz.md) | [-> PriorizarCdU](./4-PriorizarCdU.md) |

# Actores del sistema y casos de uso

## 1. Identificacion de actores

En el sistema se identifican dos actores principales:

![Actores](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/ActoresIndividuales/Actores.svg)

- **Ingeniero de QA (Quality Assurance)**: actor principal del sistema. Registra o inicia sesion, crea sesiones de trabajo, introduce documentacion funcional, mantiene artefactos funcionales, revisa borradores, publica resultados en Kiwi TCMS y consulta casos de prueba existentes.
- **Kiwi TCMS**: sistema externo con el que se integra la solucion. Recibe casos de prueba publicados y permite consultar casos de prueba existentes dentro del alcance funcional definido.

---

## 2. Actor y casos de uso

### 2.1 Ingeniero de QA

#### Casos de uso del actor Ingeniero de QA

![Casos de uso QA](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/CdUPorActor/QA/QA.svg)

---

### 2.2 Kiwi TCMS

#### Casos de uso del actor Kiwi TCMS

![Casos de uso Kiwi TCMS](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/CdUPorActor/KiwiTCMS/KiwiTCMS.svg)

---

## 3. Relacion entre actores y sistema

El flujo principal del sistema se articula a traves del actor Ingeniero de QA, que interactua con la solucion para gestionar sesiones de trabajo y transformar documentacion funcional en artefactos de prueba.

El sistema contempla dos modos funcionales:

- **Modo manual**: el Ingeniero de QA crea o mantiene documentacion, casos de uso, requisitos funcionales, escenarios Gherkin y borradores desde la interfaz.
- **Modo automatico**: el Ingeniero de QA solicita un traspaso a partir de una sesion con documentacion, y el sistema prepara casos de uso, requisitos funcionales, escenarios Gherkin y borradores para su posterior revision.

La integracion con **Kiwi TCMS** se utiliza para publicar casos de prueba y para las operaciones de consulta definidas en el alcance funcional.

De esta forma:

- **Ingeniero de QA** actua como actor primario e iniciador del flujo.
- **Kiwi TCMS** actua como sistema externo de integracion, persistencia y consulta.

---

[<- Volver al Indice](../../../README.md)
