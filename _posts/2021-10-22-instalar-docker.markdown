---
layout: post
title:  "Instalación de Docker en Ubuntu Server 20.04"
date:   2021-10-22 09:30
categories: [Virtualización,Docker]
tags: Docker
---

En el post de hoy, os muestro una guía de instalación de Docker, siguiendo los pasos de la documentación oficial, bajo Ubuntu Server 20.04 recien instalado y sin los paquetes de Docker del repositorio de Ubuntu instalados.

Según la documentación oficial de Docker, lo primero a realizar es la desinstalación de versiones anteriores, este paso, en mi caso al ser instalación limpia lo he obviado. Si vosotros tenéis alguna versión previa debéis lanzar el siguiente comando.

```console
$ sudo apt-get remove docker docker-engine docker.io containerd runc
```
Se conserva el contenido de /var/lib/docker/, incluidas imágenes, contenedores, volúmenes y redes. Si no necesitas guardar los datos existentes y deseas comenzar con una instalación limpia deberás realizar estos pasos.

1 - Desinstalar los paquetes Docker Engine, CLI y Containerd:

 ```console
$ sudo apt-get purge docker-ce docker-ce-cli containerd.io
```
 
2 - Eliminar todas las imágenes, contenedores y volúmenes:

 ```console
$ sudo rm -rf /var/lib/docker
$ sudo rm -rf /var/lib/containerd
```

Debes eliminar los archivos de configuración editados manualmente.

Ahora pasemos a la instalación. Antes de instalar Docker Engine necesitamos configurar el repositorio de Docker.

Lo primero, actualizaremos el índice de paquetes apt e instalaremos algunos paquetes para permitir a apt usar el repositorio  sobre HTTPS

 ```console
$ sudo apt-get update
$ sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release
```

<img src="https://javi-rod.github.io/assets/images/20211022/pic1.png" alt="alt text" />
<img src="https://javi-rod.github.io/assets/images/20211022/pic2.png" alt="alt text" />

Ahora, debemos añadir la clave oficial CPG de Docker

 ```console
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

<img src="https://javi-rod.github.io/assets/images/20211022/pic3.png" alt="alt text" />

Por último, configuraremos el repositorio estable.

 ```console
$ echo   "deb [arch=amd64] https://download.docker.com/linux/ubuntu   $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Nota: arch hace referencia a la arquitectura, por lo que el comando anterior variará en función de nuestro equipo, los valores posibles son

- x86_64 / amd64 -> arch=amd64
- armhf -> arch=armhf
- arm64 -> arm64 
- S390x -> arch=s390x

Si queremos comprobar que el comando anterior ha funcionado, podemos listar el directorio /etc/apt/sources.list.d/ y ver si se ha creado docker.list 

 ```console
$ ls -al /etc/apt/sources.list.d/
```

<img src="https://javi-rod.github.io/assets/images/20211022/pic4.png" alt="alt text" />

Ahora que ya tenemos el repositorio configurado, procederemos a instalar Docker Engine

Primero actualizamos el índice de paquetes  y después instalaremos la última versión de Docker Engine and containerd

 ```console
$ sudo apt-get update
```

Si cuando ejecutamos el apt-get update recibimos un error como este "Error de GPG: https://download.docker.com/linux/ubuntu focal InRelease: Las firmas siguientes no se pudieron verificar porque su clave pública no está disponible"

<img src="https://javi-rod.github.io/assets/images/20211022/pic5.png" alt="alt text" />

Debemos lanzar el comando siguiente

 ```console
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 7EA0A9C3F273FCD8
```

<img src="https://javi-rod.github.io/assets/images/20211022/pic6.png" alt="alt text" />

Después hacemos un apt-get update

<img src="https://javi-rod.github.io/assets/images/20211022/pic7.png" alt="alt text" />

Y por último lanzamos el siguiente comando para instalar Docker

 ```console
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
```

<img src="https://javi-rod.github.io/assets/images/20211022/pic8.png" alt="alt text" />

Para comprobar que la instalación ha sido exitosa lanzaremos

```console
$ sudo docker run hello-world
```

<img src="https://javi-rod.github.io/assets/images/20211022/pic9.png" alt="alt text" />
