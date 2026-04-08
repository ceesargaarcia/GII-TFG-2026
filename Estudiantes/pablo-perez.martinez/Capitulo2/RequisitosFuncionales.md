# Requisitos funcionales

Los requisitos funcionales describen las funciones que debe ofrecer la solución desarrollada para dar respuesta a las necesidades identificadas en el apartado anterior. Igual que anteriormente, las dividiremos en los requisitos funcionales correspondientes al módulo de backend, para el tratamiento y cálculo de la información, y los requisitos funcionales correspondientes al módulo de frontend, centrados en la visualización y explotación de los resultados dentro de VT.

La solución debe cubrir los siguientes requisitos funcionales:
## Módulo de backend
- Obtención de datos de planificación
- Obtención de datos reales de producción
- Cálculo de indicadores: disponibilidad, calidad y rendimiento.
- Cálculo del OEE.
- Realización de cálculos filtrando por fechas.
- Suministro de datos al frontend.
## Módulo de frontend
- Comunicación con backend para recibir datos.
- Representación gráfica de los resultados:
    - Gráfico de líneas y puntos para producción por horas.
    - Gráficos de barras para visualización de OEE, información de tiempos e información de producto fabricado o por fabricar.
    - Información emergente de tipos de paradas y motivos de rechazos en barras de tiempos y productos.
    - Tabla comparativa entre valores teóricos y reales del OEE e indicadores.
- Comparación entre valores teóricos y reales.
- Selector de partición (split) para la que mostrar información de producción..


## Especificación de requisitos funcionales
|Código | Descripción |
|:---:|:---|
|RF01 | Obtención de los datos teóricos de planificación de la base de datos de VT. Tiempo de ciclo a partir del puesto de trabajo y objetivo de producción a partir de datos de actividad. |
|RF02 | Obtención de los datos reales de producción de la base de datos. Información de actividades, productos completados, rechazos, microparadas, ejecuciones… |
|RF03 | Cálculo de disponibilidad a partir de información de tiempos. |
|RF04 | Cálculo de rendimiento a partir de información de tiempos. |
|RF05 | Cálculo de calidad a partir de información de cantidades producidas y rechazos. |
|RF06 | Cálculo de OEE a partir de disponibilidad, rendimiento y calidad. |
|RF07 | Realización de cálculos con filtro por fecha de inicio y/o fecha de fin. |
|RF08 | Suministro al frontend de los datos calculados y la información a representar. |
|RF09 | Comunicación con backend para solicitud y recepción de los cálculos realizados. |
|RF10 | Implementación de gráfico de líneas y puntos para visualización gráfica de la producción por horas. |
|RF11 | Implementación de gráficos de barras para visualización de OEE, información de tiempos e información de producto fabricado o por fabricar. |
|RF12 | Tooltip (cuadro de texto) informativo de tipos de paradas en gráfica de barras de tiempos. |
|RF13 | Tooltip informativo de motivos de rechazo en gráfica de barras de producto fabricado o por fabricar. |
|RF14 | Tabla comparativa entre valores reales y teóricos de los indicadores del OEE. |
|RF15 | Selector de split (partición) para la que realizar los cálculos. |


