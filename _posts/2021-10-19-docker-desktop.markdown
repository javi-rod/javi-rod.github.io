---
layout: post
title:  "Docker Desktop"
date:   2021-10-19 09:30
share: true
categories: [Virtualización,Docker]
tags: Docker
---

En el post anterior [¿Qué es docker?](https://javi-rod.github.io/2021/10/intro-docker/) mencionaba ligeramente Docker Desktop y hoy desearía profundizar algo más en este software que está disponible tanto para Mac como para Windows.

Si recordáis, comentaba que en un contenedor de Linux debía correr en un host Linux pero con Docker Desktop, es posible correr un contenedor Linux en Windows.

Esto es gracias a las características de Hyper-V  o al uso del WSL 2. En el primer caso se crea una máquina virtual y en el segundo se usa un susbsitema de Linux que correrá por debajo de Windows.

Los requesistos que necesitamos,según la documentación oficial, son:

Hyper-V

: - Windows 11 Pro o superior de 64 bits.
: - Windows 10 Pro o superior de 64 bits.Compilación a partir de la 19041.
: - Características de Hyper-V y contenedores de Windows habilitadas.

Además debemos de cumplir los siguientes requerimientos de hardware

- Procesador de 64 bits con traducción de direcciones de segundo nivel (SLAT).
- RAM del sistema de 4GB.
- El soporte de virtualización de hardware a nivel de BIOS debe estar habilitado.

Todas las cuentas de Windows van a usar la misma máquina virutal, por lo que los contenedores y las imágenes se compartirán entre estas.

WSL2

: - Windows 11 Pro o supererior de 64 bits.
: - Windows 10 Pro o superior de 64 bits.Compilación a partir de la 19041.
: - Habilitar la función WSL2.

En cuanto al hardware

- Procesador de 64 bits con traducción de direcciones de segundo nivel (SLAT).
- RAM del sistema de 4GB.
- El soporte de virtualización de hardware a nivel de BIOS debe estar habilitado.

Según la documentación de Docker, debemos descargar e instalar el paquete de actualización del kernel de Linux.

A diferencia de usar Hyper-V,no es posible compartir contenedores e imágenes entre cuentas de usuario cuando estamos haciendo uso de WSL 2.

Si os intersa el tema y queréis profundizar más podeís ir a <https://docs.docker.com/desktop/>
