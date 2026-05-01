## <FONT COLOR=#8B008B>Problema al usar MicroBlocks en Ubuntu: se reconoce el puerto pero no conecta</font>
MicroBlocks reconoce deb/ttyusb0, se conecta pero el IDE dice "Placa no conectada" cuando se intenta hacer funcionar algo y el icono de conexión no se pone en verde ni muestra el nombre de la placa con la que está conectado.

Este problema es muy común en distribuciones basadas en Linux (Debian, Ubuntu, Mint) al usar MicroBlocks. El síntoma "reconoce el puerto pero no conecta" indica que el puerto serie /dev/ttyUSB0 o /dev/ttyACM0 existe, pero MicroBlocks no tiene permisos para usarlo o hay otro proceso bloqueándolo.

!!! Note "Nota:"
    Asegúrate de que el cable USB no sea solo de carga; debe ser un cable de datos.

### ==**Desinstalar brltty (Causa principal)**==

Muchas distribuciones actuales instalan por defecto brltty, un servicio de soporte para pantallas Braille que secuestra los puertos serie USB al detectar la placa, impidiendo que MicroBlocks la use.

* Ejecuta esto en tu terminal:

```bash
sudo apt remove brltty
```

* Desconecta y reconecta tu placa.

El problema se puede dar después de realizar actualizaciones del Sistema Operativo.

### ==**Dar permisos de usuario (dialout/tty)**==

Tu usuario debe pertenecer al grupo que gestiona los puertos serie para poder acceder a /dev/ttyUSB0.

* Ejecuta:

```bash
sudo usermod -a -G dialout $USER # $USER es tu nombre de usuario
sudo usermod -a -G tty $USER # $USER es tu nombre de usuario
```

**Importante:** Para que esto tenga efecto debes cerrar sesión y volver a entrar, o bien reiniciar el equipo.

### ==**Verificar Firmware/Modo de la Placa**==

A veces MicroBlocks reconoce que hay un dispositivo, pero no puede comunicarse porque la placa no tiene el firmware o la máquina virtual (VM) necesaria.

* Ve al menú de configuración (icono de engranaje) y selecciona "update firmware on board" (actualizar firmware en la placa).
* Si la placa está "colgada", intenta presionar el botón de Reset de la placa mientras la conectas a MicroBlocks.

### ==**Resumen de comandos de diagnóstico**==

* Para ver si tienes permisos crw-rw----:

```shell
ls -l /dev/ttyUSB0
```

* Para ver si el sistema detecta la placa realmente como /dev/ttyUSB0 o ttyACM0:

```shell 
dmesg | grep tty ()
```

* Para verificar si estás en dialout:

```powershell
groups
```

## <FONT COLOR=#8B008B>Problemas de conexión serie</font>
Para solucionar problemas de conexión serie en Chromium, Chrome o Edge, puedes ajustar ciertas funciones experimentales en ```chrome://flags```. Estas opciones optimizan cómo el navegador interactúa con el hardware.

### ==**Habilitar soporte WebSerial en todas las situaciones**==

* **Flag**: ```chrome://flags/#enable-experimental-web-platform-features```
* **Qué hace**: Activa las APIs de plataforma web experimental. Aunque WebSerial ya es estándar, habilitar esto suele resolver errores donde Chromium no "ve" dispositivos específicos o protocolos más nuevos.
* **Acción**: Cámbialo a Enabled.

### ==**Permitir acceso a dispositivos serie "no convencionales"**==

* **Flag**: ```chrome://flags/#enable-web-serial-service-mode-ids``` (Nota: el nombre exacto puede variar según la versión de Chromium).
* **Qué hace**: Permite que Chromium reconozca una gama más amplia de IDs de fabricantes (VID/PID). Algunas placas chinas con chips CH340 o CP2102 a veces necesitan este empujón extra para ser detectadas correctamente.
* **Acción**: Cámbialo a Enabled.

### ==**Soporte para puertos serie vía Bluetooth (si usas ESP32)**==

* **Flag**: ```chrome://flags/#enable-web-bluetooth-new-permissions-backend```
* ***Qué hace***: Si intentas conectar MicroBlocks por Bluetooth en lugar de cable, esto mejora la gestión de permisos para que la ventana de emparejamiento no falle.
* **Acción**: Cámbialo a Enabled.

### ==**Importante: Pasos tras cambiar los Flags**==

1. Haz clic en el botón Relaunch (Reiniciar) que aparece abajo a la derecha.
2. Si sigues teniendo problemas de "Placa no conectada" en Linux después de tocar los flags, recuerda que el navegador no puede saltarse las restricciones del sistema: el paso de sudo apt remove brltty es obligatorio porque ese servicio desconecta el dispositivo antes de que el navegador llegue a tocarlo.
