---
title: Nextcloud en Docker
author: 5h0ckw4v3-dev
date: '2024-01-15 10:00:00 +0100'
categories:
  - Linux
tags:
  - Linux
  - Shell
  - Terminal
render_with_liquid: false
published: true
---

![command](/assets/img/common/nextcloud.png)



## INTRODUCCIÓN

Hoy vamos a ver la instalación del aplicativo de Nextcloud usando Docker en una Raspberry Pi 4, tambien veremos la instalación de Portainer para gestionar y configurar de una manera visual y desde el navegador nuestros diferentes contenedores y sus opciones. Procederemos a instalar los requisitos minimos.

**Instalación de Portainer:**

Este paso no es necesario, pero despues de la experiencia que he tenido con el, me parece superinteresante y recomendable instalarlo.
```plaintext
sudo docker pull portainer/portainer-ce:latest
```
```plaintext
sudo docker run -d -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```
Una vez termina la instalación y la miniconfiguración que hemos aplicado en el segundo comando, nos dirijimos a http://[DIRECCIÓN-IP]:9000 


**Instalación de Nextcloud:**

Realizamos la instalación desde el mismo repositorio de Docker.
```plaintext
docker pull nextcloud
```
```plaintext
docker run -d -p 8080:80 --name nextcloud nextcloud
```
```plaintext
docker logs nextcloud
```

**Instalación de MariaDB:**

Probando la configuración de Portainer, realicé la instalación desde el mismo en la sección App Templates


## DESARROLLO

Muy importante, ambos contenedores deben estar en la misma red, lo mas recomendable es usar una conexión puente. O usamos la conexión por defecto como he hecho yo o creamos una nueva conexión puente y asi podemos crear distintas islas de conexiones con los diferentes servicios que a su vez interactúan entre si.

![command](/assets/img/common/nextcloud1.png)

Al iniciar Nextcloud nos indica que por defecto usa SQL Lite y no lo recomienda para el uso de clientes, es nuestro caso, asi que vamos a configurar la bbdd en el contenedor de MariaDB.

![command](/assets/img/common/nextcloud2.png)

Nos conectamos a nuestra raspberry y listamos los contenedores.

![command](/assets/img/common/nextcloud3.png)


Usamos Docker exec para conectarnos al contenedor deseado.

![command](/assets/img/common/nextcloud4.png)

Realizamos la conexión a MariaDB, la contraseña es la que configuramos cuando realizamos la instalación desde Portainer.

![command](/assets/img/common/nextcloud5.png)

Creamos la BBDD
```plaintext
CREATE DATABASE nextcloud;
```
Creamos el usuario
```plaintext
CREATE USER ‘nextcloud’@’%’ IDENTIFIED BY ‘clavequequeramosponer;
```
 (Importante el usuario con %)

Damos permisos al usuario en la BBDD
```plaintext
GRANT ALL PRIVILEGES ON nextcloud.* TO nextcloud@’%’;
```

Aplicamos los cambios
```plaintext
FLUSH PRIVILEGES;
```

Nos dirijimos a la web de nuestro Nextcloud y rellenamos los datos.

![command](/assets/img/common/nextcloud6.png)

Le damos a Instalar y tardará unos minutos, podéis ir a tomar un café perfectamente :D
Y cuando termina el proceso de instalación, ya tenemos la nube lista.

![command](/assets/img/common/nextcloud7.png)


## CONCLUSIÓN

Podriamos haber montado un stack para Nextcloud pero me ha parecido desaprovechar una instancia de BBDD que podria usar para algun que otro proyecto, por eso la he realizado en contenedores distintos.
