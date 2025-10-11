---
layout: post
title: Terraform - Infraestructura mutable e inmutable
date: 2025-03-29 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code),devops]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

En Terraform, el concepto de infraestructura **mutable** e **inmutable** se refiere a **cómo se manejan los cambios en la infraestructura** a lo largo del tiempo. 

## Infraestructura Mutable:

La infraestructura que se crea puede ser **modificada** directamente en el lugar (es decir, se "cambia" sobre la marcha). Si hacemos algún cambio, como actualizar una configuración o agregar un recurso, hacemos **modificaciones sobre la infraestructura existente** sin tener que destruirla o reemplazarla.

### Características:

- Cambios directos en los recursos sin necesidad de recrearlos.

- Los cambios son aplicados sobre la infraestructura en ejecución.

- Puede generar complejidad a medida que los cambios se acumulan, lo que a veces lleva a un "estado indeseado" o difícil de manejar.

### Ventajas:

- Menos tiempo de inactividad en algunos casos.

- Permite cambios incrementales sin destruir todo.

### Desventajas:

- La infraestructura puede volverse más difícil de mantener a medida que las modificaciones se acumulan.

- Riesgo de configuraciones no coherentes o difíciles de manejar a largo plazo.

### Ejemplo:

Tenemos una instancia EC2 en AWS y queremos cambiar el tipo de máquina. Con un enfoque mutable, haremos esa modificación directamente, ajustando la configuración sin reemplazar la instancia.

## Infraestructura Inmutable:

La infraestructura **nunca se modifica directamente**. En lugar de eso, **cualquier cambio implica crear una nueva versión de la infraestructura** y luego **reemplazar la antigua por la nueva**. La idea principal es que la infraestructura es reemplazada por completo, garantizando que el entorno siempre esté en un estado limpio y predecible.

### Características:

- Los recursos no se modifican directamente. Si se necesita cambiar algo, se crea una nueva versión del recurso.

- Puede implicar la destrucción de la infraestructura anterior y la creación de una nueva.

- Garantiza que la infraestructura sea siempre "fresca" y en un estado completamente definido.

### Ventajas:

- Facilita la gestión y mantenimiento de la infraestructura, ya que siempre tienes una versión limpia y coherente.

- Menos probabilidades de conflictos de configuración a largo plazo.

- Mejor control sobre el ciclo de vida de la infraestructura.

### Desventajas:

- Puede generar más tiempo de inactividad debido a la necesidad de destruir y crear recursos.

- Requiere mayor planificación para evitar interrupciones de servicio.

### Ejemplo: 

Siguiendo el ejemplo anterior de AWS, si tenemos que cambiar el tipo de máquina de la instancia EC2, Terraform destruiría la instancia actual y crearía una nueva con las configuraciones actualizadas.


## ¿Cómo se aplica esto en Terraform?

- **En infraestructura mutable**: Terraform intenta realizar cambios "in-place", es decir, ajustando los recursos sin destruirlos (por ejemplo, modificando una propiedad de un recurso como una instancia).

- **En infraestructura inmutable**: Terraform tiende a reemplazar recursos completos, creando una nueva versión del recurso cuando se detecta un cambio (por ejemplo, eliminando una instancia vieja y creando una nueva con las nuevas configuraciones).

## ¿Cómo hacer que un recurso sea mutable o inmutable en Terraform?

Terraform, **por defecto**, **es mutable** en muchos casos, lo que significa que intentará actualizar los recursos existentes si es posible, sin destruirlos. Sin embargo, en algunas situaciones, podemos forzar que un recurso sea tratado de forma inmutable, es decir, que se destruya y se cree de nuevo si se realiza un cambio.

### 1. Infraestructura Mutable en Terraform

Por defecto, Terraform es mutable para la mayoría de los recursos. Esto significa que si cambias algo en un recurso (como el tipo de una instancia EC2), Terraform intentará actualizar ese recurso sin destruirlo.

#### Ejemplo de Infraestructura Mutable (comportamiento por defecto):

Supongamos que tenemos una instancia EC2 definida:

```hcl
resource "aws_instance" "example" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
}
```
Si cambias solo el tipo de instancia:

```hcl
resource "aws_instance" "example" {
  ami           = "ami-12345678"
  instance_type = "t2.medium"  # Solo cambia el tipo de la instancia
}
```
Terraform intenta actualizar la instancia sin destruirla, ya que este es el comportamiento mutable por defecto. La instancia seguirá existiendo, pero con el nuevo tipo de instancia.

### 2. Infraestructura Inmutable en Terraform

Para hacer que un recurso se comporte de manera inmutable en Terraform, debemos asegurarnos de que, cuando hagamos un cambio, el recurso se destruya y se cree uno nuevo. Para lograr esto, hay diferentes formas de configurarlo.

#### Forma 1: Forzar la destrucción de un recurso con un `lifecycle` block

Terraform permite usar el argumento `lifecycle` con la opción `create_before_destroy` para hacer que se destruyan los recursos antiguos antes de crear uno nuevo.

**Ejemplo** de un recurso inmutable con `lifecycle`:


```hcl
resource "aws_instance" "example" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  lifecycle {
    create_before_destroy = true
  }
}
```

En este caso, cuando hagamos un cambio en el recurso (como cambiar el tipo de la instancia), Terraform primero creará el nuevo recurso y luego destruirá el antiguo. Esto asegura que el nuevo recurso se cree antes de que el viejo se destruya, lo que es común en infraestructura inmutable.

Por ejemplo, si después decidimos cambiar el tipo de la instancia:


```hcl
resource "aws_instance" "example" {
  ami           = "ami-87654321"  # Imagen diferente
  instance_type = "t2.medium"     # Nuevo tipo de instancia

  lifecycle {
    create_before_destroy = true
  }
}
```

Terraform destruirá la instancia antigua y creará una nueva con la configuración actualizada.

#### Forma 2: Cambiar el ID de un recurso (cambio forzoso)

Otra forma de garantizar que Terraform cree un nuevo recurso (en lugar de modificar el existente) es hacer un cambio que no se pueda aplicar al recurso sin destruirlo.

Por **ejemplo**, cambiar algo que es fundamental para la configuración de un recurso (como el ID de la imagen AMI), Terraform no podrá simplemente actualizar el recurso, y por lo tanto, lo destruirá y creará uno nuevo. Esto genera un comportamiento inmutable porque el recurso anterior ya no existe.

```hcl
resource "aws_instance" "example" {
  ami           = "ami-87654321"  # Imagen diferente, esto forzará la recreación
  instance_type = "t2.micro"
}
```
