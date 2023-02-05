# Practica 1 - Servidor alojamiento web

## Se pide las instalación, configuración y puesta en marcha de un servidor que ofrezca servicio de alojamiento web configurable

* Se dará alojamiento a páginas web tanto estáticas como dinámicas con “php”
* Los clientes dispondrán de un directorio de usuario con una página web por defecto.
* Además contarán con una base de datos sql que podrán administrar con phpmyadmin
* Los clientes podrán acceder mediante ftp para la administración de archivos configurando adecuadamente TLS
* Se habilitará el acceso mediante ssh y sftp.
* Se configura de forma adecuada postfix y dovecot imap y pop3
* Se automatizará mediante el uso de scripts:
    * La creación de usuarios y del directorio correspondiente para el alojamiento web
    * Host virtual en apache
    * Creación de usuario del sistema para acceso a ftp, ssh, smtp, …
    * Se creará un subdominio en el servidor DNS con las resolución directa e inversa
    * Se creará una base de datos además de un usuario con todos los permisos sobre dicha base de datos (ALL PRIVILEGES)
    * Se habilitará la ejecución de aplicaciones Python con el servidor web

### Para la instalación, configuración y puesta en marcha del servidor mencionado usare Ubuntu-Desktop.

Lo primero que vamos a hacer es actualizar los paquetes con:

``` sudo apt-get update ```

Ahora vamos a instalar Apache2, PHP y libapache2-mod-php que es un módulo de Apache que permite a Apache interactuar con PHP.

``` sudo apt-get install apache2 php libapache2-mod-php ```

Probamos el servidor en el navegador:

![imagen1](img/1.png)

Ahora Instalamos MySQL y PhpMyAdmin con el siguente comando:

``` sudo apt-get install mysql-server php-mysql phpmyadmin ```

Ahora copiamos el archivo de configuración de phpMyAdmin para Apache:

 ``` sudo cp /etc/phpmyadmin/apache.conf /etc/apache2/sites-available/phpmyadmin.conf ```
 
Habilitamos la configuración de phpMyAdmin en Apache:

``` sudo a2ensite phpmyadmin.conf ```

Y reiniciamos Apache para que los cambios surtan efecto:

```sudo service apache2 restart ```


