# Lab08 - Actividad Evaluativa: Integración de ADC, LCD 16x2 con protocolo I²C y protocolo UART

En esta actividad evaluativa, los estudiantes deberán combinar lo aprendido en el manejo del ADC (Conversor Analógico-Digital), el control de una pantalla LCD $16$x$2$ en modo I²C y el protocolo de comunicaciones UART (Universal Asynchronous Receiver-Transmitter ), para desarrollar un sistema embebido que permita visualizar en tiempo real una lectura analógica y mostrar información dinámica tanto en la pantalla como en una gráfica en tiempo real usando Python.

## Descripción General

Se debe diseñar e implementar un programa en el microcontrolador ```PIC```, donde:

1. El microcontrolador lea, a través del módulo ADC, el valor de una señal analógica proveniente de un potenciómetro.

2. Convertir el valor digital obtenido en su equivalente en voltaje, tomando como referencia una $V_{ref}$ de $5.0$ V.

3. El valor debe ser visualizado en la LCD $16\times 2$, utilizando comunicación en modo I²C.

4. Además de mostrar el valor numérico del ADC, se deberá incluir mensajes de texto dinámicos, con longitud mayor a $16$ caracteres, empleando corrimiento horizontal (scroll) para que el mensaje completo sea visible en la pantalla.

5. Adicionalmente se debe integrar comunicación UART para la visualización de los valores de tensión usando un script de Python.

## Objetivos específicos

1. Aplicar la configuración y uso del ADC interno para muestrear señales analógicas.

2. Implementar funciones de control de la LCD en modo paralelo, utilizando librerías modulares con archivos ```.h``` y ```.c```.

3. Integrar la información proveniente del ADC con la visualización en pantalla en tiempo real.

4. Realizar la conversión matemática de valor digital → voltaje.

5. Desarrollar una técnica de scroll de texto, permitiendo mostrar mensajes extensos de manera dinámica.

6. Practicar la organización de un proyecto modular, separando:

    * Archivos de cabecera (```.h```)

    * Implementación de funciones (```.c```)

    * Programa principal (```main.c```)

## Requerimientos técnicos

### 1. Lectura y conversión ADC

* Configurar un canal analógico (ejemplo: ```AN0```) conectado a un potenciómetro.

* Suponer una voltaje de referencia ($V_{ref}$ = $5.0$ V).

* Calcular el voltaje según la fórmula:

$$ VOUT​=\frac{ADC_{VAL}×V_{REF}}{1023}​​  $$

* Actualizar constantemente el valor en la LCD con dos decimales de precisión.
Ejemplo: Voltaje: 3.27V.

### 2. Visualización en LCD

#### Primera línea:

* Mostrar el texto ```Voltaje: X.XXV``` con la lectura actualizada.


#### Segunda línea:

* En la segunda línea, mostrar un texto dinámico mayor a $16$ caracteres, por ejemplo:

    ```
    Sistema embebido en acción: lectura continua de sensor.
    ```

* Implementar corrimiento horizontal de izquierda a derecha para visualizar todo el mensaje.

### 3. Visualización en PC

A través de Python se debe observar en una gráfica las lecturas del potenciómetro en tiempo real.


### Adcional - bonus:

1. Crear un pograma de encienda y apague un LED a través del protocolo UART, usando un monitor serial (p. ejem: Putty). Luego de esto en lugar de controlar un LED, controlar la generación de una señal PWM.

2. Implementar un sensor analógico diferente al potenciómetro (p. ejem: Un sensor de temperatura) y ajustar la gráfica de Python a estas lecturas.


