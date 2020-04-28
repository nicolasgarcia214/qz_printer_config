# Suprimir el modal

El modal de **QZ tray** -> " _localhost wants to access connected printers Untrusted Website_** " que advierte de una conexión no confiable, es innecesario en un sistema embebido que se ejecute localmente, puesto que no se requieren cifrar los mensajes.No suprimirlo perjudicaría la experiencia de usuario y la interfaz del producto. A continuación se listaran una serie de pasos para deshabilitar el aviso.

<br />
<p align="center">
  <img src="https://i.imgur.com/uoHcg0r.png" alt="Modal de advertencia"/>
</p>
<br />


1.  Clonar el repositorio QZ Tray ( [https://github.com/qzind/tray.git](https://github.com/qzind/tray.git) ).
2.  Descargar las dependencias de compilación: Ant, Java, NSIS (Windows).

|LINUX  (DEBIAN)                               |WINDOWS                      |
|----------------------------------------------|-----------------------------|
|***OpenJDK***  							   |***JDK8***  	                             |
|`sudo apt-get purge openjdk-8-jre-headless`   |[https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)            |
|`sudo apt-get install openjdk-8-jre-headless` |Aceptar los términos y condiciones         |
|`sudo apt-get install openjdk-8-jre`          |Sino tiene cuenta en Oracle, crearla|
|`sudo apt-get install openjdk-8-jdk`          |Descargar versión compatible con el equipo (x64 o x86)|
|`sudo apt-get install openjfx`                |Ejecutar el .exe y seguir los pasos|
|***Build Tools***          				   |***Apache Ant y NSIS***  |
|`sudo apt-get install ant makeself`           |[https://ant.apache.org/bindownload.cgi](https://ant.apache.org/bindownload.cgi)|
|          |[https://sourceforge.net/projects/nsis/](https://sourceforge.net/projects/nsis/)|

3.  Ir al tag v2.0.11
	```
	git checkout v2.0.11
	```
4.  Entrar al archivo [`/src/qz/ws/PrintSocketClient.java`](https://github.com/qzind/tray/blob/6a3d0cc2fd4708b04eb92cba38f3bb35f93861bf/src/qz/ws/PrintSocketClient.java#L476)  y cambiar la linea  `476`
    
    De:
    
    ```
    if (cert.isTrusted() && cert.isSaved()) {
    ```
    
    A:
    
    ```
    if (cert.isSaved()) {
    ```
    
5.  Entrar al archivo  [`/src/qz/ui/GatewayDialog.java`](https://github.com/qzind/tray/blob/6a3d0cc2fd4708b04eb92cba38f3bb35f93861bf/src/qz/ui/GatewayDialog.java#L92) y cambiar la linea  `92`
    
    De:
    
    ```
    allowButton.setEnabled(!persistentCheckBox.isSelected() || cert.isTrusted());
    ```
    
    A:
    
    ```
    allowButton.setEnabled(true);
    ```
    
6.  Compilar:
    
    -   `ant nsis`  para Windows
    -   `ant makeself`  para Linux

	

> Nota: Para windows es necesario agregar la variable de entorno
> **JAVA_HOME** que va a ser igual a la ruta del jdk en los archivos de
> programa y a la variable, ya existente, **Path**, agregarle al final la
> ruta del bin del apache ant y la ruta del NSIS.

7.  Instalar QZ tray utilizando el .exe o .run generado.
    
8.  La primera vez que se ejecute el programa saldrá el modal pero ahora se podrá recordar la decisión de permitir  la conexión por siempre.

# Configurar la impresora para Debian

El proceso de configuración de una impresora en esta distribución de linux consta de los siguientes pasos:

1. Instalar cups y cups-bsd

	`sudo apt-get install cups cups-bsd`
2. Entrar al archivo  [`/etc/cups/cupsd.conf`] y cambiarlo
    
    De:
    
    ```
    # All administration operations require an administrator to authenticate...
	  <Limit CUPS-Add-Modify-Printer CUPS-Delete-Printer CUPS-Add-Modify-Class CUPS-Delete-Class CUPS-Set-Default CUPS-Get-Devices>
	    AuthType Default
		Require user @SYSTEM
	    Order deny,allow
	  </Limit>
    ```
    
    A:
    
    ```
    # All administration operations require an administrator to authenticate...
	  <Limit CUPS-Add-Modify-Printer CUPS-Delete-Printer CUPS-Add-Modify-Class CUPS-Delete-Class CUPS-Set-Default CUPS-Get-Devices>
	    #AuthType Default
		#Require user @SYSTEM
	    Order deny,allow
	  </Limit>
    ```
3. Ingresar a [localhost:631/admin](http://localhost:631/admin)
4. Por ultimo, dar click en el botón **Add Printer**, seguir el paso a paso y configurar el driver según el tipo de impresora

<p align="center">
  <img src="https://www.securitronlinux.com/wp-content/uploads/2012/05/add-printer.jpeg" alt="Interfaz de cups"/>
</p>

