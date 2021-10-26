---
layout: post
title:  "Dockerfile"
date:   2021-10-24 09:30
share: true
categories: [Virtualización,Docker]
tags: Docker
---

Un Dockerfile no es más que un fichero de texto. Este fichero contiene una secuencia de comandos que nos permitirán crear una imagen de Docker que posteriormente correrá en un contenedor.

Gráficamente lo podemos ver así

<img src="https://javi-rod.github.io/assets/images/20211024/docker-file.png" alt="alt text" width="600"/>

Usaremos docker build para construir una imagen a partir del docker file y un contexto. Ese contexto puede ser un PATH o una dirección URL. El PATH será local, un directorio de nuestra máquina mientras que la URL puede ser un repositorio en Git.

El proceso de construcción es recursivo. Esto quiere decir que al construir la imagen incluirá todos los subdirectorios del PATH o en el caso de la URL los repositorios y submodulos. Es recomendable comenzar en un directorio vacío y en éste meter el dockerfile y los ficheros necesarios.

El demonio de Docker es el encargado de realizar la compilación.

Desde la documentación oficial advierten de no usar el raíz / como PATH, ya que la compilación transferirá todo el contenido del disco duro al demonio de Docker.

El comando a ejecutar para construir la imagen de Docker en un directorio específico

```console
$ docker build -f /path/to/a/Dockerfile .
```

Si en lugar de usar un directorio,usamos un repositorio

 ```console
$ docker build -t shykes/myapp .
```

Podemos hacer una validación previa del dockerfile 

 ```console
$ docker build -t test/myapp 
```

El demonio de Docker ejecuta las instrucciones de forma secuencial línea a línea, confirmando el resultado de cada instrucción en una nueva imagen si fuera necesario, antes de generar el ID para la nueva imagen. También se encargará de limpiar automáticamente el contexto enviado. 

Cuando es posible, docker build hace uso de una buil-cache para acelerar el proceso de construcción de la imagen. Cuando esto suceda lo identificaremos en la salida de pantalla porque veremos CACHED. De forma predeterminada, la caché de compilación se basa en los resultados de compilaciones anteriores en la máquina en la que estamos compilando.

Para profundizar más sobre la sintaxis y las instrucciones, [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)
