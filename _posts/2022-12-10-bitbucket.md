---
title: Instalación y configuración de Bitbucket Server en Ubuntu 
author: 5h0ckw4v3-dev
date: '2022-12-10 09:30:00 +0100'
categories:
  - Linux
tags:
  - Linux
  - Aplicaciones
render_with_liquid: false
published: true
---

Tal y como vimos en el post anterior con la instalación de Subversion, hoy vamos a ver como instalar Bitbucket server en nuestro servidor Ubuntu. Para extrapolarlo a otro linux tenemos que tener en cuenta las dependencias ya que en RHEL o CentOS las versiones de Git en los repositorios son antiguas y hay que instalarlo compilando desde la fuente.

## Descarga.
Podemos hacer la descarga de tres maneras distintas. Ejecutable .bin y los comprimidos tar.gz y zip. En este caso vamos a hacerlo con .bin. Lo descargamos de la siguiente URL https://www.atlassian.com/es/software/bitbucket/download-archives

## Requisitos.
Si hacemos la instalacion con el .bin solamente necesitamos una version actualizada de Git. En Ubuntu no hay problema asi que instalamos normalmente.

```plaintext
sudo apt install git
```

En RHEL y derivados tenemos que instalar Git desde la fuente ya que los repositorios no tienen una versión actualizada del mismo.
Básicamente la instalación seria algo asi:

```plaintext
sudo yum -y remove git*
sudo yum -y install epel-release
sudo yum -y groupinstall "Development Tools"
sudo yum -y install wget perl-CPAN gettext-devel perl-devel openssl-devel zlib-devel curl-devel expat-devel getopt asciidoc xmlto docbook2X curl
sudo ln -s /usr/bin/db2x_docbook2texi /usr/bin/docbook2x-texi
	--  --  -- 
export VER="v2.38.1"
wget https://github.com/git/git/archive/${VER}.tar.gz
tar -xvf ${VER}.tar.gz
rm -f ${VER}.tar.gz
cd git-*
make configure
sudo ./configure --prefix=/usr
sudo make
sudo make install
```
Y la desinstalación en caso de necesitarlo 😉:

```plaintext
./configure
sudo make && sudo make DESTDIR=/var/tmp/git install
sudo find /var/tmp/git -type f -printf '/%P\n' | sudo xargs -n 10 rm -f
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
