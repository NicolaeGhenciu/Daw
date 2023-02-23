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

Instalamos php, mysql y todo lo necesario una vez hecho esto vamos a configurar el servidor Apache para ejecutar PHP e introducimos el siguente comando:

``` sudo nano /etc/apache2/mods-enabled/dir.conf ```

y cambiamos la lista de indices para dejarla en el siguente orden:

``` ruby
DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
```
Comprobamos que en este archivo

```
root@ubuntu-20:/etc/apache2/mods-available# sudo nano php7.4.conf
```
contenga:
``` ruby

<FilesMatch ".+\.ph(ar|p|tml)$">
    SetHandler application/x-httpd-php
</FilesMatch>

```

Ahora vamos a instalar pyhton.

``` 
apt-get install python3 libexpat1 -y 
apt-get install apache2 apache2-utils ssl-cert libapache2-mod-wsgi -y
```

Ahora configuramos el Apache para el mod wsgi:
```
nano /var/www/html/wsgy.py

def application(environ,start_response):
    status = '200 OK'
    html = 'html>\n' \
           '<body>\n' \
           '<div style="width: 100% color: blue; font-size: 40px; font-weight: bold; text-align: center;">\n' \
           'Bienvenido a mi pagina con python\n' \
           '</div>\n' \
           '</body>\n' \
           '</html>\n'
    response_header = [('Content-type','text/html')]
    start_response(status,response_header)
    return [html]

```

Ahora guardamos y ejecutamos este comando:

```chown www-data:www-data /var/www/html/wsgy.py```

```nano /etc/apache2/conf-available/wsgi.conf```

y añadimos la siguente linea:

```WSGIScriptAlias /wsgi /var/www/html/wsgy.py```

Instalamos el bind y hacemos las configuraciones pertinentes y creamos el script.

### Aqui el Script

```a2enconf wsgi
systemctl restart apache2```


Script:

``` ruby
#! /bin/bash

user=$1

if [ -z "$user" ]; then
  echo "Debe proporcionar un nombre de usuario como argumento."
  exit 1
fi

mysql -u root -e "CREATE DATABASE $user;"

mysql -u root -e "USE $user;"

mysql -u root -e "CREATE USER '$user'@'localhost' IDENTIFIED BY '12345';GRANT ALL PRIVILEGES ON $user.* TO '$user'@'localhost';"

mkdir /var/www/html/$user
touch /var/www/html/$user/index.html
echo "<!DOCTYPE html><html><head><title>Bienvenido a mi pagina web</title></head><body><h1>Bienvenido a mi sitio web</h1><p>Esta es mi pagina de bienvenida.</p></body></html>" >> /var/www/html/$user/index.html

sudo adduser $user
sudo service vsftpd restart

#DNS
echo "$user.localnet.net.     IN     A     192.168.85.168" >> /etc/bind/db.localnet.net

#DNS inverso
echo "168      IN      PTR      $user.localnet.net." >> /etc/bind/db.85.168.192

systemctl reload bind9
```

Ejecutamos el script:

``` ruby
./ScriptDaw.sh [Parametro]
```

Insertamos el nombre y la contraseña:

![imagen1](img/1.png)

Cerramos sesion y nos dirijimos al usuario que hemos creado con el script.

![imagen2](img/3.png)

ponemos localhost/nicoaleadrian que es el usuario que hemos creado para ver nuestra pagina web.

![imagen3](img/3.png)

Ahora nos dirijimos al localhost/nicolaeadrian/phpmyadmin para comprobar que nos ha creado la base de datos e introducimos nuestras credenciales.

![imagen4](img/4.png)

Y listo ya tenemos acceso a nuestra base de datos:

![imagen5](img/5.png)

Probamos si ejecuta aplicaciones python

localhost/wsgi

![imagen7](img/7.png)

Ahora nos vamos a conectar mediante FTP con filezilla

comprobamos que el servicio este activo:

![imagen15](img/15.png)

Introducimos en el servidor nuestra ip y en el puerto ponemos 21, luego ponermos nuestras credenciales y deberia de conectarnos perfectamente:

![imagen11](img/11.png)

![imagen12](img/12.png)

Nos vamos a conectar por ssh al usuario creado con el script nicolaeadrian usando mobaXterm

Primero comprobamos que el ssh este activo:

![imagen108](img/109.png)

Ahora creamos una sesion y ponemos el usuario y la contraseña

![imagen109](img/108.png)

Si todo ha salido bien nos tendria que salir esto:

![imagen110](img/110.png)

![imagen107](img/107.png)

Nos conectamos tambien con ftp

![imagen106](img/106.png)
![imagen105](img/105.png)

Le vamos a subir mediante ftp una foto logo.png a la sesion de nicolaeadrian

![imagen104](img/104.png)

Ahora mediante ssh vamos a la carpeta descargas y ahi podemos ver la foto

![imagen102](img/102.png)
![imagen103](img/103.png)
![imagen101](img/101.png)

Ahora vamos a comprobar el dns, primero ponemos el ping:

![imagen17](img/17.png)

y ahora el nslookup:

![imagen18](img/18.png)

Aqui el archivo de dns:

![imagen10](img/10.png)

y aqui el de dns inversa

![imagen9](img/9.png)



