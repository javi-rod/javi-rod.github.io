---
layout: post
title:  "¿Qué es Docker?"
date:   2021-10-18 09:30
categories: [Virtualización,Docker]
tags: Docker
---
Docker, es una herramienta que nos permite mediante tecnología de contenedores, virtualizar aplicaciones de forma ligera y portable. 

Podemos imaginar que ese contenedor de Docker, es como un container de barco como los que vemos en la siguiente imagen

<img src="https://raw.githubusercontent.com/javi-rod/javi-rod.github.io/master/pics/2021-10-18-intro-docker/containers.jpg" alt="alt text" />

La aplicación, al estar empaquetada en un contenedor, incluye además del código, sus dependencias, evitando que en un máquina funcione y en otra no. Por ejemplo, supangamos que una aplicación requiere de JAVA 7 pero yo tengo JAVA 6. A mi no me funcionará la aplicación al no tener la JAVA adecuada, esto se evita teniendo la aplicación empaquetada en un contenedor ya que nos permitirá ejecutar la aplicación siempre en el mismo entorno.

Aunque el concepto de contenedores es similar a la virtualización de máquinas tiene sus diferencias.

<img src="https://raw.githubusercontent.com/javi-rod/javi-rod.github.io/master/pics/2021-10-18-intro-docker/Docker_VM.png" alt="alt text" />

Las máquinas virtuales necesitan de su propio sistema operativo como si se tratase de un equipo físico, además de un hardware virtual (RAM,Disco...) Este hardware es compartido por todas las máquinas virtuales que tengamos en nuestro Host.

En Docker, en lugar de tener ese hardware subyacente, solo se virtualiza el sistema operativo.Cada contenedor comparte el Kernel del sistema operativo del host además de los binarios y las bibliotecas.
 
Debemos tener en cuenta, que al compartir el kernel con el host, sólo podemos usar un contenedor de Linux en un host Linux y un contenedor Windows en un host Windows.Aunque aquí podemos hacer un pequeño matiz, se puede hacer uso de Docker Desktop en Windows para para tener un contenedor Linux en Windows. 

Docker Desktop, es una aplicación que puede ser usada en entornos Mac o Windows,y nos permite crear y compartir aplicaciones y microservicios en contenedores, tal como viene recogido en la documentación oficial.
