# Lab04: Visualización en pantalla LCD de 16x2 


## Índice:

#### 1. [Objetivos de aprendizaje](#1-objetivos-de-aprendizaje)

#### 2. [Herramientas](#2-herramientas)

#### 3. [Fundamento](#3-fundamento)

#### 4. [Procedimiento](#4-procedimiento)

#### 5. [Entregables](#5-entregables)


------------------------------------------------------

## 1. Objetivos de aprendizaje
  * Dominar el control básico de una pantalla LCD de $16$x$2$ para visualizar información estándar y caracteres personalizados en sistemas embebidos.

  * Implementar una comunicación paralela con una LCD para mostrar mensajes alfanuméricos.

  * Diseñar y utilizar caracteres personalizados mediante la gestión de la memoria CGRAM de la LCD.

  * Implementar una estructura de código modular organizada en:

     * Archivos de cabecera (```.h```) para definiciones y prototipos

    * Módulos fuente (```.c```) para implementación de funciones LCD

    *  Programa principal (```main.c```) para la lógica de aplicación

  * Aplicar técnicas de programación embebida para animaciones y actualizaciones dinámicas del display.

## 2. Herramientas

  * Pantalla LCD de $16$x$2$.

  * Microcontrolador ```PIC18F45K22```.

  * Protoboard y cables de conexión.

  * Potenciómetro de $10$ kΩ (para ajustar el contraste de la LCD).

  * Entorno de programación MPLAB X IDE con compilador XC8.

## Fundamento

En sistemas embebidos y arquitecturas digitales, la interacción con dispositivos de visualización es esencial para la presentación de información en tiempo real. Entre estos dispositivos, las pantallas LCD alfanuméricas de $16$x$2$ son ampliamente utilizadas debido a su simplicidad y bajo consumo de recursos.

### Carácteristicas básicas de la LCD 16x2

LCD significa "Pantalla de Cristal Líquido" (Liquid Crystal Display). El nombre LCD $16$×$2$ se debe a que tiene $16$ columnas y $2$ filas. Existen varias configuraciones como 8×1, 8×2, 10×2, 16×1, entre otras, pero la más utilizada es la 16×2. Esto significa que puede mostrar 32 caracteres en total, donde cada carácter está compuesto por una matriz de 5×8 píxeles, como se muesta en la siguiente figura:

<p align="center">
 <img src="/labs/figs/lab04/LCD16x2_diag.png" alt="alt text" width=500 >
</p>

* Voltaje de operación: $4.7$V a $5.3$V
* Consumo de corriente: 1mA sin retroiluminación
* Pantalla alfanumérica, permite mostrar letras y números
* Dos filas, cada una con capacidad para $16$ caracteres
* Cada carácter se construye en una matriz de $5$×$8$ píxeles
* Puede operar en modo de $8$ bits o $4$ bits (se usará en modo de $4$ bits)
* Permite mostrar caracteres personalizados
* Disponible con retroiluminación en color verde o azul


La pantalla LCD 16x2 cuenta con múltiples pines para alimentación, control y transferencia de datos. La siguiente lista describe sus funciones principales:

<p align="center">
 <img src="/labs/figs/lab04/LCD16x2.png" alt="alt text" width=500 >
</p>

* **Vss** = GND (Tierra)
* **Vdd** = (+ $3.3$ V a + $5$ V) – Alimentación de la pantalla
* **Vo** = Ajuste de contraste 
* **RS** = Selección de tipo de registro – RS= $0$: Comando, RS= $1$: Dato
* **R/W** = Lectura/Escritura – R/W= $0$: Escritura, R/W= $1$: Lectura
* **E** = Clock (Enable) – Activado en el flanco de bajada
* **D0** = Bit $0$ – Línea de datos
* **D1** = Bit $1$ – Línea de datos
* **D2** = Bit $2$ – Línea de datos
* **D3** = Bit $3$ – Línea de datos
* **D4** = Bit $4$ – Línea de datos
* **D5** = Bit $5$ – Línea de datos
* **D6** = Bit $6$ – Línea de datos
* **D7** = Bit $7$ – Línea de datos
* **A** = Ánodo de retroiluminación (+)
* **K** = Cátodo de retroiluminación (-)


**Se recomienda revisar en detalle el [*datasheet*](/labs/04_lab04/lcd016n002bcfhet.pdf) de la LCD 16x2**.


### CGRAM y caracteres especiales

Una pantalla LCD de 16x2 permite mostrar caracteres alfanuméricos y algunos símbolos predefinidos. Además, es posible crear caracteres personalizados utilizando la memoria **CGRAM** (Character Generator RAM) de la LCD. Cada carácter personalizado se define como una matriz de $5$ x $8$ píxeles.


La CGRAM es una pequeña porción de memoria en la LCD (típicamente $64$ bytes) que se utiliza para definir caracteres personalizados. Cada carácter personalizado se representa como una matriz de $5$ x $8$ píxeles (aunque también hay un modo de $5$ x $10$ píxeles, menos común). La CGRAM permite almacenar hasta $8$ caracteres personalizados en una LCD estándar de 16x2.

**¿Cómo se relaciona la CGRAM con los caracteres especiales?**

1. Definición de caracteres personalizados:

    Cada carácter personalizado se define como un arreglo de $8$ bytes, donde cada byte representa una fila de $5$ píxeles (los $3$ bits restantes en cada byte no se utilizan).

2. Almacenamiento en la CGRAM:

    La CGRAM tiene $64$ bytes de espacio, lo que permite almacenar $8$ caracteres personalizados ($8$ caracteres × $8$ bytes cada uno).

    Cada carácter personalizado se asocia a una ubicación en la CGRAM (de $0$ a $7$).

3. Uso de los caracteres personalizados:

    Una vez definido un carácter en la CGRAM, se puede mostrar en la pantalla LCD enviando el valor correspondiente a su ubicación en la CGRAM.

    Por ejemplo, si se define un carácter en la ubicación $0$ de la CGRAM, se puede mostrarlo enviando el valor $0$ x $00$ a la LCD.


## Conexión con el PIC18F25K22


### Configuración de pines

| Pin LCD | Función           | Pin PIC18F25K22       |
|---------|-------------------|-----------------------|
| VSS     | GND               | GND                   |
| VDD     | +5V               | VDD                   |
| VO      | Contraste         | Potenciómetro         |
| RS      | Registro          | `LATC0`               |
| RW      | Lectura           | GND (solo escritura)  |
| E       | Enable            | `LATC1`               |
| D4      | Dato 4            | `LATC2`               |
| D5      | Dato 5            | `LATC3`               |
| D6      | Dato 6            | `LATC4`               |
| D7      | Dato 7            | `LATC5`               |
| A       | Retroiluminación+ | +5V con resistor 220Ω |
| K       | Retroiluminación- | GND                   |


## Procedimiento

### Parte 1: Visualizar texto genérico

  Mostrar en la LCD diferentes secuencias de texto.

### Parte 2: Visualizar caracteres especiales

  1. Definir 5 caracteres personalizados que representan diferentes niveles de carga de la batería.

  2. Los caracteres se cargan en la CGRAM.

  3. Se muestran los iconos de batería en la primera fila de la pantalla.

  4. Relizar una animación con estos iconos, mostrandolos uno a la vez en el mismo cursor de la pantallada.






## 5. Entregables

1. Realice las partes [1](#parte-1) y [2](#parte-2) mencionadas en el procedimiento y presente
en clase las implementaciones de cada una al docente.

2. Realice la respectiva documentación de la implementación llevada a cabo.
