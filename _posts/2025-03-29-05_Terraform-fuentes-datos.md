---
layout: post
title: Terraform - Fuentes de datos (Data sources)
date: 2025-03-29 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code)]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

Las **fuentes de datos** o **Data Sources** permiten obtener información de recursos que ya **existen fuera de la gestión de Terraform** o en otros recursos gestionados por Terraform. A diferencia de los recursos, que se crean, actualizan y destruyen, los Data Sources **solo permiten obtener datos**, no los modifican.

## ¿Por qué usar Data Sources?

Los **Data Sources** son útiles cuando necesitamos obtener información de recursos existentes, como un **ID** de una AMI en AWS, una **subnet** o un **bucket** de S3 que ya existe, o incluso para obtener información de recursos creados por otros equipos o en diferentes configuraciones de Terraform.

Por ejemplo, podemos usar un Data Source para obtener el ID de una imagen de máquina (AMI) en AWS que se haya actualizado automáticamente, o para referenciar un subnet específico de VPC en tu infraestructura.

## Sintaxis Básica de un Data Source

Un Data Source se define con el bloque `data`, y su sintaxis es similar a los recursos, pero solo proporciona datos y **no realiza cambios en la infraestructura**.

```hcl
data "provider_name_resource_type" "name" {
  # Atributos para filtrar o configurar
}
```

### Ejemplo:

Obtener el **ID** de una AMI en AWS haciendo uso de un Data Source de tipo `aws_ami` para buscar una AMI existente:

```
data "aws_ami" "latest_amazon_linux" {
  most_recent = true
  owners      = ["amazon"]
  filters = {
    name = "amzn2-ami-hvm-*-x86_64-gp2"
  }
}

output "ami_id" {
  value = data.aws_ami.latest_amazon_linux.id
}
```

## Ejemplos de uso de Data Sources

### 1. Obtener la última AMI disponible

En este ejemplo, obtenemos la última imagen de Amazon Linux 2 desde AWS, utilizando un Data Source para encontrar la AMI que coincida con los filtros definidos:

```
data "aws_ami" "latest_amazon_linux" {
  most_recent = true
  owners      = ["amazon"]
  filters = {
    name = "amzn2-ami-hvm-*-x86_64-gp2"
  }
}

resource "aws_instance" "example" {
  ami           = data.aws_ami.latest_amazon_linux.id  # Utilizamos la AMI obtenida del Data Source
  instance_type = "t2.micro"
}
```

En este caso, `data.aws_ami.latest_amazon_linux.id` nos da el **ID** de la última AMI que cumple con los filtros.

### 2. Obtener una Subnet o VPC existente

A menudo, necesitaremos referenciar una **VPC** o una **Subnet** que ya exista. Los Data Sources permiten obtener estas referencias sin tener que gestionarlas directamente en Terraform.

```
data "aws_vpc" "default" {
  default = true
}

data "aws_subnet" "default" {
  vpc_id = data.aws_vpc.default.id
}

resource "aws_instance" "example" {
  ami           = data.aws_ami.latest_amazon_linux.id
  instance_type = "t2.micro"
  subnet_id     = data.aws_subnet.default.id  # Referenciamos la Subnet existente
}
```

En este caso, no creamos una **VPC** ni una **Subnet**, sino que usamos los Data Sources `aws_vpc` y `aws_subnet` para obtener datos sobre la VPC predeterminada y la Subnet predeterminada existente en nuestra cuenta.

### 3. Obtener un Recurso Existente (ejemplo con un Bucket de S3)

Si tenemos un bucket de S3 creado y queremos usarlo en nuestra infraestructura, podemos obtener su información mediante un Data Source.

```hcl
data "aws_s3_bucket" "example" {
  bucket = "my-existing-bucket"  # Nombre del bucket que ya existe
}

output "bucket_id" {
  value = data.aws_s3_bucket.example.id
}

```
Este Data Source simplemente obtiene la información del bucket de S3 sin cambiar nada en el bucket mismo. De esta forma accedemos a los atributos del bucket, como su **ID**, a través de `data.aws_s3_bucket.example.id`.

## ¿Cuándo usar Data Sources?

- **Obtener datos de recursos existentes**: Si los recursos ya existen y necesitamos que Terraform los gestione, podemos usar un Data Source para obtener información de esos recursos.

- **Reutilizar recursos en diferentes partes de la configuración**: Si tenemos un recurso creado en otro lugar (por ejemplo, una AMI que se actualiza automáticamente), podemos usar un Data Source para reutilizar esa información sin necesidad de crear recursos adicionales.

- **Evitar duplicación de infraestructura**: Si tenemos recursos creados manualmente o por otros equipos, podemos usar Data Sources para acceder a ellos sin recrearlos.


## Principales Data Sources comunes

- **AWS**:

    - `aws_ami`: Obtiene información sobre las imágenes de Amazon EC2.
        
    - `aws_vpc`: Obtiene información sobre una VPC existente.
        
    - `aws_subnet`: Obtiene información sobre una subnet dentro de una VPC.
        
    - `aws_security_group`: Obtiene información sobre un grupo de seguridad.
        
    - `aws_s3_bucket`: Obtiene información sobre un bucket de S3.

- **Google Cloud**:

	- `google_compute_image`: Obtiene información sobre imágenes de máquinas en Google Cloud.

	- `google_compute_network`: Obtiene información sobre una red de Google Cloud.

- **Azure**:

   - `azurerm_virtual_network`: Obtiene información sobre una red virtual en Azure.