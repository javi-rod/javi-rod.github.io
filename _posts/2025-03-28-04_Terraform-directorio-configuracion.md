---
layout: post
title: Terraform - Directorio de configuración
date: 2025-03-28 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code),devops]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

# ¿Qué es el directorio de configuración en Terraform?

El directorio de configuración en Terraform es la carpeta que contiene todos los archivos necesarios para definir, gestionar y ejecutar la infraestructura en nuestro proyecto. Este directorio es esencial porque alberga los archivos de configuración `.tf`, y el archivo `terraform.tfstate`, que guarda el estado actual de nuestra infraestructura, asegurando que Terraform pueda realizar un seguimiento de los recursos gestionados.

## Estructura típica de un directorio de configuración

La estructura de un directorio de configuración de Terraform puede variar según las necesidades del proyecto. Generalmente sigue una organización estándar como la siguiente:

```
my-terraform-project/
├── main.tf            # Archivos principales de configuración
├── variables.tf       # Definición de variables
├── outputs.tf         # Definición de salidas
├── terraform.tfvars    # Archivos de valores de variables
└── terraform.tfstate   # Estado actual de la infraestructura
```
### Archivos comunes en un directorio de configuración:

1. `main.tf`: Este archivo contiene la configuración principal de los recursos de infraestructura que vamos a  gestionar, como instancias de máquinas virtuales, redes, bases de datos, etc.

2. `variables.tf`: Aquí es donde se definen las variables que usaremos en el archivo `main.tf` y otros archivos. Las variables permiten que la infraestructura sea más flexible y reutilizable, evitando hardcoding y mejorando la mantenibilidad.

3. `terraform.tfvars`: Este archivo contiene los valores que se asignan a las variables definidas en `variables.tf`. Es útil para gestionar configuraciones específicas para diferentes entornos o proyectos.

4. `outputs.tf`: Aquí definimos los outputs (resultados) que Terraform mostrará al final de la ejecución. Por ejemplo, direcciones IP de las máquinas creadas o URLs de los servicios provisionados.

5. `terraform.tfstate`: Este archivo es gestionado automáticamente por Terraform y guarda el estado actual de la infraestructura. Se actualiza cada vez que se aplica un cambio en la infraestructura. **Es crucial no modificar este archivo manualmente**, ya que contiene información crítica sobre los recursos gestionados..

## Buenas prácticas en la organización de archivos de configuración

1. **Separar la infraestructura por módulos**: En lugar de tener un solo archivo grande, es una buena práctica dividir la configuración en módulos. Los **módulos** son bloques de código reutilizables que pueden gestionarse por separado, facilitando la modularidad y la escalabilidad.

2. **Mantener un control de versiones adecuado**: Al utilizar un sistema de control de versiones (como Git), debemos de asegurarnos de agregar el archivo `terraform.tfstate` a nuestro `.gitignore`,ya que contiene información sensible sobre nuestra infraestructura.

3. **Utilizar backends remotos para el estado**: En lugar de almacenar el estado localmente en `terraform.tfstate`, podemos configurar backends remotos (como S3, Azure Blob Storage, etc.) para almacenar el estado de forma centralizada y permitir que varias personas trabajen en el mismo proyecto.