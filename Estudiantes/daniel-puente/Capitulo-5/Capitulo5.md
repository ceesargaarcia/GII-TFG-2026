# Capitulo 5
## Conclusion

El desarrollo del TFG ha permitido comprobar que la solucion propuesta en el capitulo 1 es viable para digitalizar la operativa interna del Restaurante La Union. El resultado no se limita a trasladar el papel a una pantalla, sino que incorpora reglas de negocio propias del restaurante: control de mesas, comandas por pases, KDS de cocina, caja separada del cierre de mesa, auditoria y soporte offline.

Los casos de uso seleccionados para el desarrollo  quedan cubiertos por el MVP:

| Caso de uso | Evidencia de cumplimiento |
|---|---|
| Abrir mesa | La mesa queda asociada a una comanda activa y pasa a estado ocupada |
| Tomar comanda | El camarero registra platos, cantidades, observaciones y alergenos |
| Editar comanda | Solo se modifican lineas pendientes, evitando cambios sobre platos ya trabajados por cocina |
| Marcar plato como listo | Cocina cambia el estado desde KDS y sala recibe el estado actualizado |
| Enviar ticket a caja | El sistema genera el ticket desde la comanda activa |
| Cobrar ticket en caja | El administrador registra el cobro y evita dobles cobros |
| Cerrar mesa | La mesa solo se libera cuando existe un ticket cobrado |

## Discusion de resultados

El principal resultado del proyecto es que la aplicacion consigue adaptar la tecnologia a la forma real de trabajar del restaurante. Algunas decisiones tecnicas han sido especialmente relevantes:

| Decision tomada | Implicacion |
|---|---|
| PWA en lugar de app nativa | Permite usar tablets o equipos con navegador sin depender de tiendas de aplicaciones |
| Modelo local-first con cola offline | Reduce el riesgo de parar el servicio ante cortes puntuales de red |
| PC local de la empresa | Aprovecha infraestructura existente y evita comprar un servidor para el MVP |
| MongoDB Atlas gratuito | Permite validar el sistema sin coste mensual inicial |
| KDS por estados | Reduce comunicacion verbal entre sala y cocina |
| Pases de menu | Evita que cocina reciba primeros, segundos y postres al mismo tiempo |
| Auditoria | Permite reconstruir incidencias sobre comandas, tickets, reservas y usuarios |

Durante la prueba de funcionamiento del MVP se observaron mejoras respecto al proceso manual previo. Las metricas siguientes recogen resultados aproximados obtenidos al comparar el flujo tradicional con el uso de la aplicacion en los casos de uso principales.

## Metricas cuantitativas obtenidas

| Indicador | Situacion anterior | Con la aplicacion |
|---|---|---|
| Apertura de comanda | 20-30 segundos localizando mesa y preparando nota | 5-8 segundos desde Sala |
| Registro de un plato | 10-20 segundos entre nota y comunicacion a cocina | 5-10 segundos desde Comanda |
| Localizacion del estado de una mesa | 20-40 segundos preguntando o revisando papel | 3-5 segundos desde Sala |
| Generacion de ticket | Calculo manual o revision de comandas | Ticket calculado automaticamente |
| Trazabilidad de acciones criticas | Baja o inexistente | Registro de auditoria por usuario y fecha |
| Consumo de papel y boligrafos | Uso diario de comanderos, blocs y boligrafos | Reduccion progresiva mediante comandas digitales |
| Coste inicial de infraestructura | Posible compra de servidor o TPV comercial | Uso de PC local existente y plan gratuito de MongoDB Atlas |

Estas metricas no proceden de una implantacion masiva durante meses, sino de la prueba funcional del MVP y de la comparacion con el procedimiento habitual del restaurante. Por ello, sirven como primera evidencia de mejora y como base para una medicion mas prolongada cuando el sistema se utilice de forma continuada.

## Metricas cualitativas

| Area | Mejora cualitativa observada |
|---|---|
| Sala | Los camareros pueden ver el estado de cada mesa sin depender de notas o comunicacion verbal |
| Cocina | El KDS ordena el trabajo por estados y evita enviar platos de menu antes de tiempo |
| Caja | El cobro queda separado del cierre de mesa, reduciendo errores administrativos |
| Administracion | Usuarios, carta, reservas y auditoria quedan centralizados |
| Seguridad alimentaria | Los alergenos y observaciones quedan visibles en comanda y cocina |
| Cliente final | Menos esperas, menos errores de platos y mejor coordinacion del servicio |
| Negocio | Menor dependencia del papel, mayor control de incidencias y base de datos historica para futuras decisiones |

Aunque el ahorro en papel, comanderos y boligrafos no representa el mayor coste del negocio, si supone una mejora continua: se reduce material fungible, se evitan comandas ilegibles o extraviadas y se disminuye la dependencia del soporte fisico durante los servicios de mayor carga.

## Recomendaciones

A partir de los resultados obtenidos, se recomienda abordar la implantacion de forma gradual. El sistema ya cubre el flujo principal, pero conviene mejorarlo aun mas antes de utilizarlo de forma completa en todos los turnos.

| Recomendacion | Motivo |
|---|---|
| Formar primero a camareros, cocina y administracion | Reduce rechazo al cambio y errores por uso incorrecto |
| Mantener el PC local como servidor del negocio | Aprovecha un recurso ya disponible y mantiene baja la inversion inicial |
| Valorar un plan de pago de MongoDB Atlas a corto plazo | Mejoraria copias de seguridad, capacidad y garantias de disponibilidad |

## Futuras lineas de actuacion

El proyecto se ha desarrollado de forma metodologica, separando requisitos, analisis, diseño e implementacion. Esto facilita continuar el trabajo sin rehacer la base del sistema, ya que las funcionalidades futuras pueden incorporarse sobre una arquitectura modular.

| Prioridad | Linea futura | Objetivo |
|---|---|---|
| Alta | Panel de analitica de caja y ventas | Conocer ticket medio, platos mas vendidos y ventas por dia |
| Alta | Plan superior de MongoDB Atlas | Mejorar capacidad, copias de seguridad y disponibilidad |
| Alta | Impresion de tickets | Integrar la aplicacion con la operativa fisica de caja |
| Alta | Mejor gestion de conflictos offline | Resolver casos donde dos dispositivos cambien el mismo dato |
| Media | Temporizadores en KDS | Detectar retrasos de cocina por mesa o plato |
| Media | Sugerencia automatica de mesas para reservas | Mejorar ocupacion y rotacion del restaurante |
| Baja | Estadisticas por camarero | Analizar carga de trabajo y rendimiento |


## Conclusion final

El TFG cumple su objetivo principal: desarrollar una PWA capaz de digitalizar la gestion interna del Restaurante La Union respetando su forma real de trabajo. El sistema mejora la coordinacion entre sala, cocina y caja, reduce errores operativos, disminuye la dependencia del papel y crea una base tecnica preparada para crecer.

La solucion parte de una implantacion realista: PC local ya disponible, MongoDB Atlas en plan gratuito durante la fase inicial y posibilidad de contratar un plan superior cuando el uso sea mas continuado.
