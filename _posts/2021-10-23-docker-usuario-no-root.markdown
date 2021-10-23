---
layout: post
title:  "Administrar Docker como un usuario no root"
date:   2021-10-23 09:30
categories: [Virtualización,Docker]
tags: Docker
---

En el post anterior instalamos Docker y para probar la correcta instalación lanzamos el comando sudo docker run hello-world

El motivo por el cual hicimos uso del comando sudo es el siguiente. El demonio de Docker hace uso de un [socket de Unix](https://es.wikipedia.org/wiki/Socket_Unix) y éste es propiedad del usuario root.

Para usar Docker con un usuario no root, podemos crear un grupo docker, en caso de no existir ya, y añadir nuestro usuario a este grupo.De esta forma cuando se inicie el demonio de Docker, el socket Unix que se crea es accesible por los usuarios de este grupo.

Debemos tener en cuenta el riesgo que conlleva la creación de este grupo, ya que tendrá privilegios equivalentes a root.

Se puede evitar lo anterior y ejecutar Docker sin privilegios de root en lo que se conoce como [Rootless mode](https://docs.docker.com/engine/security/rootless/). Para usar ese modo hay que cumplir unos requisitos previos y tiene una serie de limitaciones. En este post vamos a centrarnos en la ejecución de Docker con un usuario no root dejando el Rotlees mode para otro futuro post.

A continuación vamos a crer el grupo y agregar nuestro usuario:

```console
$ sudo groupadd docker
$ sudo usermod -aG docker $USER
```
Nota:
El parámetro -aG nos permite agegrar al grupo al usuario
$USER es un variable que contiene nuestro usuario. Haciendo un eco $USER veremos el valor de la variable.

Para comprobar si se hemos agregado al usuario al grupo

```console
$ grep docker /etc/group
```

<img src="https://javi-rod.github.io/assets/images/20211023/pic1.png" alt="alt text" />

Ahora lanzaremos el siguiente comando que nos permite iniciar sesión en un nuevo grupo

```console
$ newgrp docker 
```

Y por último, para comprobar que podemos ejecutar Docker sin un usuario root

```console
$ docker run hello-world 
```

<img src="https://javi-rod.github.io/assets/images/20211023/pic2.png" alt="alt text" />


El anterior comando lo que hace es descargar una imagen de prueba y la ejecuta en un contenedor.

En caso que recibamos el siguiente WARNING, nos indica que no tenemos permisos sobre el directorio de docker. Esto puede suceder si anteriormente habímos lanzado docker como sudo.

```console
WARNING: Error loading config file: /home/user/.docker/config.json -
stat /home/user/.docker/config.json: permission denied
```

Tenemos dos formas de solucionarlo, elimiar el directorio ~/.docker (se volverá a crear de forma automática pero perderemos la configuración personalizada) o bien cambiando el propietario y los permisos.

Para hacer lo segundo, ejecutaremos

```console
 $ sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
 $ sudo chmod g+rwx "$HOME/.docker" -R
```
