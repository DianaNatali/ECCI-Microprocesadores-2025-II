# Lab07: Visualización en LCD 16x2 usando módulo I2C en microcontrolador PIC

![i2c](/laboratorios/figs/lab04/i2c.png)

    La pantalla LCD $16\times2$ se controlará en este laboratorio mediante el protocolo I2C utilizando un expansor de puertos ```PCF8574```. Este enfoque reduce la cantidad de pines necesarios para la conexión, ya que utiliza únicamente dos líneas: ```SDA``` (datos) y ```SCL``` (reloj). El módulo suele tener una dirección base de $7$ bits igual a $0\times27$. Sin embargo, en el protocolo I2C, la dirección que se transmite al bus debe tener $8$ bits, donde el último bit indica si se va a leer ($1$) o escribir ($0$). Como en este caso solo se realiza escritura hacia el LCD, la dirección efectiva enviada es $0\times4E$, que resulta de desplazar $0\times27$ una posición a la izquierda ($0\times27 << 1 $). Esta dirección ya se encuentra definida en el código como:

    ```
    #define LCD_ADDR 0x4E
    ```