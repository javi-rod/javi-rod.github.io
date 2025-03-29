---
layout: post
title: Terraform - Módulos
date: 2025-03-29 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code)]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

## ¿Qué es un Módulo de Terraform?

Un **módulo** de Terraform es simplemente un **conjunto de archivos de configuración** que se pueden **reutilizar**. Un módulo puede contener recursos de Terraform, variables, salidas y otros componentes, y se puede utilizar en diferentes partes del proyecto o incluso en diferentes proyectos.

Terraform tiene **dos tipos de módulos**:

1. **Módulos raíz**: Son los **módulos principales** que se crean dentro de un directorio de trabajo.

2. **Módulos reutilizables**: Son módulos que podemos importar desde otros directorios, proyectos o incluso de Terraform Registry (un repositorio público de módulos).


## ¿Por qué usar Módulos?

- **Reutilización de código**: Permiten reutilizar el mismo conjunto de configuraciones en diferentes partes de nuestra infraestructura.

- **Organización**: Ayudan a organizar proyectos grandes dividiéndolos en componentes más pequeños y fáciles de gestionar.

- **Escalabilidad**: Facilitan la escalabilidad de nuestra infraestructura a medida que crece.


## Ejemplo: Creando un Módulo Simple para AWS EC2

Supongamos que necesitamos crear varias instancias EC2 en AWS. En lugar de escribir el mismo código una y otra vez, vamos a crear un **módulo reutilizable**, siguiendo estos pasos:

1. **Crear un directorio** para el módulo: Primero, creamos un directorio llamado `modules/ec2_instance`.

2. **Escribir el módulo** de EC2 en el archivo `main.tf` dentro del directorio `modules/ec2_instance`:

```hcl
# modules/ec2_instance/main.tf
resource "aws_instance" "example" {
  ami           = var.ami_id
  instance_type = var.instance_type
}
```

3. Definir las **variables** en `variables.tf`:

```hcl
# modules/ec2_instance/variables.tf
variable "ami_id" {
  description = "AMI ID para la instancia EC2"
  type        = string
}

variable "instance_type" {
  description = "Tipo de instancia EC2"
  type        = string
}
```

4. Definir las salidas en `outputs.tf` (opcional):

```hcl
# modules/ec2_instance/outputs.tf
output "instance_id" {
  value = aws_instance.example.id
}
```

## Ejemplo: Usando el Módulo de EC2 anterior

Ahora, en el archivo principal (main.tf), podemos llamar al módulo de la siguiente manera:

```hcl
# main.tf
module "mi_ec2_instance" {
  source        = "./modules/ec2_instance"
  ami_id        = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}
```

El bloque `module` hace referencia al módulo creado en el directorio `modules/ec2_instance`. Mientras que `source` indica la ubicación del módulo, y las variables `ami_id` e `instance_type` se pasan al módulo para configurar la instancia EC2.

## Usando un Módulo desde Terraform Registry

**Terraform Registry** es un repositorio público donde encontraremos módulos creados por la comunidad. Por ejemplo, si queremos crear un VPC en AWS, podemos usar un módulo ya existente.

```hcl
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"
  name   = "mi_vpc"
  cidr   = "10.0.0.0/16"
}
```
En este caso, el módulo `terraform-aws-modules/vpc/aws` es un módulo reutilizable disponible en **Terraform Registry**, que facilita la creación de una VPC con configuraciones predeterminadas.

## Módulos de Terraform: Detalles Adicionales

1. **Estructura de un módulo**: Un módulo generalmente sigue una estructura similar a la siguiente:

```
Copiar
├── main.tf       # Configuración principal del módulo
├── variables.tf  # Declaración de variables del módulo
├── outputs.tf    # Definición de salidas del módulo
└── README.md     # Documentación sobre el uso del módulo (opcional, pero recomendado)
```

Esta estructura puede variar dependiendo de las necesidades del proyecto, pero la idea básica es que cada módulo debe tener su propia carpeta, con archivos que separen las distintas configuraciones, lo que mejora la organización y la reutilización.

2. **Versionado de módulos**: Cuando usamos módulos desde el **Terraform Registry**, podemos especificar una versión concreta del módulo para asegurar la estabilidad de la infraestructura a lo largo del tiempo. Esto se logra con el parámetro `version`, por ejemplo:

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "3.0.0"
  name    = "mi_vpc"
  cidr    = "10.0.0.0/16"
}
```

3. **Módulos en diferentes proyectos**: Terraform permite crear módulos que podemos compartir entre diferentes proyectos. Si tenemos un repositorio de módulos centralizado, podemos hacer que nuestros módulos sean reutilizables no solo dentro de un mismo proyecto, sino a través de varios proyectos en diferentes repositorios. Esto fomenta la reutilización y mantiene la consistencia en la infraestructura.

4. **Módulos y dependencias**: A veces un módulo puede depender de otro. Por ejemplo, si tenemos un módulo que crea una VPC y otro que crea instancias EC2 dentro de esa VPC, es importante definir dependencias explícitas entre estos módulos para que Terraform los ejecute en el orden correcto. Para  hacer esto usaremos el parámetro `depends_on` en el módulo que necesita esperar a que otro se ejecute:

```hcl
module "mi_ec2_instance" {
  source        = "./modules/ec2_instance"
  ami_id        = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  depends_on    = [module.vpc]
}
```

5. **Mejorando la documentación**: Aunque los módulos deben ser fáciles de identificar, también es muy útil incluir una buena documentación dentro del módulo. El archivo `README.md` puede describir el propósito del módulo, cómo usarlo, ejemplos de configuración y cómo contribuir, entre otros detalles.

## Buenas Prácticas con Módulos

- **Nombrar bien los módulos**: Usar nombres claros para los módulos, de manera que sea fácil identificar su propósito (por ejemplo, ec2_instance, vpc, security_group).

- **Evitar la duplicación**: Si tenemos configuraciones repetidas, es hora de crear un módulo.

- **Mantener la modularidad**: No sobrecargar un módulo con demasiadas configuraciones. Cada módulo debe tener una responsabilidad clara y única.