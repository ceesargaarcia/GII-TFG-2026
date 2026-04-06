# Disciplina de Requisitos
## Actores
| Diagrama | Código Fuente |
|----------|---------------|
|![Actores](./Actores/imagen/Actores.png)|[Ver Código de Actores](./Actores/codigo/Actores.puml)

El diagrama de actores representa los elementos externos que interactúan con el sistema y cómo se relacionan entre sí dentro del flujo de funcionamiento. En este caso, se identifican tres actores principales: el Cliente, el Técnico y el sistema automatizado implementado mediante Microsoft Power Automate.

El Cliente es el encargado de iniciar el proceso mediante el envío de solicitudes, como consultas o incidencias. Estas son procesadas por el sistema automatizado, que actúa como núcleo del sistema, gestionando la información, coordinando el flujo de trabajo y automatizando las respuestas. En aquellos casos en los que es necesaria intervención humana, el sistema asigna la solicitud al Técnico, quien se encarga de su gestión y resolución.

El funcionamiento del sistema sigue una estructura cíclica, en la que, tras la intervención del Técnico, la información se actualiza en el sistema y se genera una respuesta que es enviada de nuevo al Cliente, cerrando el ciclo y permitiendo su repetición continua.

## Casos De Uso Por Actor

| Cliente | Técnico | Power Automate | 
|---------|---------|----------------|
|![CdU_Cliente](./CdU/CdU_Cliente/imagen/CdU_Cliente.png)|![CdU_Tecnico](./CdU/CdU_Tecnico/imagen/CdU_Tecnico.png)|![CdU_PowerAutomate](./CdU/CdU_PowerAutomate/imagen/CdU_PowerAutomate.png)|
|[Ver código](./CdU/CdU_Cliente/codigo/CdU_Cliente.puml)|[Ver código](./CdU/CdU_Tecnico/codigo/CdU_Tecnico.puml)|[Ver código](./CdU/CdU_PowerAutomate/codigo/CdU_PowerAutomate.puml)

## Diagrama de Contexto por Actor

### Diagrama de Contexto del Cliente    
