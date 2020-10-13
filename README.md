# TFG-ELASTEST-WSL2
Scripts y ayudas para poder desarrollar en Linux sobre un Windows.

## Objetivos
Este proyecto se ha desarrollado prácticamente por completo con [WSL2](https://docs.microsoft.com/es-es/windows/wsl/install-win10).
A pesar de que ha sido un grán avance sobre WSL, sigue tiendo algunas limitaciones que están por solucionarse. 
Con ayuda de la comunidad que hay detrás de esta tecnología, se han tenido 
que desarrollar algunos parches para dar solución a algunos problemas que aún tiene este Linux,
que impedía hacer uso de algunas funcionalidades de este proyecto.

Por ello, este proyecto no tiene ningún fin como utilidad para el proyecto, sino que sirve como 
medio para el desarrollo y despliegue de dicho proyecto en Windows.

Los principales fallos que se han dado solución con los componentes de estre proyecto son:
- Inaccesibilidad del subsistema Linux por el resto de equipos de la red local.
- Incapacidad de virtualización en el kernel por defecto de WSL2. 

## Componenetes
Los dos fallos mencionados son solucionados por estos dos componentes.

### Port Forwarding
Se trata de un script que soluciona el problema de no poder acceder al Linux a partir de una máquina de la red
local. Esta limitación está documentada y se ha hecho uso de un _workaround_ comentado en este [hilo](https://github.com/microsoft/WSL/issues/4150).

La solución que se ha optado es de hacer un _port forwarding_ del SO Host (Windows) al SO Linux, que aunque se ha 
intentado virtualizar de la forma que parezca que es una parte de Windows, no deja de ser una máquina virtual.

Para hacer uso de este script se debe editar los puertos que queremos exponer en la máquina.

### Custom Kernel
Por defecto, no se pueden ejecutar máquinas virtuales en WSL2. Aunque se trate de un SO completo con su propio
kernel y donde se pueden ejecutar el 100% de instrucciones de Linux, no deja de ser una máquina virtual. 
Esto quiere decir que por defecto, no cuenta con la caracterísitica de virtualización anidada. Esto lo que nos
permite es poder ejecutar "microinstrucciones" hardware de virtualización.

WSL2 no lo tiene activado por defecto, por lo que se tiene que crear un kernel personalizado para hacer uso de
esta caracterísitca.

## Despliegue
Se indicará como hacer uso de estas características.

### Port Forwarding
Para hacer uso de esta funcionalidad, lo único que se tiene que hacer es abrir una aplicaciónd de comandos PowerShell
en modo adminstrador y ejecutar el comando .\port-forwarding.ps1

Para elegir los puertos de los que se quiere hacer port forwarding se seleccionan dentro del script, en la 
variable `ports`.

### Custom Kernel
Para poder hacer uso de un custom kernel, se debe contar con WSL2 y seguir una serie de pasos. Estos pasos han
sido sacados de la siguiente [página](https://microhobby.com.br/blog/2019/09/21/compiling-your-own-linux-kernel-for-windows-wsl2/).

En principio, no se debe compilar un kernel, ya que se encuentra en el repositorio. Si se quisiese compilar un 
kernel personalizado se han de seguir los pasos mencionados en la página anterior.

Para desplegarlo, se va a hacer uso del siguiente [repositorio](https://gist.github.com/offlinehacker/b1d96515f87a47bd0b0bea574eab5583) que cuenta con los scripts necesarios para 
hacer uso de este kernel.

1. Copiar el kernel del repositorio en la carpeta de usuario `cp .\windows-scripts\vmlinux C:\Users\<User>`
2. Actualizar _.wslconfig_ con la ruta de dentro del fichero apuntando a nuestro
nuevo kernel y copiarla a la misma carpeta que el kernel. `cp .\windows-scripts\.wslconfig C:\Users\<User>`
3. Tener _windbg.exe_ (descargar si no se tiene [aquí](https://developer.microsoft.com/es-es/windows/downloads/windows-10-sdk/)) 
en el path y actualizar el path de _.\windows-scripts\attach.wdbg_.
4. Abrir un PowerShell en modo administrador y ejecutar _.\run-wsl.bat_. 
`cd .\windows-scripts\` `.\run-wsl.bat`

#### Observaciones

Usamos la pagina https://gist.github.com/offlinehacker/b1d96515f87a47bd0b0bea574eab5583

Errores que pueden dar al seguir estos pasos:
- En el Linux, hay que instalar gcc y make, libncurses5-dev, bison, flex, libelf-dev, libssl-dev
- No tenemos Windbgx.exe y descargamos el SDK de Windows: https://developer.microsoft.com/es-es/windows/downloads/windows-10-sdk/
    + Solo marcamos la opcion de debugging tools
    + Path de windbg.exe: C:\Program Files (x86)\Windows Kits\10\Debuggers\x64
- Cosas a tener en cuenta:
    + Ejecutar el .bat en una terminal localizada en el path donde este (no ejecutar script directamente "run as administrator").
    + Cada vez que se quiera usar ese kernel tengo que ejecutar el bat y no se puede cerrar.
    + Si se usa el repositorio original: cambiar run-wsl.bat de windbgx.exe a windbg.exe
    + No olvidar: Cambiar ruta de _attach.wdbg_ a la ubicacion de _patch_wsl_nested.js_.