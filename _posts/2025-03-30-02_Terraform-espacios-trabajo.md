---
layout: post
title: Terraform - Espacios de trabajo (workspaces)
date: 2025-03-29 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code)]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

Los **workspaces** son **entornos aislados** dentro de un **mismo directorio de trabajo**. En lugar de tener que crear múltiples directorios para diferentes entornos, como desarrollo, pruebas y producción, podemos usar workspaces para mantener todo dentro de un solo directorio. Esto facilita la gestión de diferentes configuraciones y estados de recursos.

Terraform crea un workspace por defecto llamado **"default"**. Sin embargo, podemos crear tantos workspaces como necesitemos, cada uno con su propio archivo de estado, lo que permite tener configuraciones separadas para cada entorno.

## ¿Por qué usar Workspaces en Terraform?

- **Aislamiento de entornos**: Los workspaces permiten gestionar múltiples entornos sin la necesidad de tener configuraciones separadas o directorios distintos. Cada workspace tiene su propio estado, lo que significa que los cambios en uno no afectan a los demás.

- **Simplicidad en la gestión**: Usar workspaces facilita la organización del código y la administración de los recursos en entornos de prueba, desarrollo y producción, sin tener que duplicar configuraciones.


## Comandos Básicos para Gestionar Workspaces

1. **Ver los workspaces existentes**: Para ver los workspaces que tienes, utiliza el siguiente comando:

    `terraform workspace list`

2. **Cambiar a un workspace específico**: Para cambiar a un workspace, usa:

    `terraform workspace select <nombre-del-workspace>`

3. **Crear un nuevo workspace**: Si necesitas crear un nuevo workspace, puedes usar:

    `terraform workspace new <nombre-del-workspace>`

4. **Eliminar un workspace**: Para eliminar un workspace, si ya no lo necesitas, usa:

    `terraform workspace delete <nombre-del-workspace>`

## Ejemplo de Uso de Workspaces

Crear 3 nuevos workspaces; *us-payroll*, *uk-payroll* e *india-payroll*.

Para crear los workspaces ejecutaremos el comando `terraform  workspace new <nombre del workspace>`

Este comando creará un nuevo workspace. Si el comando es exitoso, Terraform cambiará automáticamente al nuevo workspace y mostrará un mensaje similar al que vemos en la imagen.

<img src="https://javi-rod.github.io/assets/images/20250330/01_terraform_workspace.png" alt="Creación de workspaces" />

Después listamos el contenido del directorio de trabajo. Veremos la creación de dos directorios. El directorio `.terraform` que contiene los archivos relacionados con la configuración de Terraform y el directorio `terraform.tfstate.d`donde Terraform guarda el estado de los diferentes workspaces. 

<img src="https://javi-rod.github.io/assets/images/20250330/02_listar_directorio.png" alt="Listado directorio de trabajo para ver que se directorios se han creado" />

Si ahora listamos el contenido de `terraform.tfstate.d` veremos que se ha creado un subdirectorio para cada uno de los workspaces que hemos creado.

<img src="https://javi-rod.github.io/assets/images/20250330/03_listar_directorio.png" alt="Listado del directorio .terraform.tfstate.d" />

Para terminar vamos a ver, un ejemplo de cómo cambiarnos a un uno de los workspaces creados. En este  caso `us-payroll`

<img src="https://javi-rod.github.io/assets/images/20250330/04_cambiar_workspace.png" alt="Ejemplo de fichero .tf" />

## ¿Qué debemos esperar en cada paso?

1- **Creación de workspaces**: Cada vez que creamos un **workspace**, Terraform debería proporcionar un mensaje indicando que se ha creado correctamente y que has cambiado al workspace recién creado.

2- **Listado de workspaces**: El comando `terraform workspace list` mostrará todos los workspaces existentes. El workspace actual estará marcado con un asterisco (*).

3- **Verificación de directorios**: Cuando listamos el directorio `terraform.tfstate.d`, podemos confirmar que se han creado subdirectorios para cada uno de los workspaces. Esto es importante para asegurarse de que los estados están correctamente aislados.

4- **Cambio de workspace**: Usar el comando `terraform workspace select <nombre-del-workspace>` cambiará el contexto al workspace seleccionado, y siempre deberíamos ver un mensaje que confirme el cambio.

## Limitaciones de los Workspaces

Aunque los workspaces son útiles, también tienen limitaciones. Por ejemplo:

- **No separan las configuraciones de código**: Los workspaces no permiten tener diferentes configuraciones de código para cada entorno; sólo separan el estado.

- **Uso limitado de variables y módulos**: Si bien podemos usar diferentes valores de variables en función del workspace, el código de los módulos será el mismo.