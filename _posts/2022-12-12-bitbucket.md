---
title: Instalación y configuración de Bitbucket Server en Ubuntu 
author: 5h0ckw4v3-dev
date: '2022-12-12 09:30:00 +0100'
categories:
  - Linux
tags:
  - Linux
  - Aplicaciones
render_with_liquid: false
published: true
---
![SVN](/assets/img/common/svn.png)

Tal y como vimos en el post anterior con la instalación de Subversion, hoy vamos a ver como instalar Bitbucket server en nuestro servidor Ubuntu. Para extrapolarlo a otro linux tenemos que tener en cuenta las dependencias ya que en RHEL o CentOS las versiones de Git en los repositorios son antiguas y hay que instalarlo compilando desde la fuente.

## Descarga.
Podemos hacer la descarga de tres maneras distintas. Ejecutable .bin y los comprimidos tar.gz y zip. En este caso vamos a hacerlo con .bin. Lo descargamos de la siguiente URL https://www.atlassian.com/es/software/bitbucket/download-archives

## Requisitos.
Si hacemos la instalacion con el .bin solamente necesitamos una version actualizada de Git. En Ubuntu no hay problema asi que instalamos normalmente.

```plaintext
sudo apt install git
```

## Instalación.
Damos permisos de ejecución y ejecutamos el archivo.

```plaintext
chmod +x atlassian-bitbucket-8.6.1-x64.bin
./atlassian-bitbucket-8.6.1-x64.bin
```
En las siguientes imágenes podemos ver las preguntas que va realizando y la respuesta. 

![bitbucket](/assets/img/common/bitbucket-1.jpg)

![bitbucket](/assets/img/common/bitbucket-2.jpg)

Una vez termine el proceso accedemos a la url.

```plaintext
http://IP_Servidor:7990/setup
```

Y veremos lo siguiente

![bitbucket](/assets/img/common/bitbucket-3.jpg)

Seguidamente necesitaremos la licencia de instalación. Para un server podemos solicitar una evaluación de 90 dias simplemente registrandonos en la web. Una vez hecho veremos algo así.

![bitbucket](/assets/img/common/bitbucket-4.jpg)

Una vez tenemos la licencia solo nos queda crear el usuario.

![bitbucket](/assets/img/common/bitbucket-5.jpg)

Iniciamos sesión y ya estamos dentro.

![bitbucket](/assets/img/common/bitbucket-6.jpg)

A disfrutarlo! ;)
