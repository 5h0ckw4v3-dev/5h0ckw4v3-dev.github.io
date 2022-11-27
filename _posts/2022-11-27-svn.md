---
title: Instalación y configuración de SVN en Ubuntu 
author: 5h0ckw4v3-dev
date: '2022-11-27 09:30:00 +0100'
categories:
  - Linux
tags:
  - Linux
  - Aplicaciones
render_with_liquid: false
published: true
---
![SVN](/assets/img/common/svn.png)

El término SVN se refiere a un software de control de versiones y existe desde hace mucho tiempo y aunque aplicaciones del tipo git (como el Github donde estas leyendo esto) o bitbucket puedan ser la evolución natural, lo cierto es que SVN se sigue usando.

Vamos a realizar una instalación básica en Ubuntu y puede ser extrapolable a cualquier Linux.

## Instalación.
Realizamos la instalación de todos los paquetes necesarios.

```plaintext
sudo apt install apache2 subversion libapache2-svn libsvn-dev
```

Seguidamente habilitaremos los modulos necesarios en Apache.

```plaintext
sudo a2enmod dav
sudo a2enmod dav_svn
```
Y reiniciamos el servicio de Apache como de costumbre para aplicar los cambios.

```plaintext
sudo systemctl restart apache
```

## Configuración.
Crearemos el directorio dedicado a subversión y otorgaremos los permisos necesarios.

```plaintext
mkdir /var/www/svn
chmod -R 755 /var/www/svn
chown -R www-data:www-data /var/www/svn
```
Y ahora vamos a crear nuestro primer repositorio de pruebas.

```plaintext
svnadmin create /var/www/svn/repositorio_prueba
```
Una vez tenemos toda la estructura de carpetas completada, vamos a configurar Apache.
Editamos el siguiente archivo.

```plaintext
sudo nano /etc/apache2/mods-enabled/dav_svn.conf
```
Y añadimos lo siguiente.

```plaintext
Alias /svn /var/www/svn
<Location /svn>
AuthType Basic
AuthName “Subversion Repository”
AuthUserFile /etc/apache2/svn.passwd
Require valid-user
<Location>
```

Vamos ha crear un usuario para que pueda acceder al repositorio.

```plaintext
sudo htpasswd -cm /etc/apache2/svn.passwd sh0ck
```

Reiniciamos el servicio de apache y accedemos a la url de nuestro repositorio para probarlo. Nos pedirá introducir las credenciales 

```plaintext
http://localhost/repositorio_prueba
```
