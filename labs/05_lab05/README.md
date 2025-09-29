# Lab05 - Actividad Evaluativa: Integración de ADC y LCD 16x2

En esta actividad evaluativa, los estudiantes deberán combinar lo aprendido en el manejo del ADC (Conversor Analógico-Digital) y el control de una pantalla LCD $16$x$2$ en modo paralelo, para desarrollar un sistema embebido que permita visualizar en tiempo real una lectura analógica y mostrar información dinámica en la pantalla.

## Descripción General

Se debe diseñar e implementar un programa en el microcontrolador ```PIC```, donde:

1. El microcontrolador lea, a través del módulo ADC, el valor de una señal analógica proveniente de un potenciómetro.

2. Convertir el valor digital obtenido en su equivalente en voltaje, tomando como referencia una $V_{ref}$ de $5.0$ V.

2. El valor debe ser visualizado en la LCD $16$x$2$, utilizando comunicación en modo paralelo de $4$ bits.

3. Además de mostrar el valor numérico del ADC, se deberá incluir mensajes de texto dinámicos, con longitud mayor a $16$ caracteres, empleando corrimiento horizontal (scroll) para que el mensaje completo sea visible en la pantalla.

## Objetivos específicos

1. Aplicar la configuración y uso del ADC interno para muestrear señales analógicas.

2. Implementar funciones de control de la LCD en modo paralelo, utilizando librerías modulares con archivos ```.h``` y ```.c```.

3. Integrar la información proveniente del ADC con la visualización en pantalla en tiempo real.

4. Realizar la conversión matemática de valor digital → voltaje.

5. Desarrollar una técnica de scroll de texto, permitiendo mostrar mensajes extensos de manera dinámica.

6. Practicar la organización de un proyecto modular, separando:

    * Archivos de cabecera (.h)

    * Implementación de funciones (.c)

    * Programa principal (main.c)

## Requerimientos técnicos

### 1. Lectura y conversión ADC

* Configurar un canal analógico (ejemplo: ```AN0```) conectado a un potenciómetro.

* Suponer una voltaje de referencia ($V_{ref}$ = $5.0$V).

* Calcular el voltaje según la fórmula:

$$ VOUT​=\frac{ADC_{VAL}×V_{REF}}{1023}​​  $$

* Actualizar constantemente el valor en la LCD con dos decimales de precisión.
Ejemplo: Voltaje: 3.27V.

### 3. Visualización en LCD

#### Primera línea:

* Mostrar el texto ```Voltaje: X.XXV``` con la lectura actualizada.


#### Segunda línea:

* En la segunda línea, mostrar un texto dinámico mayor a $16$ caracteres, por ejemplo:

    ```
    Sistema embebido en acción: lectura continua de sensor.
    ```

* Implementar corrimiento horizontal de izquierda a derecha para visualizar todo el mensaje.

#### Estructura de código

* **lcd.h / lcd.c**: Funciones para inicializar y controlar la LCD.

* **adc.h / adc.c**: Funciones para inicializar y leer valores del ADC.

* **main.c**: Lógica principal, actualización de lectura y control de corrimiento del texto.

