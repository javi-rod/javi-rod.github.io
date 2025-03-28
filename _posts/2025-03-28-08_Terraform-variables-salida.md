---
layout: post
title: Terraform - Variables de salida
date: 2025-03-28 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code)]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

### Variables de Salida (Output Variables) en Terraform

Las variables de salida en Terraform permiten compartir información entre recursos, módulos y con sistemas externos, como playbooks de Ansible o scripts. Son especialmente útiles para mostrar valores importantes, como IDs de recursos, direcciones IP públicas, URLs de servicios, entre otros, al finalizar la ejecución de Terraform.

Por ejemplo, al crear una instancia EC2 en AWS, podemos utilizar una variable de salida para obtener la dirección IP pública de la máquina creada y utilizarla en otro lugar o mostrarla como parte del resumen de la ejecución.

### ¿Cómo se definen las Variables de Salida?

Las variables de salida se definen mediante el bloque `output` en los archivos de configuración de Terraform. Aquí tienes un ejemplo sencillo:

```hcl
output "instance_public_ip" {
  value = aws_instance.my_instance.public_ip
  description = "La dirección IP pública de la instancia EC2"
}
```

En este caso, estamos exponiendo la dirección IP pública de una instancia EC2 (`aws_instance.my_instance.public_ip`) como una variable de salida llamada `instance_public_ip`. La opción `description`, no es obligatoria, pero es muy útil para proporcionar contexto sobre lo que hace cada variable de salida.

### Tipos de Valores de Salida

Las variables de salida pueden contener distintos tipos de valores. Algunos ejemplos comunes son:

- **Strings**: Direcciones IP, URLs, nombres, etc.

- **Listas**: Un conjunto de valores, como direcciones IP públicas de varias instancias.

- **Mapas**: Pares clave-valor, útiles para representar configuraciones complejas.

- **Referencias a Recursos**: Puedes exponer atributos de recursos, como IDs, nombres, etc.

**Ejemplo de una salida con lista**:

```hcl
output "instance_ips" {
  value = aws_instance.my_instance[*].public_ip
  description = "Lista de direcciones IP públicas de todas las instancias"
}
```

Este bloque crea una variable de salida llamada `instance_ips`, que contiene una lista de las direcciones IP públicas de todas las instancias `aws_instance.my_instance`. Podemos acceder a esta lista de IPs para usarlas en otras partes del código o simplemente verlas al ejecutar Terraform.

### ¿Cómo se Accede a las Variables de Salida?

Una vez que se ejecuta `terraform apply`, Terraform mostrará las variables de salida en la consola. También podemos acceder a ellas utilizando el comando `terraform output`:

```bash
terraform output instance_public_ip
```

Esto devolverá el valor de la dirección IP pública de la instancia EC2 que hemos definido anteriormente.

Si tenemos múltiples variables de salida definidas, podemos ver todas las variables de salida con:

```bash
terraform output
```

### Uso de Variables de Salida entre Módulos

Las variables de salida también son muy útiles para trabajar con módulos en Terraform. Podemos pasar información entre módulos utilizando estas variables de salida.

Por ejemplo, si tenemos un módulo que crea una red y otro módulo que crea instancias EC2, podemos exponer la ID de la red desde el módulo de red como una variable de salida y usarla en el módulo de instancias EC2.

**Ejemplo de módulo de red (module "network")**:

```hcl
# module_network/main.tf
resource "aws_vpc" "main_vpc" {
  cidr_block = "10.0.0.0/16"
}

output "vpc_id" {
  value = aws_vpc.main_vpc.id
  description = "El ID del VPC creado"
}
```

**Ejemplo de módulo de instancias (module "instances")**:

```hcl
# module_instances/main.tf
resource "aws_instance" "web_server" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
  vpc_id        = module.network.vpc_id
}

output "instance_public_ip" {
  value = aws_instance.web_server.public_ip
  description = "Dirección IP pública de la instancia"
}
```

En este ejemplo:

1. El módulo de red (`module_network`) expone la ID del VPC como una variable de salida (`vpc_id`).

2. El módulo de instancias (`module_instances`) utiliza esta salida como entrada para crear una instancia dentro de ese VPC.

### ¿Por qué son Importantes las Variables de Salida?

1. **Interoperabilidad**: Permiten que diferentes partes de tu infraestructura se comuniquen sin intervención manual.

2. **Automatización**: Facilitan la automatización de flujos de trabajo reutilizando información relevante sin necesidad de acceder a los recursos directamente.

3. **Visibilidad**: Proporcionan una forma clara y directa de obtener información útil tras la ejecución de Terraform, como direcciones IP, IDs de recursos, nombres, etc.

4. **Configuración más clara**: Ayudan a documentar la infraestructura de forma implícita, ya que las salidas describen lo que se está creando o gestionando.