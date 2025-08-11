# Lab 01 – Blinker 

## Objetivos

1. Familiarizarse con el uso del microcontrolador ```PIC18F45K22```, el entorno de desarrollo ```MPLAB X IDE``` y el programador PICkit, mediante la implementación de un programa sencillo que controle el parpadeo de un LED utilizando salidas digitales y retardos en lenguaje ```C```.
2. Configurar un pin del PIC18F45K22 como salida digital.
3. Controlar el encendido y apagado de un LED utilizando retardos de tiempo programados en lenguaje C con el compilador ```XC8```.

## Materiales

* Microcontrolador PIC18F45K22 montado en tarjeta de desarrollo o protoboard.

* LED (cualquier color).

* Resistencia de $330$ Ω a $1$ kΩ.

* Programador (PICkit $3$, PICkit $4$ o equivalente).

* Fuente de alimentación de $5$ V --> El PICkit $3$ o $4$ puede suministrar tensión directamente al circuito (típicamente $5$ V o $3.3$ V, según se configure en MPLAB X)

* ```MPLAB X IDE``` con compilador ```XC8``` instalado.

* Cables de conexión.

##  Procedimiento

1. Realizar el montaje monstrado en la siguiente figura:

    ![pictest](/labs/figs/lab01/pictest.jpg)

2. Aceptar la tarea en Github Classroom.

3. Abrir MPLAB X y crear un nuevo proyecto para el ```PIC18F45K22```. Con el código proporcionado en Github Classroom crear un archivo ```main.c``` o ```newmain.c``` en la carpeta ```Source Files```. 

4. Compilar el programa y grabarlo en el PIC.

5. Observar el parpadeo del LED.

6. Modificar el tiempo de retardo para variar la frecuencia de parpadeo.
