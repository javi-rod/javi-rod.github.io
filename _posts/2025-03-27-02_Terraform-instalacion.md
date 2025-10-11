---
layout: post
title: Terraform - Instalación
date: 2025-03-27 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code),devops]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

Para instalar Terraform, vamos a la web oficial (https://developer.hashicorp.com/terraform/install), donde podemos ver que hay opciones para macOS, Windows, Linux... y diferentes versiones de CPU.

En nuestro caso, nos centraremos en Linux. Como se puede apreciar, podemos optar por usar un gestor de paquetes o descargar el binario.

Al seleccionar nuestra distribución, veremos los comandos que debemos ejecutar:

<img src="https://javi-rod.github.io/assets/images/20250327/01_descarga_terraform.png" alt="Formas de Instalar Terraform, vía Gestor Paquetes o descarga de binario" width="600" />

A continuación, veremos dos ejemplos de instalación: uno usando el gestor de paquetes (apt) y otro con el binario.

## Ejemplo instalación en Ubuntu 24.04 vía apt

Para hacer la instalación en Ubuntu 24.04, seguiremos los pasos del apartado **Package Manager**.

1 - Primero, descargaremos la clave GPG para verificar la autenticidad de los paquetes que provienen del repositorio de HashiCorp. Guardaremos esta clave en el archivo */usr/share/keyrings/hashicorp-archive-keyring.gpg*.

<img src="https://javi-rod.github.io/assets/images/20250327/02_descargar_clave.png" alt="Descargar clave CPG" width="600" />

2 - Después vamos añadir la configuración del repositorio de HashiCorp a nuestro sistema. Escribiremos la línea correspondiente en el archivo */etc/apt/sources.list.d/hashicorp.list*. Esto permitirá que nuestro sistema obtenga los paquetes del repositorio de HashiCorp y los verifique utilizando la clave GPG que descargamos previamente.

<img src="https://javi-rod.github.io/assets/images/20250327/03_anadir_repo.png" alt="Falla porque no tenemos lsb-release instalado" width="600" />

Como vemos, en este caso, no reconoce el comando *lsb_release* porque no tenemos el paquete lsb-release instalado. Por lo tanto, debemos eliminar el repositorio de HashiCorp de la lista de fuentes para evitar errores.

<img src="https://javi-rod.github.io/assets/images/20250327/04_borrar_repo_de_lista.png" alt="Eliminar repo del listado de repositorios" width="600" />

3 - A continuación, actualizamos el sistema instalamos el paquete *lsb-release*

<img src="https://javi-rod.github.io/assets/images/20250327/05_update_packages.png" alt="Actualizar paquetes e instalar paquete lsb-release" width="600" />

4 - Con el paquete *lsb-release* instalado, podemos añadir nuevamente el repositorio de hashicorp

<img src="https://javi-rod.github.io/assets/images/20250327/06_anadir_repo.png" alt="Añadir repo al listado de repositorios" width="600" />

5 - Luego actualizaremos los paquetes e instalaremos terraform. Una vez terminado, procedemos a verificar que se ha instalado con `terraform version`

<img src="https://javi-rod.github.io/assets/images/20250327/07_actualizar_instalar.png" alt="Actualizar los paquetes del repositorio e instalar Terraform" width="600" />

> 📌 Nota
> Ten en cuenta que si no eres el usuario root, deberás usar sudo antes de cada comando que requiera privilegios de administrador. En mi caso se podía omitir, al usar el usuario root.

Estos son los comandos que se han ejecutado por orden:

```bash
wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

apt install lsb-release

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update && sudo apt install terraform

terraform version
```

## Ejemplo instalación Ubuntu 24.04 vía binario

La otra forma de instalar Terraform es usando el binario.

1 - El primer paso es descargar el binario comprimido. Es importante tener en cuenta la arquitectura de nuestra CPU (en este caso, amd64). Si no estás seguro de la arquitectura, puedes usar el comando `lscpu` para comprobarla.

<img src="https://javi-rod.github.io/assets/images/20250327/08_descargar_binario.png" alt="Descargar binario" width="600" />

2 - Una vez descargado, vamos a descomprimir el fichero. En este caso no tenía `unzip`, así que he actualizado los paquetes y después lo he instalado.

<img src="https://javi-rod.github.io/assets/images/20250327/09_actualizar_instalar_unzip.png" alt="Actualizar los paquetes del repositorio e instalar unzip" width="600" />

3 - Después de descomprimir el archivo moveremos el binario de Terraform a */usr/local/bin* para poder ejecutarlo desde cualquier lugar. Para comprobar que todo está correcto ejecutaremos un `terraform version`.Deberíamos ver la versión de Terraform instalada. 

<img src="https://javi-rod.github.io/assets/images/20250327/10_unzip_mover_bin.png" alt="Descomprimir archivo, mover binario y ver version de Terraform" width="600" />

En este caso la salida nos devuelve la versión de Terraform 1.10.5 y que está corriendo en un sistema Linux con una arquitectura de CPU de 64 bits.

Los comandos que se deben ejecutar en orden son:

```bash
wget https://releases.hashicorp.com/terraform/1.10.5/terraform_1.10.5_linux_amd64.zip

apt update ;  apt install terraform

unzip terraform_1.10.5_linux_amd64.zip

mv terraform /usr/local/bin

terraform version
```



