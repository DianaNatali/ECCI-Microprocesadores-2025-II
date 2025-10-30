# Lab06: Comunicación UART en microcontrolador PIC

## Objetivos

1. Configurar el módulo UART en un ```PIC18F45K22``` para permitir la comunicación serial.

2. Transmitir datos a través del UART desde el PIC hacia un terminal serial conectado a través de USB-UART.

3. Implementar funciones de transmisión y recepción de datos a través de UART.

4. Visualizar los datos recibidos en el terminal y comprender el proceso de transmisión asíncrona.

## Materiales

1. PIC18F45K22 o cualquier PIC compatible.

2. Programador/debugger PICkit 3/4.

2. Fuente de alimentación (o PICkit 3/4).

3. Conversor USB a serial UART (por ejemplo el CP2102).

4. Cable de conexión entre el conversor y el PC.

5. Software de terminal serial (puede usar PuTTY, Tera Term, o cualquier terminal de comunicación serial).


## Fundamento teórico

El módulo **EUSART** (Enhanced Universal Synchronous Asynchronous Receiver Transmitter) del ```PIC18F45K22``` permite la comunicación serial tanto síncrona como asíncrona. En este laboratorio se usará en modo asíncrono (**UART**), que no requiere señal de reloj adicional y es muy común para comunicación con PCs u otros dispositivos.

<div align="center">
 <img src="/labs/figs/lab06/uart_usart.png" alt="uart" width="550" />
 </div>


&rarr; **UART (Universal Asynchronous Receiver/Transmitter)**: es un protocolo de comunicación serial utilizado para la transmisión de datos de forma asíncrona, es decir, sin necesidad de una señal de reloj externa. En un sistema UART, dos dispositivos se comunican a través de dos líneas: ```TX``` (transmisión) y ```RX``` (recepción), como se muestra en el siguiente diagrama. El  ```PIC18F45K22``` tiene un modo UART que se puede configurar para transmitir y recibir datos en serie. 

<div align="center">
 <img src="/labs/figs/lab06/uart_diagram.png" alt="uart" width="450" />
 </div>



A continuación se enliastan algunos parámetros relevantes:

* Baud Rate: La tasa de transferencia de datos. El valor común para este ejemplo es $9600$ bps.

* Bits de datos: Generalmente se usan $8$ bits de datos en la transmisión.
  
* Paridad: La paridad es un mecanismo de verificación que añade un bit extra a los datos transmitidos para detectar posibles errores en la comunicación serial.No se usa en este ejemplo.

* Bits de parada: Un bit de parada indica el final de un *byte* transmitido.



## Procedimiento

### Descripción del funcionamiento del código en Github Classroom

🔹 Archivo ```uart.c``` – Implementación de la comunicación UART

1. Función ```UART_Init(void)```:


    * ```TRISC6 = 0``` configura el pin ```RC6``` como salida (TX).

    * ```TRISC7 = 1``` configura el pin ```RC7``` como entrada (RX).

        Estos pines están conectados internamente al módulo EUSART.

    * ```SPBRG1``` es el registro que determina el divisor del reloj para establecer la velocidad de baudios.

        En este caso, el módulo USART en el ```PIC18F45K22``` debe configurarse para transmitir y recibir datos a una velocidad de $9600$ bps. El valor de la constante de baudios (```SPBRG```) debe calcularse según la fórmula:

        $$SPBRG=\frac{f_{osc}}{64×Baudrate}−1$$

        Para un oscilador de $16$ MHz y un baudrate de $9600$, el valor de ```SPBRG``` es $25$.

    * ```BRGH = 0```: Baja velocidad (divisor de 64)

        Este bit se encuentra en el registro ```TXSTA1``` y controla la velocidad del generador de baud rate:

        * Generador de Baud Rate de 8 bits (```BRG16 = 0```):

            Cuando el bit ```BRG16 ``` está configurado a $0$, significa que el generador de baud rate utiliza un registro de 8 bits (```SPBRG```) para almacenar el valor calculado. Esto permite generar velocidades de transmisión, pero con una precisión limitada debido a la resolución del registro de $8$ bits (pudiendo almacenar valores de $0$ a $255$).

            En este caso, el divisor de la frecuencia de reloj para el cálculo del baud rate es 64. Es una fórmula más simple, y la velocidad de transmisión se ajusta dentro de una precisión razonable.

            Es lo que se conoce como baja velocidad asincrónica.

        * Generador de Baud Rate de $16$ bits (```BRG16 = 1```):

            Cuando ```BRG16``` está configurado a $1$, el generador de baud rate usa un registro de $16$ bits, lo que permite una mayor precisión en el cálculo del baud rate, ya que puede almacenar valores mucho mayores (de $0$ a $65535$).

            Con ```BRG16 = 1```, se puede configurar un baud rate más preciso y ajustarlo más finamente para obtener una transmisión más exacta en ciertas aplicaciones que requieren velocidades más altas.

    * ```TXSTA1bits.SYNC = 0```: Habilita el modo asíncrono (UART).

    * ```RCSTA1bits.SPEN = 1```: Enciende el módulo serial (habilita ```RC6``` y ```RC7``` como UART).

    * ```TXSTA1bits.TXEN = 1```: Habilita el transmisor UART.

    * ```CREN = 1```: Habilita la recepción continua (aunque no se esté usando aún, es buena práctica dejarlo habilitado por si se requiere).

2. Función ```UART_WriteChar```:

    * ```TXSTA1bits.TRMT```: Bit que indica si el buffer de transmisión está vacío.

    * ```TXREG1```: Registro donde se escribe el dato a transmitir.

    El bucle garantiza que no se sobrescriba el dato anterior en tránsito.


3.  Función ```UART_WriteString```:

    * Esta función toma un puntero a una cadena (```const char* str```) y envía carácter por carácter usando la función del ítem anterior ```UART_WriteChar```.

    * ```*str++``` envía el carácter actual y luego avanza al siguiente.

    &rarr; ¿Por qué usar un puntero en esta función?

    Un **apuntador** (o puntero) en ```C``` es una variable que guarda la dirección de memoria de otra variable.

    1. **Eficiencia en memoria**: 
    Una cadena, por ejemplo ```"Hola"```, es un arreglo de caracteres en memoria. En lugar de copiar todo el arreglo, se pasa solo la dirección del primer carácter, lo cual es mucho más eficiente.

    2. **Recorrer carácter por carácter**:  El puntero ```str``` avanza con ```str++```, lo que permite recorrer toda la cadena sin usar un índice ni conocer su tamaño. Se detiene cuando encuentra el carácter nulo ```'\0'```, que marca el final de la cadena en lenguaje ```C```.

    3. **Simplicidad**:  El uso de punteros hace que el código sea compacto y claro. La expresión ```*str++```:

        * Envía el carácter apuntado con ```UART_WriteChar(*str)```.

        * Luego avanza el puntero con ```str++```.

🔹 Archivo ```main.c``` – Función principal

1.  Fuses de configuración

    * ```FOSC = INTIO67```:  *Frequency Oscillator* (fuente de reloj principal del microcontrolador). En ste caso con ```INTIO67``` se configura el oscilador interno, es decir, sin cristal externo. En otras palabras, selecciona el oscilador interno como fuente de reloj y permite que los pines ```RA6``` (```pin 10```) y ```RA7``` (```pin 9```) se usen como entradas/salidas normales.

    * ```WDTEN = OFF```: El *Watchdog Timer* (WDT) es un temporizador interno del microcontrolador que sirve como sistema de seguridad.
    Su función es reiniciar el microcontrolador si este se queda "colgado" o entra en un bucle infinito. En este caso se desactiva el watchdog timer para evitar reinicios automáticos.

    * ```LVP = OFF```: LVP es una configuración del microcontrolador que significa *Low Voltage Programming* (Programación en Bajo Voltaje) y sirve para:

        * Si está en ON (activada): El microcontrolador permite ser programado usando solo $5$ V en lugar de un voltaje de programación más alto (~$13$ V).

            Pero para lograrlo, el pin ```RB3``` se reserva para ese propósito y no se puede usar como entrada/salida normal.

        * Si está en OFF (desactivada): 
        
            * Desactiva la programación en bajo voltaje.

            * Libera el pin ```RB3``` para que puedas usarlo como I/O normal.

#### Resumen y diagrama interno 
<div align="center">
 <img src="/labs/figs/lab06/pic_uart.png" alt="uart" width="850" />
 </div>

 1. Activación del puerto serie (```RCSTA_bit7_R/W```): 1&rarr;Habilitado, 0&rarr; Deshabilitado.
 2. Habilitación de transmisión (```TXTA_bit5_R/W```): 1&rarr;Habilitado, 0&rarr; Deshabilitado.
3. Determinar el divisor del reloj para establecer la velocidad de baudios.
4. Dato a transmitir.
5. Registro de desplazamiento interno. Es el registro interno al que el hardware del UART transfiere los datos del buffer ```TXREG``` para luego enviarlos bit a bit por la línea TX.
6. Estado del registro ```TSR``` (```TXSTA_bit1_R```): 1&rarr;Vacío, 0&rarr; Lleno.
7. Noveno bit a transmitir (```TXSTA_bit0_W```). Bit de paridad.
8. Habilitación del noveno bit (```TXSTA_bit6_R/W```): 1&rarr;Habilitado, 0&rarr; Deshabilitado.
9. Habilitación de interrupción de transmisión (```PIE1_bit4_R/W```): 1&rarr;Habilitado, 0&rarr; Deshabilitado.
10. Flag de interrupción de transmisión (```PIR1_bit4_R/W```): 1&rarr;Buffer de datos vacío, 0&rarr; Deshabilitado.


<div align="center">
 <img src="/labs/figs/lab06/RCSTA.png" alt="uart" width="850" />
 </div>

 <div align="center">
 <img src="/labs/figs/lab06/TXSTA.png" alt="uart" width="850" />
 </div>

  <div align="center">
 <img src="/labs/figs/lab06/BAUD.png" alt="uart" width="850" />
 </div>

   <div align="center">
 <img src="/labs/figs/lab06/BAUD2.png" alt="uart" width="850" />
 </div>




### Verificación con monitor serial

#### Parte 1
Para observar los datos enviados por el PIC a través de UART, se debe usar un monitor serial. Aquí hay varias opciones:

* Opción 1: Usar PuTTY (Windows/Linux)

    * Descargar PuTTY desde https://www.putty.org.

    * Conectar el PIC al PC a través del conversor serial

    * Abrir PuTTY y seleccione:

        * Connection type: Serial

        * Serial line: (ej. COM3 en Windows, /dev/ttyUSB0 en Linux)

        * Speed (baud): 9600

    * Hacer clic en Open. Deberías ver los mensajes enviados por el PIC.

* Opción 2: Usar CuteCom (Linux)

    * Intalar con:

        ```
        sudo apt install cutecom
        ```

    * Ejecutar cutecom y selecciona el puerto correcto (/dev/ttyUSB0 o similar).

    * Configurar a 9600 baudios, 8 bits, sin paridad, 1 bit de parada.

    * Hacer clic en Open.

* Opción 3: Usar un Arduino

 Pueden utilizar el monitor serial de la IDE de Arduino.


#### Parte 2

 Otra forma de visualizar la comunicación serial es haciendo uso de un [script de Python](/labs/06_lab06/serial_pic.py), que recibe los datos enviados por el **PIC18** a través del puerto UART y los representa en tiempo real mediante la librería ```matplotlib```. Este script se abordara en clase.

 Antes de ejecutar el script, debe verificarse el nombre del puerto serial al que está conectado el conversor USB a serial UART.

 * En Windows, el puerto tiene formato COMx, por ejemplo:

    ```
     SERIAL_PORT = 'COM3'
    ```
    Pueden identificar el número desde el Administrador de dispositivos → Puertos (COM y LPT).

* En Linux, el nombre del puerto suele ser:

  ```
  SERIAL_PORT = '/dev/ttyUSB0'
  ```

  o bien ```/dev/ttyACM0```, dependiendo del adaptador utilizado.

  Pueden verificarlo ejecutando en la terminal:

  ```
  ls /dev/tty*
  ```

        
### Conexiones:
<div align="center">
 <img src="/labs/figs/lab06/montaje.png" alt="uart" width="850" />
 </div>


<!-- <div align="center">
 <img src="/laboratorios/figs/lab04/uart1.png" alt="pwm" width="850" />
 </div>
 <div align="center">
 <img src="/laboratorios/figs/lab04/uart2.png" alt="pwm" width="850" />
 </div>

 <div align="center">
 <img src="/laboratorios/figs/lab04/uart3.png" alt="pwm" width="850" />
 </div> -->

 ## Entregables

1. Lea la anterior guía y presente en clase la implementación al docente.

2. Realice la respectiva documentación de la implementación llevada a cabo.

