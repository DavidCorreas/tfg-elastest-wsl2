# Custom Kernel

Necesitamos usar un kernel propio si queremos ejecutar emuladores en wsl2.
Esto debemos tenerlo en windows y ejecutarlo en una power shell con permisos de administrador.
En este directorio tenemos ya un kernel creado (vmlinux)

## Observaciones

Usamos la pagina https://gist.github.com/offlinehacker/b1d96515f87a47bd0b0bea574eab5583
No tiene habilitado para hacer clone del repo, por lo que nos tenemos que descargar el zip.
- ERROR: hay que instalar gcc y make, libncurses5-dev, bison, flex, libelf-dev, libssl-dev
- ERROR: No tenemos Windbgx.exe y descargamos el SDK de Windows: https://developer.microsoft.com/es-es/windows/downloads/windows-10-sdk/
-- Solo marcamos la opcion de debugging tools
-- Path de windbg.exe: C:\Program Files (x86)\Windows Kits\10\Debuggers\x64
- Conseguido virtualizacion, cosas a tener en cuenta:
-- Ejecutar el .bat en una terminal localizada en el path donde este (no ejecutar "run as administrator")
-- Cada vez que quiero usar ese kernel tengo que ejecutar el bat y no lo puedo cerrar
-- IMPORTANTE: cambiar run-wsl.bat de windbgx.exe a windbg.exe

## README del repositorio con las instrucciones de uso:# WSL notes

### Rebuilding kernel with kvm support

This instructions are based on https://microhobby.com.br/blog/2019/09/21/compiling-your-own-linux-kernel-for-windows-wsl2/

1. Clone source

```
git clone https://github.com/microsoft/WSL2-Linux-Kernel.git
cd WSL2-Linux-Kernel
```

2. Reconfigure kernel

Enable kvm, as described here https://wiki.gentoo.org/wiki/QEMU#Kernel

```
make menuconfig KCONFIG_CONFIG=Microsoft/config-wsl
```

3. Build kernel

```
make KCONFIG_CONFIG=Microsoft/config-wsl -j8
```

4. Copy kernel

```
cp vmlinux /mnt/c/Users/<seuUser>/
```

5. Copy `.wslconfig` file to user home

### Starting WSL with KVM support

1. Copy `run-wsl.bat`, `attach.wdbg` and `patch_wsl_nested.js` to same folder
2. Fix path in `attach.wdbg`
3. Open admin powershell in that folder
4. Run `./run-wsl.bat` and follow instructions

# Utilidades para WSL2
Se han tenido algunos problemas en el desarrollo con WSL2. Se han tenido que añadir algunas utilidades.

## Port Forward
Las interfaces de red aun no funcionan correctamente en WSL2 y por ejemplo, se pretende que el Linux integrado comparta las interfaces de red con Windows.
Esta abstraccion aun no funciona correctamente, ya que si se expone un servicio por un puerto del Linux, no es visible en la red local.
Para solucionar esto, hago uso del script `port-forwarding.ps1`, el cual tiene que ser ejecutado en windows y expone los puertos del Linux a la red local.
