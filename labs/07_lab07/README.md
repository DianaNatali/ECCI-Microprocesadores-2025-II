# Lab07: Visualización en LCD 16x2 usando módulo I²C con microcontrolador PIC

Índice

1. [Objetivos de aprendizaje](#1-objetivos-de-aprendizaje)
2. [Materiales](#2-materiales)
3. [Fundamento teórico](#3-fundamento-teórico)
4. [Procedimiento](#4-procedimiento)
5. [Entregables](#5-entregables)

-------------------------------------------

## 1. Objetivos de aprendizaje

1. Configurar el módulo I²C (```MSSP```) del ```PIC18F45K22``` en modo maestro.

2. Comunicar el PIC con una LCD $16×2$ utilizando el adaptador basado en **PCF8574**.

3. Implementar funciones para enviar comandos y caracteres vía **I²C**.

4. Mostrar mensajes en la pantalla LCD desde el programa principal.


## 2. Materiales

1. PIC18F45K22 o cualquier PIC compatible.

2. Programador/debugger PICkit 3/4.

3. Fuente de alimentación (o PICkit 3/4).

4. LCD $16×2$.

5. Módulo I²C **PCF8574**.
<p align="center">
    <img src="/labs/figs/lab07/PCF8574.png" alt="alt text" width=250 >
</p>

6. Entorno de programación MPLAB X IDE con compilador XC8.



## 3. Fundamento teórico

**I²C** es un protocolo de comunicación serial de dos hilos que utiliza una línea de datos serial (```SDA```) y una línea de reloj serial (```SCL```).

Este protocolo permite múltiples dispositivos esclavos (o periféricos) en el mismo bus de comunicación, y también puede soportar múltiples maestros que envíen y reciban comandos y datos.


**I²C** es una comunicación **half-duplex**, donde solo un controlador o un dispositivo objetivo envía datos por el bus en un momento dado. En comparación, la Interfaz Periférica Serial (**SPI**) es un protocolo **full-duplex**, donde se pueden enviar y recibir datos simultáneamente. SPI requiere cuatro líneas para la comunicación: dos líneas de datos utilizadas para enviar y recibir información hacia y desde el dispositivo objetivo. Además de la línea de reloj serial, se emplea una línea de chip select única para seleccionar el dispositivo con el que se desea comunicar, junto con las dos líneas de datos usadas para la entrada y salida del dispositivo.

La comunicación se transmite en paquetes de bytes, con una dirección única para cada dispositivo esclavo.

La siguiente figura muestra la estructura de una transferencia típica en el bus I²C, donde se observa la condición de inicio, el envío de la dirección del dispositivo con el bit de lectura/escritura, el bit de reconocimiento (```ACK```) y finalmente la transmisión de datos seguida de la condición de parada.

![I²C](/labs/figs/lab07/i2c.png)

La pantalla LCD $16\times2$ se controlará en este laboratorio mediante el protocolo I²C utilizando un expansor de puertos **`PCF8574**. Este enfoque reduce la cantidad de pines necesarios para la conexión, ya que utiliza únicamente dos líneas: ```SDA``` (datos) y ```SCL``` (reloj). 

El PIC18F45K22 cuenta con el módulo **MSSP** (Master Synchronous Serial Port), capaz de trabajar en protocolos **SPI** e **I²C**. 

El siguiente diagrama representa el funcionamiento interno del módulo MSSP configurado en modo **I²C**.

Incluye:

✔ Buffers de transmisión y recepción (SSPBUF y SSPSR)

✔ Detectores de Start/Stop, ACK, colisiones

✔ Control de reloj (Clock Gen)

✔ Generador de baudios

✔ Lógica para habilitar SDA y SCL

✔ Flags y registros de control (SSPCON1, SSPCON2, SSPSTAT, PIR1bits.SSPIF)

<p align="center">
    <img src="/labs/figs/lab07/mssp.png" alt="alt text" width=600 >
</p>


Entonces, en este laboratorio se usa en modo **I²C** Maestro para enviar datos al módulo LCD **PCF8574**.

El módulo **PCF8574** suele tener una dirección base de $7$ bits igual a $0\times27$. Sin embargo, en el protocolo **I²C**, la dirección que se transmite al bus debe tener $8$ bits, donde el último bit indica si se va a leer ($1$) o escribir ($0$). Como en este caso solo se realiza escritura hacia el LCD, la dirección efectiva enviada es $0\times4E$, que resulta de desplazar $0\times27$ una posición a la izquierda ($0\times27 << 1 $). Esta dirección ya se encuentra definida en el código como:


```
#define LCD_ADDR 0x4E
```

**¿Por qué usar I²C con la LCD?**

* Sin I²C → la LCD requiere $6$ a $8$ pines del microcontrolador.
* Con I²C → solo se requieren $2$ pines: ```SCL``` (```RC3```) y ```SCL``` (```RC4```).

## 4. Procedimiento

1. Acepte la tarea en [Github Classroom](https://classroom.github.com/a/4IVWINxo) y cree un proyecto en MPLAB X IDE con los código que se encontrarán allí.

2. Realice la respectiva documentación de los códigos en Github Classroom.

3. Elabore el siguiente montaje y realice la implementación: compile el proyecto y cargue al ``PIC``.


<p align="center">
    <img src="/labs/figs/lab07/montaje.png" alt="alt text" width=700 >
</p>


4. Realice las mismas actividades que se plantearon para el [lab04 - LCD en modo paralelo](/labs/04_lab04/README.md), pero esta vez con LCD en modo **I²C**, es decir:

    * Texto estático.
    * Desplazamiento de string.
    * Caracteres especiales.





## 5. Entregables

1. Lea la anterior guía y presente en clase la implementación al docente.

2. Realice la respectiva documentación de la implementación llevada a cabo.



