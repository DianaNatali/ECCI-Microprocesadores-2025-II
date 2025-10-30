# Lab07: Visualización en LCD 16x2 usando módulo I2C en microcontrolador PIC

## 1. Objetivos de aprendizale

1. Configurar el módulo I2C (```MSSP```) del ```PIC18F45K22``` en modo maestro.

2. Comunicar el PIC con una LCD $16×2$ utilizando el adaptador basado en **PCF8574**.

3. Implementar funciones para enviar comandos y caracteres vía **I2C**.

4. Mostrar mensajes en la pantalla LCD desde el programa principal.


## 2. Materiales

1. PIC18F45K22 o cualquier PIC compatible.

2. Programador/debugger PICkit 3/4.

3. Fuente de alimentación (o PICkit 3/4).

4. LCD $16×2$.

5. Módulo I2C **PCF8574**.

6. Entorno de programación MPLAB X IDE con compilador XC8.



## 3. Fundamento teórico

![i2c](/labs/figs/lab07/i2c.png)

La pantalla LCD $16\times2$ se controlará en este laboratorio mediante el protocolo I2C utilizando un expansor de puertos **`PCF8574**. Este enfoque reduce la cantidad de pines necesarios para la conexión, ya que utiliza únicamente dos líneas: ```SDA``` (datos) y ```SCL``` (reloj). 

El PIC18F45K22 cuenta con el módulo MSSP (Master Synchronous Serial Port), capaz de trabajar en protocolos **SPI** e **I2C**. Entonces, en este laboratorio se usa en modo **I2C** Maestro para enviar datos al módulo LCD **`PCF8574**.

El módulo **`PCF8574** suele tener una dirección base de $7$ bits igual a $0\times27$. Sin embargo, en el protocolo **I2C**, la dirección que se transmite al bus debe tener $8$ bits, donde el último bit indica si se va a leer ($1$) o escribir ($0$). Como en este caso solo se realiza escritura hacia el LCD, la dirección efectiva enviada es $0\times4E$, que resulta de desplazar $0\times27$ una posición a la izquierda ($0\times27 << 1 $). Esta dirección ya se encuentra definida en el código como:

```
#define LCD_ADDR 0x4E
```

**¿Por qué usar I2C con la LCD?**

* Sin I2C → la LCD requiere $6$ a $8$ pines del microcontrolador.
* Con I2C → solo se requieren $2$ pines: ```SCL``` (```RC3```) y ```SCL``` (```RC4```).