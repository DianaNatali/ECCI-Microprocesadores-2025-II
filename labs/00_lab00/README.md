# Lab 00: Instalación de herramientas 

En este laboratorio nos enfocaremos en la instalación y configuración de las herramientas esenciales para el desarrollo de este curso.

Índice:

1. [Visual Studio Code](#1-visual-studio-code)
2. [Git/Github](#2-git-y-github)
3. [MPLAB X IDE](#3-instalar-mplab-x-ide-en-windows)



## 1. Visual Studio Code 

```Visual Studio Code``` (```VS Code```) es un editor de código ligero pero potente, desarrollado por Microsoft. Es altamente personalizable y cuenta con una amplia gama de extensiones que lo hacen ideal para el desarrollo de software y hardware.

1. Descargar Visual Studio Code: [VS Code download](https://code.visualstudio.com/).

2. Seguir las instrucciones del instalador para completar la instalación.

3. Una vez instalado, abrir ```VS Code``` y explora las extensiones disponibles en el marketplace.


## 2. ```Git``` y GitHub

Git es un sistema de control de versiones ampliamente utilizado para gestionar proyectos de software y hardware. GitHub es una plataforma basada en Git que permite alojar repositorios y colaborar en proyectos.

1. Crear cuenta en [GitHub](https://github.com/).

2. Descargar Git en el computador a través del sitio oficial: [Git download](https://git-scm.com/downloads).

3. Seguir las instrucciones del instalador.

4. Configura ```Git``` de acuerdo al nombre de usuario y correo electrónico usados al crear la cuenta en Github:

    En una terminal del sistema operativo ejecutar:

    ```
    git config --global user.name "cuenta de usario"
    git config --global user.email correo@email.com
    ```

5. Aprender los comandos básicos de Git:

    * ```git init```: Inicializa un repositorio.
    * ```git clone <url>```: Clona un repositorio remoto.
    * ```git add <archivo>```: Añade archivos al área de preparación.
    * ```git commit -m "mensaje"```: Guarda los cambios en el repositorio.
    * ```git push```: Sube los cambios al repositorio remoto.
    * ```git pull```: Actualiza el repositorio local con los cambios remotos.


## 3. Instalar MPLAB X IDE en Windows

### 1. Descargar MPLAB X IDE:

    
* Entrar a la página oficial de Microchip:
    https://www.microchip.com/en-us/tools-resources/develop/mplab-x-ide

* Hacer clic en Download y seleccionar la versión para Windows (```.exe```).

* Guardar el archivo en el PC.


### 2. Descargar compiladores XC

Para programar microcontroladores, asegurarse también de descargar:

* MPLAB XC8 Compiler → para microcontroladores PIC de $8$ bits como el ```PIC18F45K22```.

* MPLAB XC16 Compiler → para PIC de $16$ bits.

*  MPLAB XC32 Compiler → para PIC de $32$ bits.

**Para el ```PIC18F45K22```, descargar únicamente ```XC8```.**

Descargar desde:
https://www.microchip.com/en-us/tools-resources/develop/mplab-xc-compilers

### 3. Instalar MPLAB X IDE

* Abrir el instalador ```.exe``` de MPLAB X.

* Aceptar los términos de licencia.

* Seleccionar la carpeta de instalación (por defecto ```C:\Program Files (x86)\Microchip\MPLABX```).

* Mantener las opciones predeterminadas.

* Hacer clic en ```Install``` y esperar a que finalice.

* Permitir el acceso en caso de advertencias del firewall de Windows.

### 4. Instalar el compilador XC8

* Abrir el instalador de ```XC8```.

* Aceptar la licencia.

*  Seleccionar el modo de uso:

    * **Free** → gratis, con optimizaciones limitadas.

    * **Pro (Trial)** → gratis por 60 días con optimizaciones avanzadas.

* Completar la instalación.


### 5. Verificar instalación

* Abrir ```MPLAB X IDE```.

* Ir a ```Tools → Options → Embedded → Build Tools```.

* Comprobar que el compilador ```XC8``` aparezca en la lista.