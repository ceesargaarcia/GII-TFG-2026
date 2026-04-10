# Descripción de casos de uso

## CU-01: Obtención de datos de producción

### Actor principal
Visual Tracking


### Precondición
La orden de trabajo ha sido creada en VT, tiene una o más actividades, ha pasado la fase de asignación y se encuentra en fase de producción. La fase de asignación es el momento en que se asigna a cada actividad de la orden un puesto de trabajo y una fecha y hora para el inicio de su ejecución, en función de la disponibilidad de los puestos de trabajo que incluye y de las duraciones y disponibilidad entre sí de las actividades. Además, se dispone de un splitId del que sacar la información, y parámetros opcionales como fecha de inicio y fecha de fin. 


### Flujo principal: 
1. Al cambiar a la pestaña “Rendimiento” en fase de producción, VT hace una llamada a su API.
2. Dicha llamada utiliza el splitId de la primera partición de la orden como parámetro por defecto. Las fechas de inicio y fin por defecto son tomadas desde la fecha de inicio de la partición hasta la fecha actual respectivamente.
3. El sistema consulta los servicios y las entidades correspondientes en la base de datos para obtener la información relacionada con el splitId. Esto incluye:
    - Datos de paradas de la estación de trabajo.
    - Artículos producidos.
    - Actividades realizadas durante la producción.
    - Procedimientos indirectos involucrados.
    - Salidas de fabricación.
    - Rechazos generados.
    - Ejecuciones de la producción.
    - Microparadas detectadas.
4. Los datos obtenidos se almacenan temporalmente para su posterior uso en la realización de cálculos de OEE e indicadores.


### Flujo alternativo: 
  - Error al realizar la llamada a la API. Puede ser provocado por un fallo de la propia API (problemas de red, respuesta incorrecta del servidor…) o bien por un error en los parámetros de la llamada (falta de parámetros obligatorios, formato incorrecto, splitId no existente…).
  - Error en la obtención de datos. La conexión con la base de datos se ha realizado correctamente pero ha surgido algún error ajeno a ella. Puede venir provocado por inconsistencia de datos, timeout  (tiempo excesivo en carga de datos), errores en la validación… 

### Postcondición
Se dispone de toda la información teórica y real necesaria para realizar los cálculos en la fase posterior.


## CU-02: Cálculo del OEE

### Actor principal
Visual Tracking


### Precondición
Se dispone de toda la información teórica y real necesaria obtenida en el CU-01 para la partición seleccionada. 


### Flujo principal: 
1. El sistema crea una estructura de resultados vacía asociada al splitId sobre la que almacenar los resultados.
2. Dicha estructura almacenará información de fabricación de piezas, información de fabricación de tiempo, información de fabricación de ejecuciones e información de fabricación de indicadores.
3. Se realizará una agrupación eficiente y clara de la información de fabricación de piezas, información de fabricación de tiempos e información de fabricación de ejecuciones . Además se agrupará en un parámetro la información de fabricación por horas desde la fecha de inicio pasada junto al splitId.
4. Se realizará el cálculo de la disponibilidad a partir de la información de tiempos.
5. Se realizará el cálculo del rendimiento a partir de la información de tiempos.
6. Se realizará el cálculo de la calidad a partir de la información de cantidades producidas y rechazos.
7. Se realizará el cálculo del OEE  a partir de la disponibilidad, rendimiento y calidad.
8. Se añadirá la información de indicadores a la estructura de resultados asociada al splitId.


### Flujo alternativo: 
  - Falta o error de datos:
    
    - No hay producción. Se establecerá calidad a 1 (100%) para evitar división entre 0.
    
    - Tiempo transcurrido es 0. Se establecerá disponibilidad a 1 (100%) para evitar división entre 0.
    
    - Tiempo de ciclo es 0. Se establecerá rendimiento a 1 (100%) para evitar división entre 0.

### Postcondición
Los cálculos han sido realizados correctamente y se dispone de una estructura de datos (ManufacturingIndexInfoBySplit) con toda la información necesaria para mostrar al usuario gráficamente en fases posteriores. Dicha información contiene información de fabricación de tiempos, información de fabricación de piezas, información de producción de ejecuciones, información de ejecución de indicadores y un array de la producción por hora.


## CU-03: Comunicación entre backend y frontend.
### Actor principal
Visual Tracking

### Precondición


Habrá 2 precondiciones, una para cada sentido de la comunicación:
- De back a front: se dispone de la estructura de datos ManufacturingIndexInfoBySplit obtenida en el CU-02 con la información calculada y clasificada correctamente.
- De front a back: se dispone de un splitId proporcionado por parte del responsable de producción mediante el CU-05.

### Flujo principal


Habrá 2 flujos principales, uno para cada sentido de la comunicación:  
- De back a front
1. El back devuelve al front la estructura ManufacturingIndexInfoBySplit, que contiene la información de ejecuciones, piezas, tiempos, indicadores y curva de velocidad calculada para la partición seleccionada.
2. El front recibe la respuesta y avanza al siguiente caso de uso (CU-04) para la interpretación y graficación de resultados.  
- De front a back
3. Desde el filtro del front se selecciona un splitId diferente al actual.  
4. El front construye una solicitud y se la hace al back para obtener los datos de la nueva partición seleccionada.  
5. El back recibe la petición y vuelve al CU-01.

### Flujo alternativo  
- Error en la comunicación de back a front: el back procesa la solicitud pero la respuesta no puede devolverse o interpretarse correctamente debido a fallos internos, estructura de datos incompleta o error en el tratamiento de la respuesta en frontend.  
- Error en la comunicación de front a back: la solicitud no puede enviarse correctamente por problemas de red, formato incorrecto o inexistencia del splitId solicitado.

### Postcondición  
- La información calculada correspondiente al splitId seleccionado queda correctamente transferida del back al front y utilizada en el siguiente caso de uso (CU-04) para su interpretación y visualización.  
- Se vuelve a procesar la solicitud en el CU-01 y calcula los datos de la nueva partición seleccionada.



## CU-04: Visualización de datos  
### Actores  
Visual Tracking y responsable de producción  
### Precondición  
La comunicación con el backend ha sido exitosa y ya se dispone de la información necesaria en la sección de frontend para proceder a graficarla.  
### Flujo principal  
En la pestaña de rendimiento de la sección de producción, VT mostrará las siguientes gráficas:  
1. Curva de velocidad con la producción por hora: gráfica de puntos y líneas. En el eje x las horas transcurridas desde que comenzó la partición y en el eje y la producción por hora en las unidades del material a producir (ej. unidades, kg, L, cajas…).
2. Gráficas de barras: 3 gráficas de barras horizontales con la siguiente información:
	- OEE actual: mostrará el valor porcentual del OEE en ese instante.
	- Tiempo transcurrido: mostrará los tiempos: producido, pendiente, de paradas y de indirectos, respecto al tiempo total planificado de la partición.
	- Total fabricado: mostrará la cantidad producida, pendiente y los rechazos respecto al objetivo total de fabricación de la partición.
3. Tabla comparativa entre valores reales y teóricos de los indicadores del OEE.
4. Cuadro de texto con gráfica de barras informativo sobre los tipos de paradas, que se despliega al colocar el cursor sobre la parte de paradas en la barra de tiempo transcurrido.
5. Cuadro de texto con gráfica de barras informativo sobre los tipos de rechazos, que se despliega al colocar el cursor sobre la parte de rechazos en la barra de total fabricado.
Además de las gráficas, habrá un selector de partición que permitirá cambiar entre todas las particiones de cada actividad de la orden de trabajo.
El responsable de producción analiza los datos mediante gráficas. Las únicas interacciones que puede llevar a cabo son:
	1. Colocar el cursor sobre las paradas en la barra de tiempo transcurrido.
 	2. Colocar el cursor sobre los rechazos en la barra de total fabricado.
  	3. Pinchar sobre el selector de partición: CU-05. 
### Postcondición
- VT muestra correctamente las gráficas con la información de la partición seleccionada.
- El responsable de producción analiza toda la información de la partición deseada mediante la visualización de las gráficas, tabla y tooltips.

## CU-05: Filtrado de datos  
### Actores  
Visual Tracking y responsable de producción  
### Precondición  
El responsable de producción se encuentra en la pestaña de rendimiento de VT.  
### Flujo principal  
1. El responsable de producción hace click sobre el botón del selector de partición.
2. VT muestra en un desplegable todas las particiones de todas las actividades de la orden actual.
3. El responsable de producción selecciona una partición distinta a la actual.
### Flujo alternativo  
El responsable de producción no cambia de partición y VT sigue mostrando la información de la partición anterior.  
### Postcondición  
VT hace una llamada a back (CU-03) con el splitId de la nueva partición seleccionada por el responsable de producción.


