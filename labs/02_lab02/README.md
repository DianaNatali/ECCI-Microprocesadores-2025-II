# Lab 02 – Caracterización de osciladores (externo vs. interno)

## Índice:

#### 1. [Objetivos de aprendizaje](#1-objetivos-de-aprendizaje)

#### 2. [Herramientas](#2-herramientas)

#### 3. [Fundamento teórico](#3-fundamento-teórico)

#### 4. [Procedimiento](#4-procedimiento)

#### 5. [Entregables](#5-entregables)

#### 6. [Referencias](#6-referencias)



-----------------------------------

## 1. Objetivos de aprendizale

1. Configurar el ```PIC18F45K22``` (o la referencia de PIC seleccionada) para operar con:

    - Oscilador externo basado en cristal de cuarzo.

    - Oscilador interno (```INTOSC```).

2. Verificar la frecuencia real de la CPU generando una señal de referencia en un pin de salida y midiendo dicha señal con un osciloscopio.

3. Comparar ambos modos de operación (cristal vs. ```INTOSC```) en cuanto a:

    - Precisión absoluta de la frecuencia.

    - Estabilidad temporal de la señal.

    - Efectos de la deriva térmica sobre la frecuencia.

## 2. Herramientas 

* ```PIC18F45K22``` (o la referencia de PIC seleccionada) en tarjeta de desarrollo o protoboard.

* Cristal 16 MHz (puede ser 8–20 MHz; se recomienda 16 MHz).

* 2× capacitores de $18$ pF - $27$ pF.

* LED + resistencia $330$ Ω – $1$ kΩ.

* Programador (PICkit $3$, PICkit $4$ o equivalente).

* Fuente de alimentación de $5$ V → El PICkit $3$ o $4$ puede suministrar tensión directamente al circuito (típicamente $5$ V o $3.3$ V, según se configure en MPLAB X)
MPLAB X IDE con compilador XC8.

* Osciloscopio.

* Cables de conexión.

## 3. Fundamento teórico

### Oscilador

Un oscilador es necesario porque en casi todos los sistemas electrónicos (microcontroladores, microprocesadores, circuitos de comunicación, etc.) se requiere una señal periódica estable que sirva como referencia de tiempo o reloj. A continuación se enlistan las funciones principales de un oscilador

#### Sincronización

1. En un microcontrolador o procesador, el oscilador genera la señal de reloj que marca el “ritmo” de ejecución de las instrucciones.

2. Sin un reloj, el procesador no sabría cuándo avanzar de una instrucción a la siguiente.

3. Referencia de tiempo.

4. Para temporizadores, contadores, protocolos de comunicación (UART, I²C, SPI, CAN, USB, etc.), se necesita una frecuencia precisa para medir intervalos de tiempo o mantener la velocidad de transmisión correcta.

5. Generación de señales.


En la imagen se muestra la arquitectura de selección de reloj en el microcontrolador ```PIC18F45K22```. En ella se evidencia cómo el microcontrolador puede obtener la señal de reloj, ya sea desde una fuente externa o interna, y cómo se controla esta selección.

<p align="center">
<img src="/labs/figs/lab02/oscillator.png" alt="esp11" width="380">
</p>
<p align="center">
  Fig 1. Arquitectura de selección de reloj <b>[1]</b>
</p>

### Partes principales:


#### 1. Oscilador externo:

Un oscilador externo es aquel que no se encuentra integrado dentro del microcontrolador, sino que, en el caso del PIC, se conecta a los pines ```OSC1``` y ```OSC2```. Se denomina “externo” porque requiere componentes adicionales para generar y estabilizar la señal de reloj. Estos componentes pueden ser: 

* Un cristal de cuarzo
* Un resonador cerámico 
* En configuraciones más simples, un circuito RC (resistencia-capacitor). 

Por lo tanto, puede funcionar en distintos modos según el cristal o circuito usado:

* **LP** (Low Power Crystal): cristal de baja frecuencia y bajo consumo.

* **XT** (Crystal/Resonator): cristal típico de 4 MHz a 10 MHz.

* **HS** (High-Speed Crystal): cristales de alta frecuencia (hasta decenas de MHz).

<p align="center">
<img src="/labs/figs/lab02/external1.png" alt="osc1" width="350">
</p>
<p align="center">
  Fig 2. Oscilador externo (modos LP, XT y HS) <b>[1]</b>
</p>

* **RC**: oscilador empleando un circuito RC externo.

<p align="center">
<img src="/labs/figs/lab02/RC.png" alt="osc2" width="380">
</p>
<p align="center">
  Fig 3. Oscilador externo modo RC <b>[1]</b>
</p>

* **EC**: señal de reloj externa aplicada directamente.

<p align="center">
<img src="/labs/figs/lab02/external2.png" alt="osc2" width="350">
</p>
<p align="center">
  Fig 4. Señal externa <b>[1]</b>
</p>

#### 2. Oscilador interno:

El oscilador interno del ```PIC18F45K22``` está compuesto por dos bloques principales:

- ```HFINTOSC``` (High-Frequency Internal Oscillator): es un oscilador interno de alta frecuencia calibrado de fábrica, cuyo valor nominal es $8$ MHz. El microcontrolador puede trabajar directamente con esta frecuencia o con versiones reducidas mediante el divisor de prescalador.

- ```LFINTOSC``` (Low-Frequency Internal Oscillator): es un oscilador interno de baja frecuencia calibrado a $31$ kHz. Suele emplearse para temporizadores internos, como el ```Timer de encendido``` (Power-up Timer) y el perro guardián (Watchdog Timer), aunque también puede seleccionarse como fuente de reloj principal para todo el microcontrolador cuando se prioriza bajo consumo.

La elección entre usar un oscilador interno o externo se controla mediante el bit ```SCS``` (System Clock Select) en el registro 
```OSCCON```, el cual define cuál será la fuente activa de reloj del sistema.

#### 3. Divisor de frecuencia (Divisor 1/N):

Permite reducir la frecuencia de ```HFINTOSC```. Los valores posibles son:

$8$ MHz → $4$ MHz → $2$ MHz → $1$ MHz → $500$ kHz → $250$ kHz → $125$ kHz → $31$ kHz.

La selección se hace con los bits ```IRCF2:IRCF0``` del registro ```OSCCON```.

#### 4. Registro OSCCON:

* Bits ```IRCF2```, ```IRCF1```, ```IRCF0```: seleccionan la frecuencia del oscilador interno.

* Bit ```SCS``` (System Clock Select): selecciona la fuente de reloj del sistema (interna o externa).

<p align="center">
<img src="/labs/figs/lab02/osccon.png" alt="osc2" width="380">
</p>
<p align="center">
  Fig 5. Registro OSCCON <b>[1]</b>
</p>

#### 5. CPU 

Recibe la señal de reloj seleccionada y la usa para ejecutar instrucciones.

Adicionalmente cuenta con lógica de protección (temporizador de arranque, perro guardián (*watchdog*), monitor de fallo de reloj) que supervisa el funcionamiento del reloj.

### Estabilidad y exactitud

El oscilador interno de un microcontrolador suele ser suficiente para aplicaciones básicas, pero puede ser impreciso (deriva con la temperatura).

Por eso a veces se usa un cristal externo (ej. 16 MHz, 20 MHz) para tener una frecuencia mucho más exacta.

## 4. Procedimiento

1. Aceptar la tarea en GitHub Classroom.

2. Crear proyecto en ```MPLAB X``` para PIC18F45K22 (o referencia seleccionada) y agregar el ```main.c``` base del ítem anterior.

3. Realizar los montajes:

    * Con oscilador externo con sus capacitores y el circuito mínimo de la [figura 2](https://github.com/DianaNatali/ECCI-Microprocesadores-2025-II/blob/main/labs/figs/lab02/external1.png). 

    * Configurando el oscilador interno.

    * Con circuito RC como se muestra en la [figura 3](https://github.com/DianaNatali/ECCI-Microprocesadores-2025-II/blob/main/labs/figs/lab02/RC.png).

4. Compilar y grabar el programa en el PIC.

5. Observar el parpadeo del LED. Se debería observar una frecuencia de ~$500$ Hz (período ~$2$ ms medido en ```RC0```).

6. Medir con osciloscopio en ```RC0``` y registrar la frecuencia.

7. Cambiar a oscilador interno (```INTOSC```).

8. Deriva térmica:

    - Calentar levemente el encapsulado (con la mano o aire tibio) y observa cambio de frecuencia en el osciloscopio.


9. Comparar la frecuencia medida en el osciloscopio con la frecuencia teórica esperada (ej. $500$ Hz).

    - Calcular el porcentaje de error utilizando la fórmula:

    \[
\text{Error (\%)} = \frac{f_{medida} - f_{teórica}}{f_{teórica}} \times 100
\]


## 5. Entregables

1. Realice el [procedimiento](#4-procedimiento) y presente en clase las implementaciones de cada una al docente.

2. Realice la respectiva documentación de la implementación llevada a cabo en su respectivo repositorio en Github Classroom.


## 6. Referencias

**[1]** Verle, M. (s.f.). Oscilador de Reloj. En Microcontroladores PIC – Programación en C con ejemplos. MikroElektronika. [Online]:  https://www.mikroe.com/ebooks/microcontroladores-pic-programacion-en-c-con-ejemplos/oscilador-de-reloj

**[2]** Microchip Technology Inc. (s.f.). PICmicro Mid-Range MCU Family Reference Manual (Documento DS33023A). [Online]: de https://ww1.microchip.com/downloads/en/devicedoc/33023a.pdf