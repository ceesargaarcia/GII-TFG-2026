# TFG — César García Eguiguren

Netkia Analytics es un módulo analítico externo desacoplado del ERP Odoo que transforma datos operativos en KPIs estratégicos para directores y responsables de proyecto de Netkia, una consultoría tecnológica de 110 empleados.

## Índice general

### Capítulo 1 — Introducción y contexto

- [Resumen](Capítulo-1/README.md#resumen)
- [Introducción](Capítulo-1/README.md#introducción)
  - [Contextualización del entorno empresarial](Capítulo-1/README.md#contextualización-del-entorno-empresarial)
  - [Problema identificado y motivación](Capítulo-1/README.md#problema-identificado-y-motivación)
  - [Alcance y limitaciones](Capítulo-1/README.md#alcance-y-limitaciones-del-trabajo)
- [Marco teórico](Capítulo-1/README.md#marco-teórico)
  - [Estado del arte — soluciones existentes](Capítulo-1/README.md#exploración-de-soluciones-existentes)
  - [Por qué las soluciones existentes no son suficientes](Capítulo-1/README.md#por-qué-las-soluciones-existentes-no-son-suficientes)
  - [Solución propuesta](Capítulo-1/README.md#solución-propuesta)
  - [Objetivos general y específicos](Capítulo-1/README.md#objetivos-general-y-específicos)
  - [Metodología RUP y fases del proyecto](Capítulo-1/README.md#metodología-y-tareas)
  - [Estructura del trabajo](Capítulo-1/README.md#estructura-del-trabajo)

---

### Capítulo 2 — Requisitos

- [Modelo del dominio](Capítulo-2/ModeloDelDominio.md)
  - [Diagrama de dominio](Capítulo-2/ModeloDelDominio.md#modelo-del-dominio)
  - [Diagrama de clases](Capítulo-2/ModeloDelDominio.md#diagrama-de-clases)
  - [Diagrama de objetos](Capítulo-2/ModeloDelDominio.md#diagrama-de-objetos)
  - [Diagrama de estados del sistema](Capítulo-2/ModeloDelDominio.md#diagrama-de-estados-del-sistema)
  - [Diagrama de estados de una tarea](Capítulo-2/ModeloDelDominio.md#diagrama-de-estados-de-una-tarea)
  - [Requisitos funcionales](Capítulo-2/ModeloDelDominio.md#requisitos-funcionales)
  - [Requisitos no funcionales](Capítulo-2/ModeloDelDominio.md#requisitos-no-funcionales-suplementarios)
- [Disciplina de requisitos](Capítulo-2/DisciplinaDeRequisitos.md)
  - [Identificación de actores](Capítulo-2/DisciplinaDeRequisitos.md#11-identificación-de-actores)
  - [Lista de casos de uso](Capítulo-2/DisciplinaDeRequisitos.md#13-lista-de-casos-de-uso)
  - [Diagramas de casos de uso por actor](Capítulo-2/DisciplinaDeRequisitos.md#14-diagramas-de-casos-de-uso-por-actor)
  - [Priorización de casos de uso](Capítulo-2/DisciplinaDeRequisitos.md#2-priorizar-casos-de-uso)
  - [Detalle de casos de uso (CU-01 a CU-21)](Capítulo-2/DisciplinaDeRequisitos.md#3-detallar-casos-de-uso)
  - [Detalle de casos de uso de métricas (CU-22 a CU-32)](Capítulo-2/docs/CasosDeUsoMetricas.md)
  - [Prototipos de interfaz](Capítulo-2/DisciplinaDeRequisitos.md#4-prototipar-casos-de-uso)
  - [Diagramas de contexto y relaciones include/extend](Capítulo-2/DisciplinaDeRequisitos.md#5-estructurar-el-modelo-de-casos-de-uso)
- [Glosario](Capítulo-2/docs/Glosario.md)
  - [Glosario general](Capítulo-2/docs/Glosario.md#glosario-general)
  - [Glosario de métricas](Capítulo-2/docs/Glosario.md#glosario-de-métricas)

---

### Capítulo 3 — Análisis y diseño

- [Disciplina de análisis](Capítulo-3/Análisis.md)
  - [Análisis de la arquitectura](Capítulo-3/Análisis.md#1-análisis-de-la-arquitectura)
    - [Visión general del módulo](Capítulo-3/Análisis.md#11-visión-general-del-módulo)
    - [Restricción fundamental: solo lectura sobre Odoo](Capítulo-3/Análisis.md#12-restricción-fundamental-solo-lectura-sobre-odoo)
  - [Análisis de casos de uso](Capítulo-3/Análisis.md#2-análisis-de-casos-de-uso)
    - [CU-02 — Listar empleados](Capítulo-3/utils/Análisis_CU02.md)
    - [CU-03 — Ver resumen de empleado](Capítulo-3/utils/Análisis_CU03.md)
    - [CU-08 — Listar tareas](Capítulo-3/utils/Análisis_CU08.md)
    - [CU-10 — Mostrar catálogo de métricas](Capítulo-3/utils/Análisis_CU10.md)
    - [CU-13 — Consultar rentabilidad financiera](Capítulo-3/utils/Análisis_CU13.md)
    - [CU-17 — Guardar snapshot](Capítulo-3/utils/Análisis_CU17.md)
    - [CU-19 — Consultar detalle de snapshot](Capítulo-3/utils/Análisis_CU19.md)
    - [CU-22 — Consultar productividad](Capítulo-3/utils/Análisis_CU22.md)
  - [Análisis de clases](Capítulo-3/Análisis.md#3-análisis-de-clases)
    - [Clases Entidad](Capítulo-3/Análisis.md#clases-entidad-del-dominio-operativo-y-documental)
    - [Clases Vista (Boundary)](Capítulo-3/Análisis.md#clases-vista-boundary--páginas-de-las-aplicaciones)
    - [Clases Control](Capítulo-3/Análisis.md#clases-control)
  - [Análisis de paquetes](Capítulo-3/Análisis.md#4-análisis-de-paquetes)
    - [Subsistemas conceptuales](Capítulo-3/Análisis.md#41-subsistemas-conceptuales)
    - [Paquetes del backend](Capítulo-3/Análisis.md#42-paquetes-de-análisis-del-backend)
    - [Paquetes de los frontends](Capítulo-3/Análisis.md#43-paquetes-de-análisis-de-los-frontends)
- [Disciplina de diseño](Capítulo-3/Diseño.md)
  - [Diseño de la arquitectura](Capítulo-3/Diseño.md#5-diseño-de-la-arquitectura)
    - [Diagrama de arquitectura del sistema](Capítulo-3/Diseño.md#51-diagrama-de-arquitectura-del-sistema)
    - [Patrón arquitectónico](Capítulo-3/Diseño.md#52-patrón-arquitectónico)
    - [Stack tecnológico](Capítulo-3/Diseño.md#53-stack-tecnológico)
    - [Seguridad — JWT y RBAC](Capítulo-3/Diseño.md#54-seguridad-jwt-y-rbac)
  - [Diseño de casos de uso](Capítulo-3/Diseño.md#6-diseño-de-casos-de-uso)
    - [CU-02 — Listar empleados](Capítulo-3/utils/Diseño_CU02.md)
    - [CU-03 — Ver resumen de empleado](Capítulo-3/utils/Diseño_CU03.md)
    - [CU-04 — Listar departamentos](Capítulo-3/utils/Diseño_CU04.md)
    - [CU-08 — Listar tareas](Capítulo-3/utils/Diseño_CU08.md)
    - [CU-10 — Mostrar catálogo de métricas](Capítulo-3/utils/Diseño_CU10.md)
    - [CU-13 — Consultar rentabilidad financiera](Capítulo-3/utils/Diseño_CU13.md)
    - [CU-17 — Guardar snapshot](Capítulo-3/utils/Diseño_CU17.md)
    - [CU-19 — Consultar detalle de snapshot](Capítulo-3/utils/Diseño_CU19.md)
    - [CU-22 — Consultar productividad](Capítulo-3/utils/Diseño_CU22.md)
  - [Diseño de clases](Capítulo-3/Diseño.md#7-diseño-de-clases)
    - [Frontend principal → Routes](Capítulo-3/Diseño.md#71-frontend-principal--routes)
    - [Visor de snapshots → Routes](Capítulo-3/Diseño.md#72-visor-de-snapshots--routes)
    - [Routes → Services](Capítulo-3/Diseño.md#73-routes--services)
    - [Servicios de dominio → Repositorios base](Capítulo-3/Diseño.md#74-servicios-de-dominio--repositorios-base)
    - [Servicios de métricas → Repositorios de métricas](Capítulo-3/Diseño.md#75-servicios-de-métricas--repositorios-de-métricas)
    - [Repositorios base → Modelos](Capítulo-3/Diseño.md#76-repositorios-base--modelos)
    - [Repositorios de métricas → Modelos](Capítulo-3/Diseño.md#77-repositorios-de-métricas--modelos)
    - [Modelo de datos — PostgreSQL](Capítulo-3/Diseño.md#modelo-de-datos-del-dominio-operativo)
    - [Modelo de datos — MongoDB (snapshots)](Capítulo-3/Diseño.md#modelo-de-datos-del-subsistema-de-snapshots)
    - [Esquemas de validación Pydantic](Capítulo-3/Diseño.md#79-esquemas-de-validación)
  - [Diseño de paquetes](Capítulo-3/Diseño.md#8-diseño-de-paquetes)
    - [Estructura de paquetes backend y frontend](Capítulo-3/Diseño.md#81-estructura-de-paquetes)
    - [Principios SOLID aplicados](Capítulo-3/Diseño.md#82-principios-aplicados-en-la-organización-de-paquetes)
    - [Métricas de calidad](Capítulo-3/Diseño.md#83-métricas-de-calidad)
  - [Prototipos de interfaz](Capítulo-3/Diseño.md#9-prototipos-de-interfaz)

---

### Capítulo 4 — Implementación

- [Diagramas de navegación](Capítulo-4/README.md#diagramas-de-navegación)
  - [Frontend principal — navegación general](Capítulo-4/README.md#parte-1--navegación-general)
  - [Frontend principal — catálogo de métricas](Capítulo-4/README.md#parte-2--catálogo-de-métricas)
  - [Visor de snapshots](Capítulo-4/README.md#frontend-2--visor-de-snapshots-puerto-3001)
- [Casos de uso implementados](Capítulo-4/README.md#casos-de-uso-implementados)
  - [CU-02 — Listar empleados](Capítulo-4/docs/CU-02.md)
  - [CU-03 — Ver resumen de empleado](Capítulo-4/docs/CU-03.md)
  - [CU-08 — Listar tareas](Capítulo-4/docs/CU-08.md)
  - [CU-10 — Catálogo de métricas](Capítulo-4/docs/CU-10.md)
  - [CU-13 — Rentabilidad financiera](Capítulo-4/docs/CU-13.md)
  - [CU-17 — Guardar snapshot](Capítulo-4/docs/CU-17.md)
  - [CU-19 — Consultar detalle de snapshot](Capítulo-4/docs/CU-19.md)
  - [CU-22 — Consultar productividad](Capítulo-4/docs/CU-22.md)
- [Catálogo de endpoints de la API (47 endpoints)](Capítulo-4/docs/endpoints.md)
- [Estructura de directorios](Capítulo-4/docs/estructuraDirectorios.md)
- [Galería de vistas de la aplicación](Capítulo-4/README.md#galería-de-vistas)

---

### Capítulo 5 — Conclusiones

- [Resultados frente a los objetivos](Capítulo-5/README.md#51-resultados-obtenidos-frente-a-los-objetivos)
  - [OE1 — Ingeniería de Requisitos](Capítulo-5/README.md#oe1--ingeniería-de-requisitos)
  - [OE2 — Análisis, diseño y arquitectura](Capítulo-5/README.md#oe2--análisis-diseño-y-arquitectura)
  - [OE3 — Implementación y validación](Capítulo-5/README.md#oe3--implementación-y-validación)
- [Impacto real en la gestión diaria de Netkia](Capítulo-5/README.md#52-impacto-real-en-la-gestión-diaria-de-netkia)
  - [Antes y después de Netkia Analytics](Capítulo-5/README.md#antes-de-netkia-analytics)
  - [Análisis cuantitativo — reducción de tiempos](Capítulo-5/README.md#análisis-cuantitativo-reducción-en-horas-de-acceso-a-información)
  - [Ejemplos concretos de valor generado](Capítulo-5/README.md#ejemplos-concretos-de-valor-generado)
  - [Cumplimiento de requisitos funcionales y no funcionales](Capítulo-5/README.md#cumplimiento-de-requisitos-funcionales-y-no-funcionales)
- [Discusión de decisiones de diseño](Capítulo-5/README.md#53-discusión-de-resultados-y-decisiones-de-diseño)
- [Limitaciones del trabajo](Capítulo-5/README.md#54-limitaciones-del-trabajo)
- [Futuras líneas de actuación](Capítulo-5/README.md#55-recomendaciones-y-futuras-líneas-de-actuación)

---

## Partes más importantes

- [Objetivo general y alcance](Capítulo-1/README.md#objetivos-general-y-específicos)
- [Modelo del dominio](Capítulo-2/ModeloDelDominio.md)
- [Catálogo de casos de uso — 32 CU en 10 paquetes](Capítulo-2/DisciplinaDeRequisitos.md#13-lista-de-casos-de-uso)
- [Arquitectura del sistema](Capítulo-3/Diseño.md#51-diagrama-de-arquitectura-del-sistema)
- [Seguridad — JWT y RBAC en tres capas](Capítulo-3/Diseño.md#54-seguridad-jwt-y-rbac)
- [Principios SOLID aplicados](Capítulo-3/Diseño.md#82-principios-aplicados-en-la-organización-de-paquetes)
- [Modelo de datos PostgreSQL y MongoDB](Capítulo-3/Diseño.md#78-modelo-de-datos)
- [Catálogo de endpoints de la API](Capítulo-4/docs/endpoints.md)
- [Solución implementada en producción](Capítulo-4/README.md)
- [Impacto operativo medido en Netkia](Capítulo-5/README.md#52-impacto-real-en-la-gestión-diaria-de-netkia)
- [Futuras líneas — planificación temporal y análisis predictivo](Capítulo-5/README.md#55-recomendaciones-y-futuras-líneas-de-actuación)