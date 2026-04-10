|[ ← Matriz](./Matriz.md) | [ → PriorizarCdU](./PriorizarCdU.md) |

# Actores del Sistema y Casos de Uso

## 1. Identificación de Actores

En el sistema se identifican dos actores principales:


![Actores](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/ActoresIndividuales/Actores.svg)


- **Ingeniero de QA (Quality Assurance)**: actor principal del sistema, responsable de iniciar y controlar el flujo de trabajo. A partir de la documentación del proyecto, utiliza el sistema para seleccionar o crear nuevas sesiones de trabajo, extraer casos de uso y requisitos funcionales, generar escenarios de prueba, refinarlos mediante iteraciones y feedback, guardar los resultados obtenidos y finalmente publicar los casos de prueba. Actúa como supervisor del proceso, validando los artefactos generados antes de su integración en herramientas externas.

- **Kiwi TCMS**: sistema externo con el que se integra la solución, encargado de registrar los casos de prueba generados. Proporciona funcionalidades de persistencia, consulta y administración de los casos de prueba, permitiendo su reutilización y seguimiento dentro del proceso de testing.



---

## 2. Actor y Casos de Uso

### 2.1 Ingeniero de QA (Quality Assurance)


#### Casos de uso del actor Ingeniero de QA

![Casos de uso QA](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/CdUPorActor/QA/QA.svg)

---

### 2.2 Kiwi TCMS

#### Casos de uso del actor Kiwi TCMS

![Casos de uso Kiwi TCMS](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/CdUPorActor/KiwiTCMS/KiwiTCMS.svg)

---

## 4. Relación entre Actores y Sistema

El flujo principal del sistema se articula a través del actor Ingeniero de QA, que interactúa con el sistema para gestionar sesiones de trabajo y transformar documentación en casos de prueba.

El sistema, a su vez, se integra con **Kiwi TCMS** para almacenar y gestionar los resultados finales.

De esta forma:

- **QA** actúa como actor primario (iniciador del flujo)
- **Kiwi TCMS** actúa como sistema externo (soporte de persistencia y consulta)

---

## 5. Diagrama Contexto
![Diagrama de Contexto](/Estudiantes/david-garcia-costa/Capitulo_2/CdU/DiagramaContexto/DiagramaContexto.svg)
