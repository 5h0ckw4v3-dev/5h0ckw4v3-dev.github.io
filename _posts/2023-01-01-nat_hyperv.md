---
title: Crear red personalizada en Hyper-V 
author: 5h0ckw4v3-dev
date: '2023-01-01 10:00:00 +0100'
categories:
  - Windows
tags:
  - Powershell
  - Hyper-V
render_with_liquid: false
published: true
---
![Hyper-V](/assets/img/common/mhv.png)

Comenzamos el año con una entrada, la cual en mi experiencia, es muy importante. Desde hace unos años uso Hyper-V como gestor de máquinas virtuales en Windows y uno de los problemas que tenian versiones anteriores era la gestion de la red. Con este pequeño truco no volveremos a tener problemas en la conexión a internet de nuesta virtual aunque el host cambie de cable a wifi (muy común en Hyper-V que ocurra esto).

## Requisitos
Tenemos que identificar un rango de IPs que no vaya a tener problemas con otras redes. Por ejemplo 192.168.200.0/24.

Abrimos Powershell como administrador.

## Procedimiento

Crearemos un switch interno llamado MiSwitchInterno.

```plaintext
New-VMSwitch -SwitchName "MiSwitchInterno" -SwitchType Internal
```

Vamos a listar los adaptadores y debemos apuntar la numeración del ifIndex de nuestro switch.

```plaintext
Get-NetAdapter
```

Una vez identificado, procedemos a configurar una IP fija en el switch (por ejemplo 74).

```plaintext
New-NetIPAddress -IPAddress 192.168.200.2 -PrefixLength 24 -InterfaceIndex 74
```

Por último creamos la NAT para darle salida a internet a nuestro switch.

```plaintext
New-NetNat -Name MiNatSwitch -InternalIPInterfaceAddressPrefix 192.168.200.0/24
```

De esta manera, podemos crear una subred donde al asignar IPs fijas a las máquinas virtuales ya no tendremos problemas de conectividad.

Por ejemplo, con los datos suministrados, al asignar este switch interno no obtendria internet, debemos asignarle una IP fija dentro del rango 192.168.200.0/24 y obligatoriamente poner de puerta de enlace 192.168.200.2. De DNS puede ponerse los de Google o Cloudflare. 

Al usar todas el mismo rango, podemos incluso crear clusters o realizar pruebas entre ellas sin problema.

El mundo de las redes es muy bonito pero a la vez complejo, espero haberme explicado correctamente 😉

Otros comandos de interés pueden ser:

Ver los NATs

```plaintext
Get-NetNat
```

Eliminar un NAT

```plaintext
Remove-NetNat -Name MiNatSwitch
```


