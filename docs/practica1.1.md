<h1 style="text-align: center;">LAMP Stack en Ubuntu Server</h1>
<p style="text-align: center;">Curso 2 ASIR  - Olga Valle </p>

## Índice

1. [Repositorio de GitHub](#1-repositorio-de-github)
2. [Instalar y configurar LAMP](#2-instalar-y-configurar-lamp)
3. [Configuración de Apache](#3-configuración-de-apache)
4. [Instalar PHP y módulos PHP y MySQL](#4-instalar-php-y-módulos-php-y-mysql)
5. [Instalar MySQL](#5-instalar-mysql)
6. [Host virtual basado en el nombre de dominio](#6-host-virtual-basado-en-el-nombre-de-dominio)
7. [Variables de entorno](#7-variables-de-entorno)
8. [Instalamos phpMyAdmin](#8-instalamos-phpmyadmin)  
   8.1 [Instalar phpMyAdmin para acceder vía web a MySQL](#81-instalar-phpmyadmin-para-acceder-vía-web-a-mysql)
9. [Instalación de Adminer para acceder vía web a MySQL](#9-instalación-de-adminer-para-acceder-vía-web-a-mysql)    
10. [Instalar un analizador de logs para Apache Server](#10-instalar-un-analizador-de-logs-para-apache-server)  
    10.1 [GoAccess](#101-goaccess)   
       10.1.1 [Control de acceso a un directorio con autenticación básica utilizando 000.default.conf](#1011-control-de-acceso-a-un-directorio-con-autenticación-básica-utilizando-000defaultconf)  
       10.1.2 [Control de acceso usando .htaccess](#1012-control-de-acceso-usando-htaccess)



## 1. Repositorio de GitHub 
1. instalamos Git:
``` 
sudo apt update
sudo apt install git 
```
2. Configuramos Git 
```
git config --global user.name "Tu_Nombre"
git config --global user.email tu_email@example.com
```
3. Clonar un repositorio de GitHub en Visual Studio:

```
Git clone y el enlace de tu repositorio https://github.com/usuario/repositorio.git 
```

Visual Studio nos facilita el proceso de control de versiones con Git al integrar opciones gráficas que eliminan la necesidad de usar comandos manuales.

`Git commint -m “descripcion” `  
`Git pull`  
`Git push`   
En el panel izquierdo, dentro de la sección de **Control de código fuente**, se nos muestra los cambios pendientes. Aquí podemos realizar el commit, hacer pull para obtener los cambios más recientes, y hacer push para enviar nuestras actualizaciones.


## Estructura del Repositorio 
```

├── README.md
├── conf
│   └── 000-default.conf
│   └── 000-default-stats.conf
│   └── 000-default-htaccess.conf
│   └── .htaccess
├── php
│   └── index.php
└── scripts
    ├── .env
    ├── install_lamp.sh
    └── install_tools.sh
```
### Creación de carpetas y archivos

1. Creo una carpeta llamada scripts
    - Creo un archivo llamado install_lamp.sh
    - Asignacion de permisos `chmod +x install-lamp.sh`
    - Ejecutamos `sudo ./install-lamp.sh`

## 2. Instalar y configurar LAMP 
Nos ubicamos en el archivo  _Install_lamp.sh_

 ### Actualizamos repositorios 
```   
sudo apt update 
```
### Actualizamos los paquetes

```   
apt upgrade -y
```
### Instalamos el servidor web Apache
```   
apt install apache2 -y
```   
### Habilitar módulos adicionales de Apache
    
```   
sudo a2enmod rewrite
``` 
### Reiniciamos el servidor apache2
```
sudo systemctl restart apache2
```
## 3. Configuración de Apache

### Creación de carpetas y archivos

 2. Crear archivos en la carpeta conf:   
   - `000-default.conf`  _configuración del sitio virtual por defecto_
   - `000-default_stats.conf`

El archivo _000-default.conf_ se encuentra en la ruta _/etc/apache2/sites-available/_, donde Apache guarda las configuraciones de los sitios disponibles. Aquí se crean o modifican los archivos que definen cómo funcionarán los sitios

### 3.1 Archivos de configuración de Apache
Los archivos de configuración de Apache se almacenan en el directorio:
```
/etc/apache2/
```
En este directorio encontramos los siguientes archivos y directorios:

```
/* Archivos */
├── apache2.conf
├── envvars
├── magic
└── ports.conf

/* Directorios */
├── conf-available
├── conf-enabled
├── mods-available
├── mods-enabled
├── sites-available 
└── sites-enabled
```

A continuación se muestra un ejemplo con el contenido del archivo _/etc/apache2/sites-available/000-default.conf_

``` apache
    <VirtualHost *:80>
        #ServerName www.example.com
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html/

        DirectoryIndex index.php index.html

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
```

1.  Copiamos el archivo de configuración de Apache  
```
cp ../conf/000-default.conf /etc/apache2/sites-available 
```
Estamos copiando el archivo de configuración _000-default.conf_ desde la carpeta _../conf/_ hacia el directorio /etc/apache2/sites-available/  Este paso es necesario para mover la configuración a la ubicación donde Apache gestiona los archivos de los sitios web.

Una vez hecho los cambios en los archivos de configuración, tenemos que reiniciar el servicio de Apache para que los cambios se apliquen.

```
sudo systemctl restart apache2
```

_Abre tu navegador y visita http://IP para comprobar que Apache está funcionando. Deberías ver la página de bienvenida de Apache._


## 4. Instalar PHP y modulos PHP y MySQL
Para que un servidor web Apache pueda procesar código PHP vamos a necesitar instalar el intérprete de PHP y algunos módulos adicionales.

```
sudo apt install php libapache2-mod-php php-mysql -y 
```

Reiniciamos el servidor apache 

```
sudo systemctl restart apache2
```
Comprobar que se hizo correctamente creamos un archivo PHP llamado `index.php` en el directorio `/var/www/html`


```
<?
php phpinfo();
 ?>
```
Copiamos el script de Prueba de PHP en `/var/www/html`

```
cp ../php/index.php /var/www/html
```

Modificamos el propietario y el grupo del archivo index.php
```
sudo chown -R www-data:www-data /var/www/html
``` 
_Ahora accede desde un navegador a la http://IP/info.php, donde IP será la dirección IP de su máquina virtual_

## 5. Instalar MySQL
```
sudo apt install mysql-server -y
```
Verifica si esta funcionando 
```
sudo systemctl status mysql
```
## 6. Host virtual basado en el nombre de dominio

### Crear directorios para los sitios web

Creamos el directorio dentro de /var/www/html/ para el sitio web

```
sudo mkdir -p /var/www/html/web1
```
 Modificamos permisos 
```
 sudo chown -R www-data:www-data /var/www/html
```
Creamos archivo de prueba para el sitio web

```
echo "Sitio web 1" | sudo tee /var/www/html/web1/index.html
```
### Configurar sitios virtuales en Apache

Contenido del archivo web1.conf:

```
<VirtualHost *:80>
    ServerAdmin webmaster@web1.com
    ServerName web1.com
    DocumentRoot /var/www/html/web1
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Apache viene con un sitio predeterminado llamado 000-default.
conf. Deshabilitas este sitio.

Deshabilitar el sitio predeterminado de Apache
```
sudo a2dissite 000-default.conf
```
Habilitar los sitios web configurados:
```
sudo a2ensite web1.conf
``` 
Para poder comprobar que los sitios virtuales están funcionando correctamente entramos con nuestra IP y el nombre del host

![web1.com](imagenes/WEB.1.jpg "Web1.com")



## 7. Variables de entorno

Crear un archivo _.env_ en la carpeta _conf_ con el siguiente contenido:

```
PHPMYADMIN_APP_PASSWORD=password
DB_USER=usuario
DG_PASSWORD=password
DB_NAME=moodle
STATS_USERNAME=ovalpen
STATS_PASSWORD=ovalpen
```
### Creación de carpetas y archivos

3. Creo una carpeta llamada scripts  
    - Creo un archivo llamado install_tools.sh
    - Asignacion de permisos `chmod +x install-tools.sh`
    - Ejecutamos `sudo ./install-tools.sh`

 Importamos el archivo de variables

``` 
source .env 
```
 Para mostrar los comandos que se van ejecutando
```
set -ex
```
Actualizamos los repositorios  `sudo apt update`  
Actualizamos los paquetes   `sudo apt upgrade`

## 8. Instalamos phpMyAdmin 

``` bash
apt install phpmyadmin php-mbstring php-zip php-gd php-json php-curl -y
```

### 8.1 Instalar phpMyAdmin para acceder vía web a MySQL

1. Seleccionar el servidor web que queremos configurar para ejecutar.

``` bash
echo "phpmyadmin phpmyadmin/reconfigure-webserver multiselect apache2" | debconf-set-selections 
```
2. Confirmar que desea utilizar dbconfig-common para configurar la base de datos.

``` bash
echo "phpmyadmin phpmyadmin/dbconfig-install boolean true" | debconf-set-selections
``` 

3. Seleccionar la contraseña para phpMyAdmin.

``` bash
echo "phpmyadmin phpmyadmin/mysql/app-pass password $PHPMYADMIN_APP_PASSWORD" | debconf-set-selections
echo "phpmyadmin phpmyadmin/app-password-confirm password $PHPMYADMIN_APP_PASSWORD" | debconf-set-selections
```
En este ejemplo estamos guardando la contraseña en la variable de entorno $PHPMYADMIN_APP_PASSWORD que tendremos que configurar previamente asignándole un valor inicial.

Comprobamos entrando con nuestra http://IP/phpmyadmin

![comprobacion](imagenes/myadmin.jpg)
 
## 9. Instalación de Adminer para acceder vía web a MySQL

Se puede descargar en archivo .php que esta disponible en [Adminer para MySQL](https://github.com/vrana/adminer/releases/download/v4.8.1/adminer-4.8.1-mysql.php)  y guardarlo en el directorio _/var/www/html_ 


1. Creamos un directorio para Adminer dentro del directorio _/var/www/html_

```
mkdir -p /var/www/html/adminer
``` 

2. Con la utilidad wget descargamos el archivo de Adminer y con el parámetro -P indicamos la ruta donde queremos guardar el archivo. 

```
wget https://github.com/vrana/adminer/releases/download/v4.8.1/adminer-4.8.1-mysql.php -P /var/www/html/adminer
```

3. Renombramos el nombre del script de Adminer

```
mv /var/www/html/adminer/adminer-4.8.1-mysql.php /var/www/html/adminer/index.php
```

4. Modificamos el propietario y el grupo del archivo

```
chown -R www-data:www-data /var/www/html/adminer
```

5. Creamos una base de datos de ejemplo

```bash 
mysql -u root <<< "DROP DATABASE IF EXISTS $DB_NAME"
mysql -u root <<< "CREATE DATABASE $DB_NAME"
mysql -u root <<< "DROP USER IF EXISTS '$DB_USER'@'%'"
mysql -u root <<< "CREATE USER '$DB_USER'@'%' IDENTIFIED BY '$DB_PASSWORD'"
mysql -u root <<< "GRANT ALL PRIVILEGES ON $DB_NAME.* TO '$DB_USER'@'%'"
```

## 10. Instalar un analizador de logs para Apache Server
### 10.1 GoAccess

Actualizamos los repositorios e instalamos GoAccess.

```
sudo apt update
sudo apt install goaccess -y
```
### 10.1.1 Control de acceso a un directorio con autenticación básica utilizando 000.default.conf

Crea un directorio llamado stats dentro del directorio _/var/www/html_ donde se podrán consultar los informes generados con goaccess. El acceso a este directorio deberá estar controlado y solo se podrá acceder mediante un usuario y una contraseña.
#### Paso 1 
```
mkdir -p /var/www/html/stats
```  
#### Paso 2 
Para ejecutar goaccess en segundo plano podemos utilizar la opción --daemonize.

```
goaccess /var/log/apache2/access.log -o /var/www/html/stats/index.html --log-format=COMBINED --real-time-html --daemonize
```
#### Paso 3
Creamos archivos de contraseña   
El archivo de contraseñas lo guardamos en un directorio seguro que no sea accesible desde el exterior. En nuestro caso el archivo se llamará .htpasswd y lo vamos a guardar en el directorio /etc/apache2
```
sudo htpasswd -bc /etc/apache2/.htpasswd 
$STATS_USERNAME
$STATS_PASSWORD
```
#### Paso 4 
Editamos el archivo configuración de Apache.
```
sudo nano /etc/apache2/sites-available/000-default.conf
```

 Añadimos la siguiente sección dentro de las etiquetas de  
<VirtualHost *:80> 

```bash 
 <VirtualHost *:80>
        #ServerName www.example.com
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html

        DirectoryIndex index.php index.html

        <Directory "/var/www/html/stats">
          AuthType Basic
          AuthName "Acceso restringido"
          AuthBasicProvider file
          AuthUserFile "/etc/apache2/.htpasswd"
          Require valid-user
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost> 
```
  
 Copia este archivo a la ubicación correcta en Apache para que pueda ser utilizado al activar el sitio con a2ensite 
 _(comando para habilitar sitios)_

```
 cp conf/000-default-stats.conf /etc/apache2/sites-available
```
#### Paso 5

Reiniciamos el servicio de Apache.
```
sudo systemctl restart apache2
```


#### Deshabilitar el sitio predeterminado  

Antes de habilitar los nuevos sitios virtuales vamos a deshabilitar el sitio virtual que viene por defecto.

```
    a2dissite 000-default-stats.conf 
```

### Habilitar el sitio predeterminado


```
a2ensite 000-default-htaccess.conf
```

### Recargar el sistema de servicios

 Vuelve a cargar los archivos de configuración de los servicios sin detener el servicio.

```
systemctl daemon-reload
systemctl reload apache2
``` 

Comprobamos entrando con nuestra http://IP/stats

![stats](imagenes/stats.jpg "Comprobacion Stats")



### 10.1.2. Control de acceso usando .htaccess
 
 Para habilitar el uso de _.htaccess_, asegúrate de que en _000-default.conf_ esté configurado AllowOverride All para las carpetas.

 #### Proteger el archivo .env con .htaccess
El contenido del archivo .htaccess será el siguiente:


```
AuthType Basic
AuthName "Acceso restringido"
AuthBasicProvider file
AuthUserFile "/etc/apache2/.htpasswd"
Require valid-user
```
Copiamos el archivo 
```
cp ../conf/.htaccess /var/www/html/stats
``` 
Comprobamos entrando con nuestra http://IP/stats

![stats](imagenes/clave.jpg "Comprobacion Stats")

 ## 11. Instalar AWStats

Instala AWStats

```
sudo apt install awstats
```

Configurar Apache para AWStats
Habilitar el módulo CGI en Apache:
```
sudo a2enmod cgi
```
Crear el archivo de configuración de AWStats en Apache
```
sudo nano /etc/apache2/conf-available/awstats.conf
``` 

Añadir la configuración para AWStats 
```apache
Alias /awstatsclasses "/usr/share/awstats/lib/"
Alias /awstats-icon "/usr/share/awstats/icon/"
Alias /awstatscss "/usr/share/doc/awstats/examples/css"

ScriptAlias /cgi-bin /usr/lib/cgi-bin/

<Directory "/usr/lib/cgi-bin/">
    Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
    AddHandler cgi-script .pl
    AllowOverride None
    Require all granted
</Directory>

Alias /awstats "/usr/lib/cgi-bin/awstats.pl"
```

Habilitar la configuración de AWStats en Apache:

```
sudo a2enconf awstats
```
Recargar Apache para aplicar los cambios

```
sudo systemctl reload apache2
```
### 11.1 Configurar AWStats para el sitio web

Copiar el archivo de configuración base de AWStats
Crea un archivo de configuración específico para tu sitio web 

```
sudo cp /etc/awstats/awstats.conf /etc/awstats/webolga.conf
```
Editar el archivo de configuración
```
sudo nano /etc/awstats/awstats.webolga.conf
```


1. Dentro del archivo, cambia lo siguiente:  
    - _SiteDomain_: Define el dominio o IP del sitio que vas a monitorear:
    - _LogFile_: Especifica la ruta del archivo de logs de Apache. Si estás utilizando logs separados para cada sitio:
    - _DirData_: Cambia la ruta del directorio donde AWStats guardará los datos procesados:

```apache
SiteDomain="web1.com"
LogFile="/var/log/apache2/webolga-access.log"
DirData="/var/lib/awstats"

```










