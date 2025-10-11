---
layout: post
title: Terraform - Reglas ciclo de vida
date: 2025-03-29 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code),devops]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

En Terraform, las **reglas de ciclo de vida (lifecycle)** nos permiten **controlar** cómo Terraform maneja los recursos durante su **creación**, **modificación** y **destrucción**.

## Ciclo de vida de un recurso en Terraform

El ciclo de vida de un recurso en Terraform abarca varias **fases**:

1. **Creación**: Terraform **crea** el recurso en el proveedor.

2. **Actualización**: Terraform **actualiza** el recurso cuando se detectan cambios en la configuración.

3. **Destrucción**: Terraform **elimina** el recurso cuando se elimina de la configuración o cuando se ejecuta el comando terraform destroy.

Terraform proporciona un **bloque** llamado `lifecycle` para controlar cómo se deben gestionar estas fases. El bloque `lifecycle` contiene varias reglas importantes que afectan cómo Terraform trata los recursos durante esas fases.

## Reglas de ciclo de vida en Terraform

Las reglas principales de ciclo de vida en Terraform son:

1. `create_before_destroy`: Determina si un recurso debe ser creado antes de destruir el recurso existente.

2. `prevent_destroy`: Evita que un recurso sea destruido, incluso si se elimina de la configuración o se solicita explícitamente la destrucción.

3. `ignore_changes`: Permite ignorar ciertos cambios en los atributos del recurso para evitar que Terraform realice una actualización en ese atributo.

4. `replace_triggered_by`: Fuerza la destrucción y creación de un recurso cuando otro recurso específico cambia.


## Ejemplos prácticos de cada regla de ciclo de vida

1. `create_before_destroy`

Esta opción garantiza que Terraform cree el recurso nuevo antes de destruir el recurso existente. Esto es útil para evitar períodos en los que no haya un recurso disponible.

**Ejemplo**: Cambiar el tipo de instancia EC2, asegurando de que la instancia nueva se cree antes de destruir la instancia antigua.

```hcl
resource "aws_instance" "example" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  lifecycle {
    create_before_destroy = true  # Crear la nueva instancia antes de destruir la vieja
  }
}

```

Ahora, si cambiamos el tipo de instancia a `t2.medium`:

```hcl
resource "aws_instance" "example" {
  ami           = "ami-12345678"
  instance_type = "t2.medium"

  lifecycle {
    create_before_destroy = true
  }
}

```

Terraform primero creará una nueva instancia con el tipo `t2.medium`, y destruirá la instancia antigua después de que la nueva instancia esté en funcionamiento.

2. `prevent_destroy`

Esta regla impide que un recurso sea destruido de forma accidental. Incluso si el recurso se elimina de la configuración o si ejecutamos terraform destroy, el recurso no será destruido.

**Ejemplo**: Prevenir que una instancia EC2 sea destruida accidentalmente.

```hcl
resource "aws_instance" "example" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  lifecycle {
    prevent_destroy = true  # No permitirá que la instancia sea destruida
  }
}

```

Con esta configuración, incluso si intentamos eliminar  este recurso con terraform destroy o si lo eliminas de la configuración de Terraform, no se destruirá. Terraform devolverá un error.

3. `ignore_changes`

Esta regla permite a Terraform ignorar cambios en atributos específicos de un recurso. Si Terraform detecta un cambio en un atributo que se ha marcado con ignore_changes, no lo actualizará ni intentará modificar ese atributo.

**Ejemplo**: Ignorar cambios en el tags de una instancia EC2. Si cambiamos los tags manualmente, Terraform no intentará volver a aplicar los tags definidos en la configuración.

```hcl
resource "aws_instance" "example" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  tags = {
    Name = "MyInstance"
  }

  lifecycle {
    ignore_changes = [tags]  # Ignorar cambios en los tags
  }
}

```

Si modificamos los tags manualmente en AWS, Terraform no los cambiará al ejecutar terraform apply, ya que ha sido configurado para ignorar esos cambios.

4. `replace_triggered_by`

Esta regla permite que un recurso sea destruido y recreado en respuesta a cambios en otro recurso específico. Es útil cuando ciertos recursos dependen de otros y, si uno cambia, el otro también debe ser reemplazado.

**Ejemplo**: Si cambiamos el nombre de un bucket de S3, forzaremos la recreación de una instancia EC2 asociada.

```hcl
resource "aws_s3_bucket" "example" {
  bucket = "my-example-bucket"
}

resource "aws_instance" "example" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  lifecycle {
    replace_triggered_by = [aws_s3_bucket.example]  # Si el bucket cambia, se reemplazará la instancia
  }
}

```

Aquí, si hacemos un cambio en el bucket S3 (por ejemplo, cambiar su nombre), la instancia EC2 será destruida y recreada, porque Terraform está configurado para que la instancia dependa del estado del bucket.

## Resumen de las reglas de ciclo de vida en Terraform

- `create_before_destroy`: Crea el **nuevo recurso** antes de destruir el antiguo. Utilizado cuando se desea evitar interrupciones.

- `prevent_destroy`: **Evita** que un recurso sea **destruido**, incluso si se **elimina** de la configuración o se ejecuta terraform destroy.

- `ignore_changes`: Permite que Terraform **ignore** ciertos **cambios** en atributos específicos de un recurso, sin actualizarlos.

- `replace_triggered_by`: **Fuerza la destrucción y creación** de un recurso cuando otro recurso específico cambia.

## ¿Cuándo usar cada una de estas reglas?

- `create_before_destroy` cuando **no queremos interrumpir un servicio** y necesitamos que siempre haya un recurso disponible.

- `prevent_destroy` cuando queremos **proteger un recurso crítico** de la destrucción accidental.

- `ignore_changes` si deseamos **evitar que Terraform modifique ciertos atributos**, como etiquetas o configuraciones que cambian fuera de Terraform.

- `replace_triggered_by` cuando deseemos que un **recurso se reemplace debido a cambios en otro recurso dependiente**.
