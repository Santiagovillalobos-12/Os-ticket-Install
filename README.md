# Documentacion de instalación Os-ticket en ubuntu 

## Este es el enlace de la documentacion
(https://computingforgeeks.com/how-to-install-osticket-on-ubuntu-linux/)

### Paso 1: actualiza el sistem

```sh
sudo apt-get update
sudo apt-get upgrade (opcional para actualizar el sistema operativo)
```

También puede establecer el nombre de host del servidor (opcional):

```sh
sudo hostnamectl set-hostname osticket.computingforgeeks.com
```

### Paso 2: instalar y configurar la base de datos MySQL / MariaDB

Necesitará una base de datos MySQL con un usuario , contraseña y nombre de host válidos a mano durante la instalación. El usuario de MySQL especificado debe tener privilegios COMPLETOS en la base de datos creada. si aún no tiene un servidor de base de datos instalado, consulte nuestra guía sobre cómo  instalar MariaDB 10 en Ubuntu

También puede ejecutar los siguientes comandos para instalar mariadb desde los repositorios OS APT:

```sh
sudo apt update
sudo apt install mariadb-server
```
Asegure su servidor de base de datos:

```sh
$ sudo mysql_secure_installation
```
Cambie el complemento de autenticación para poder iniciar sesión como usuario normal con credenciales raíz.

```sh
sudo mysql -u root
UPDATE mysql.user SET plugin = 'mysql_native_password' WHERE User = 'root';
FLUSH PRIVILEGES;
QUIT;
```
Confirma que está funcionando:

```sh
mysql -u root -p

(Deberia salir el siguiente mensaje)

Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 56
Server version: 10.3.22-MariaDB-1ubuntu1 Ubuntu 20.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> 
```

Una vez instalado el servidor MariaDB / MySQL, proceda a crear una base de datos para osTicket. Inicie sesión en su servidor de base de datos como usuario root y cree una base de datos para osTicket:

```sh
mysql -u root -p
CREATE DATABASE osticket_db;
GRANT ALL PRIVILEGES ON osticket_db.* TO osticket_user@localhost IDENTIFIED BY "Str0ngDBP@ssw0rd";
FLUSH PRIVILEGES;
QUIT;
```

Una vez que la base de datos esté lista, proceda a instalar el servidor web Apache:

### Paso 3: instalar Apache en Ubuntu

En Ubuntu, puede instalar el servidor web Apache desde el repositorio oficial de apt:

```sh
sudo apt install apache2
```

Para iniciar el servicio manualmente, ejecute:

```sh
sudo systemctl start apache2
```

Aunque el servicio está habilitado para iniciarse en el arranque de forma predeterminada, si lo permite manualmente, debe ejecutar:


```sh
sudo systemctl enable apache2
```

### Paso 4: instalar PHP

El siguiente paso es instalar PHP en Ubuntu:

```sh
sudo apt update
sudo apt-get install php php-{fpm,pear,imap,apcu,intl,cgi,common,mbstring,net-socket,gd,xml-util,mysql,bcmath}
```

La versión de PHP que se instalará es  v7.2 para Ubuntu 18.04 y v7.4 para Ubuntu 20.04.


```sh
$ php -v
PHP 7.4.3 (cli) (built: Mar 26 2020 20:24:23) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
    with Zend OPcache v7.4.3, Copyright (c), by Zend Technologies
```

### Paso 5: Descargue e instale osTicket en Ubuntu 20.04 / 18.04

En este punto, debería estar listo para descargar la última versión de osTicket. Luego descomprima los archivos y coloque un directorio de su elección en la raíz del documento web del servidor.

En el momento de escribir estas líneas, la última versión de osTicket es. v1.14.2.Confirme la versión actual antes de descargarla.

```sh
sudo apt-get install curl wget unzip
```

Descargue la última versión de osTicket:

```sh
curl -s https://api.github.com/repos/osTicket/osTicket/releases/latest \
| grep browser_download_url \
| grep "browser_download_url" \
| cut -d '"' -f 4 \
| wget -i -
```

Extraiga el archivo descargado:

```sh
unzip osTicket-v*.zip -d osTicket
```

Obtendrá dos directorios después de la extracción: (scripts) y (upload)

```sh
ls osTicket
```

Cree un directorio web para osTicket y mv estos directorios en él.

```sh
sudo mv osTicket /var/www/
```

A continuación, cree un archivo de configuración de osTicket:

```sh
cd /var/www/osTicket/upload/include
sudo cp ost-sampleconfig.php ost-config.php
```

Cambie la propiedad del directorio web osTicket a usuario **www-data**y grupo.

```sh
sudo chown -R www-data:www-data /var/www/
```

### Paso 6: configurar el servidor web Apache

Cree el archivo de configuración de VirtualHost para osTicket en el directorio de configuraciones de Apache:

```sh
sudo nano /etc/apache2/sites-enabled/osticket.conf
```

Agregar contenido:

```sh
<VirtualHost *:80>
     ServerAdmin admin@example.com
     DocumentRoot /var/www/osTicket/upload
     ServerName osticket.computingforgeeks.com
     ServerAlias www.osticket.computingforgeeks.com
     <Directory /var/www/osTicket/>
          Options FollowSymlinks
          AllowOverride All
          Require all granted
     </Directory>

     ErrorLog ${APACHE_LOG_DIR}/osticket_error.log
     CustomLog ${APACHE_LOG_DIR}/osticket_access.log combined
</VirtualHost>
```

Reemplazar:

* **osticket . Computerforgeeks . com** con su nombre de dominio.
* **/ var / www / osTicket / upload** con la ruta a sus archivos osTicket.

Confirme la sintaxis de configuración:


```sh
sudo apachectl -t 
(Deberia aparecer el siguiente mensaje)

Sintaxis correcta
```

Reinicie apache2:

```sh
sudo systemctl restart apache2
```

### Paso 7: Instale y configure osTicket en Ubuntu 20.04 / 18.04

Ahora que todo está configurado, terminemos la instalación configurando osTicket desde la interfaz de usuario. abra **http : // dominio . com** en su navegador web favorito. La primera página se verá a continuación:

![alt text](https://computingforgeeks.com/wp-content/uploads/2018/06/os-ticket-install-ubuntu-18.04-01.png?ezimgfmt=ng:webp/ngcb8)
