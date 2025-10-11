---
layout: post
title: Terraform - HCL
date: 2025-03-28 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code),devops]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

HCL (HashiCorp Configuration Language) es un lenguaje de configuración desarrollado por HashiCorp, utilizado principalmente en herramientas como Terraform. A diferencia de otros lenguajes como JSON o YAML, HCL fue creado para ser más legible y fácil de escribir, sin perder la capacidad de definir configuraciones complejas. Los archivos HCL tienen extensión `.tf`

Aquí tenemos un ejemplo básico de un fichero `.tf`

```hcl
provider "aws" {
  region = "us-west-2"
}

resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  tags = {
    Name = "example-instance"
  }
}
```

En el ejemplo, tenemos dos bloques, uno llamado provider y otro resource. En el primer bloque, definimos el proveedor AWS con sus respectivos argumentos, como la región. En el segundo bloque, definimos un tipo de recurso del proveedor AWS, que en este caso es una instancia de EC2. En el segundo bloque, además, especificamos que el nombre del recurso es example.

<img src="https://javi-rod.github.io/assets/images/20250328/01_estructura_ejemplo_tf.png" alt="Ejemplo de fichero .tf" />

Un flujo de trabajo simple de Terraform, tras escribir el fichero de configuración, consta de estos pasos:

1. `terraform init` - Verificar archivo de configuración e inicializar el directorio de trabajo que contiene el archivo .tf. Además de descargar los plugins necesarios.

2. `terraform plan` - Ver el plan de ejecución que llevará a cabo Terraform. Permite revisar los cambios antes de aplicarlos.

3. `terraform apply` - Crear, actualizar o eliminar los recursos según el plan generado en el paso anterior.

En resumen, HCL es una herramienta poderosa para describir nuestra infraestructura en Terraform. Al aprender a escribir y comprender archivos .tf, podemos gestionar y automatizar recursos de forma eficiente en diferentes proveedores de nube. ¡No olvides revisar los otros posts de esta serie para profundizar más en cada tema!