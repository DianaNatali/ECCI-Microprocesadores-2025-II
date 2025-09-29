# Lab03: Generación de señal PWM con PIC (usando timmers, interrupciones ADC)

## Índice:

#### 1. [Objetivos de aprendizaje](#1-objetivos-de-aprendizaje)

#### 2. [Herramientas](#2-herramientas)

#### 3. [Fundamento teórico](#3-fundamento-teórico)

#### 3.1. [PWM](#31-pwm)
#### 3.2. [En el PIC](#32-en-el-pic)

#### 4. [Procedimiento](#4-procedimiento)

#### 4.1. [Parte 1](#41-parte-1)
#### 4.2. [Parte 2](#42-parte-2)


#### 5. [Entregables](#5-entregables)

#### 6. [Referencias](#6-referencias)

------------------------------------------------------


## 1. Objetivos de aprendizaje


1. Implementar una señal PWM usando el módulo ```CCP1```, ajustando parámetros como el periodo y el ciclo útil (*duty cycle*) a través del temporizador 2.

2. Configurar ```Timer0``` como temporizador de 16 bits con prescaler, para generar eventos periódicos de temporización con precisión.

3. Usar interrupciones de ```Timer0``` para modificar automáticamente el ciclo útil del PWM, demostrando el control dinámico mediante hardware e interrupciones.

4. Leer el valor de un potenciómetro mediante el módulo ```ADC``` del microcontrolador y usarlo para ajustar el ciclo útil del PWM en tiempo real.


## 2. Herramientas


* Microcontrolador ```PIC18F45K22``` (o la referencia de PIC seleccionada) en tarjeta de desarrollo o protoboard.

* LED + resistencia $330$ Ω – $1$ kΩ.

* Potenciómetro

* Programador (PICkit $3$, PICkit $4$ o equivalente).

* Entorno de programación MPLAB X IDE con compilador XC8.

* Fuente de alimentación de $5$ V → El PICkit $3$ o $4$ puede suministrar tensión directamente al circuito (típicamente $5$ V o $3.3$ V, según se configure en MPLAB X)

* Osciloscopio.

* Protoboard y cables de conexión.


## 3. Fundamento teórico 

### 3.1 PWM

PWM (*Pulse Width Modulation*) o Modulación por Ancho de Pulsos es una técnica ampliamente utilizada para controlar la potencia entregada a dispositivos eléctricos mediante la variación del ancho de los pulsos en una señal digital de alta frecuencia. Esta modulación se utiliza para controlar la cantidad de energía que se suministra a una carga, como un motor, un LED o incluso en sistemas de regulación de voltaje.

<div align="center">
 <img src="/labs/figs/lab03/pwm.png" alt="pwm" width="600" />
</div>


**Características clave del PWM**:

1. **Frecuencia**:
Es la cantidad de ciclos completos de la señal por segundo. La frecuencia de la señal PWM determina cuántos pulsos por segundo se envían al dispositivo. La frecuencia es importante porque debe ser alta lo suficiente como para que el ojo humano no perciba el parpadeo o el zumbido de los dispositivos, como un LED o un motor. Sin embargo, debe ser lo suficientemente baja como para no generar interferencia o ruido eléctrico.

$$ f = \dfrac{1}{\text{período}(T)} = \dfrac{1}{f}$$

2. **Ciclo de trabajo (Duty Cycle)**:
 El ciclo de trabajo es el porcentaje del tiempo durante el cual la señal permanece en un nivel alto (encendida) durante un ciclo. Se calcula como la fracción del tiempo en que la señal está en estado alto con respecto al tiempo total del ciclo.


 $$ \text{Ciclo de trabajo} = \dfrac{\text{Tiempo en alto}}{\text{Período} (T)} \times 100 $$  


3. **Frecuencia y ciclo de trabajo**: La frecuencia y el ciclo de trabajo están relacionados, pero son independientes en cuanto al control. Mientras que la frecuencia controla cuántos ciclos se repiten por segundo, el ciclo de trabajo controla la cantidad de tiempo que la señal permanece "encendida" dentro de cada ciclo.


<div align="center">
 <img src="/labs/figs/lab03/PWM02.gif" alt="pwm" width="550" />
 </div>
 
 Tomado de **[3]**


### En el PIC 

#### Módulo PWM (CCP)

El módulo ```CCP1``` permite generar una señal PWM cuyo periodo se define mediante el registro ```PR2``` y la frecuencia del ```Timer2```. El ciclo útil se ajusta mediante el registro ```CCPR1L```. La fórmula de la frecuencia de la señal PWM es:

$$ f_{PWM} = \dfrac{f_{osc}}{4 \times TMR2_{prescaler} \times (PR2+1)}$$

En los ```PIC18``` (como el ```PIC18F45K22```), existen varios temporizadores: ```Timer0```, ```Timer1```, ```Timer2```, ```Timer3```, etc.

```Timer2``` es un temporizador de $8$ bits (cuenta de $0$ a $255$).

Está pensado especialmente para trabajar con el módulo ```CCP``` (*Capture/Compare/PWM*), porque:

* Tiene prescaler ($1$, $4$, $16$).

* Tiene postscaler ($1$ a $16$).

Usa un registro extra llamado ```PR2``` que define hasta qué valor contar.

Cuando ```Timer2``` llega al valor de $PR2$, se reinicia automáticamente a $0$ y se genera un “match” que controla el periodo del PWM.

* **Registro ```PR2```**: Es el *Period Register* del ```Timer2```.

    Como ```Timer2``` es de $8$ bits, ```PR2``` también es de $8$ bits (```0``` a ```255```).

    Lo que hace es definir el valor máximo hasta el cual ```Timer2``` cuenta antes de reiniciarse.

    Eso, junto con el prescaler, determina el periodo de la señal PWM.

    ⇒ Ejemplo:
    Si ```PR2``` $= 249$, entonces ```Timer2``` cuenta de $0$ a $249$ y luego se reinicia.

Se recomienda revisar la documentación [CCP Modules](https://www.mikroe.com/ebooks/pic-microcontrollers-programming-in-assembly/ccp-modules) de Microchip.


#### Temporizador 0 e interrupciones

El ```Timer0``` puede configurarse como temporizador de $16$ bits con prescaler para generar interrupciones periódicas. En este laboratorio, se usará para generar eventos cada $100$ ms. Dentro de la interrupción se puede modificar el ciclo útil, logrando un control automatizado de la modulación.

#### ADC y control manual del PWM

El módulo ```ADC``` (*Analog-to-Digital Converter*) permite convertir una señal analógica (como la de un potenciómetro) en un valor digital. Este valor se puede mapear directamente al rango del ciclo útil PWM ($0$ a $255$), permitiendo ajustar la potencia entregada al actuador de forma manual y en tiempo real.

## 4. Procedimiento

Se desea generar una señal PWM con un ciclo de trabajo que aumente automáticamente en pasos, utilizando interrupciones del ```Timer0```. La señal debe observarse mediante un osciloscopio y tener un periodo visible (por ejemplo, $100$ ms entre cambios de ciclo útil). 

### 4.1 Parte 1: 

Consiste en configurar el sistema  para operar a $64$ MHz usando el oscilador interno con PLL. Se habilita el módulo ```CCP1``` para generar PWM y se configura el ```Timer0``` con interrupciones para modificar periódicamente el ciclo útil.

#### Pasos:

1. Aceptar la tarea en GitHub Classroom.

2. Crear proyecto en ```MPLAB X``` para PIC18F45K22 (o referencia seleccionada) y agregar el ```main.c``` base del ítem anterior.

3. Realizar el montaje:

    <div align="center">
     <img src="/labs/figs/lab03/pwm_pic1.png" alt="pwm" width="700" />
     </div>

4. Calcular el valor de recarga adecuado para $100$ ms:

    * Frecuencia del sistema: $64$ MHz → ciclo de instrucción = $64$ MHz / $4$ = $16$ MHz

    * Periodo del ciclo: $1$ / $16$ MHz = $62.5$ ns

    * Para $100$ ms: $100$ ms / $62.5$ ns = $1.600.000$ ciclos

    * Como ```TMR0``` es $16$-bit ($0$ a $65535$), el valor a cargar sería: ```TMR0``` = $65536$ - $1600000$ / $256$ = $3036$

        ```
        TMR0 = 3036;
        ```

5. Modularizar el código embebido en:

    * Archivos de cabecera (```.h```) para definiciones y prototipos.

    * Módulos fuente (```.c```) para implementación de funciones PWM y temporizador.

    * Programa principal (```main.c```) para la lógica general del sistema.

6. Medir con el osciloscopio

    * Conectar la sonda del osciloscopio al pin ```RC2/CCP1```.

    * Verificar el cambio en el ciclo útil cada $100$ ms.

    * Medir la frecuencia de la señal PWM teóricamente y expermientalmente.

    * Calcular el ciclo útil de la señal PWM en diferentes instantes de tiempo.

8. Conectar un led y una resistencia pin ```RC2/CCP1``` para observar el cambio en el brillo debido al cambio en el ciclo útil de la señal y explicar. Tenga en cuenta el diagrama en la sección [Conexiones](#conexiones).

### 4.2 Parte 2

El ADC convierte la tensión analógica entregada por el potenciómetro en un valor digital de $10$ bits ($0$ a $1023$). Para el PWM de $8$ bits ($0$ a $255$), basta con tomar los $8$ bits más significativos ($>> 2$). Esto permite usar el potenciómetro para controlar directamente el ciclo de trabajo del PWM.

#### Pasos:

1. Realizar el siguiente montaje:

    <div align="center">
    <img src="/labs/figs/lab03/pwm_pic.png" alt="pwm" width="850" />
    </div>


    * Conexión del potenciómetro:

        * Terminal central a ```AN0``` (RA0)

        * Extremos a VDD y GND

2. Configurar ADC:

    ```
    void ADC_Init(void) {
        ADCON0 = 0x01;     
        ADCON1 = 0x0E;     
        ADCON2 = 0xA9;    
    }
    ```
    ```
    uint16_t ADC_Read(void) {
        ADCON0bits.GO = 1;              
        while (ADCON0bits.GO);          
        return ((uint16_t)(ADRESH << 8) | ADRESL); 
    }   
    ```

3. En el ```main```, reemplazar el incremento del ciclo útil por la lectura del valor del ADC para ajustar el ciclo útil del PWM de manera dinámica según el valor analógico recibido:

    ```
    while (1){
        uint16_t pot_value = ADC_Read();
        setDuty(pot_value);        
        __delay_ms(10);
    }
    ```

      

## 5. Entregables

1. Realice las partes [1](#41-parte-1) y [2](#42-parte-2) mencionadas en el procedimiento y presente
en clase las implementaciones de cada una al docente.

2. Realice la respectiva documentación de la implementación llevada a cabo.


## 6. Referencias
            
[1] [pic-microcontrollers-programming-in-assembly](https://www.mikroe.com/ebooks/pic-microcontrollers-programming-in-assembly)

[2] [CCP Modules](https://www.mikroe.com/ebooks/pic-microcontrollers-programming-in-assembly/ccp-modules).

[3] [Modulación de Ancho de Pulso PWM ](https://mecanicapp.com/modulacion-de-ancho-de-pulso-pwm/)