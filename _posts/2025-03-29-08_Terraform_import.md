---
layout: post
title: Terraform - Import
date: 2025-03-29 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code)]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

## ¿Qué es Terraform Import?

Terraform es una herramienta de infraestructura como código (IaC) que facilita la gestión automatizada de recursos en la nube.Sin embargo, ¿qué sucede cuando ya tenemos recursos creados fuera de Terraform y necesitamos gestionarlos con él?

Aquí es donde entra `terraform import`. Este **comando** se utiliza para importar recursos que ya existen en nuestra infraestructura a Terraform, de modo que podamos gestionarlos mediante archivos de configuración de Terraform.

### Ejemplo 1: Importar una Instancia EC2 de AWS

Supongamos que en AWS tenemos una instancia EC2 que ya fue creada, pero ahora necesitamos gestionarla con Terraform.

Haremos lo siguiente:

1. **Identificación del recurso**: Es necesario conocer el identificador único del recurso. Para una instancia EC2, este identificador se ve algo así como *i-1234567890abcdef0*.

2. **Importación del recurso**: Ahora, lanzamos el siguiente comando en la terminal:

```bash
terraform import aws_instance.mi_instancia_ec2 i-1234567890abcdef0
```

En este comando:

- `aws_instance.mi_instancia_ec2` es el **nombre del recurso** en Terraform, el cual debe coincidir con el tipo de recurso (`aws_instance`) y el nombre que le demos a la instancia (`mi_instancia_ec2`).

- `i-1234567890abcdef0` es el **ID de la instancia EC2** que se va a importar.

Con lo anterior, Terraform tendrá información sobre esa instancia y la agregará al estado de Terraform, lo que permitirá gestionarla en el futuro pero no generará los archivos de configuración.

### Ejemplo 2: Importar un Bucket de S3

Tenemos un bucket de S3 que ya está creado y queremos importarlo, el proceso es muy similar al anterior.

1. **Identificación del recurso**: Debemos conocer el **nombre del bucket de S3**. Por ejemplo, `mi-bucket-ejemplo`.

2. Ejecutar la importación del recurso:

```bash
terraform import aws_s3_bucket.mi_bucket_s3 mi-bucket-ejemplo
```

En el comando anterior:

- `aws_s3_bucket.mi_bucket_s3`: **Tipo de recurso** y **nombre del recurso** en Terraform.

- `mi-bucket-ejemplo`: **Nombre exacto del bucket** de S3.

## ¿Qué pasa después de la importación?

Una vez que el recurso ha sido importado, **Terraform lo registra en su estado**. Sin embargo, **no crea automáticamente los archivos de configuración** de Terraform (los `.tf`). Esto significa que después de importar el recurso, tenemos que crear o actualizar los archivos de configuración manualmente para que coincidan con el estado del recurso.

## Resumen

- `terraform import` permite traer recursos existentes a Terraform.

- Se necesita **conocer el identificador único del recurso** a importar.

- Después de la importación, es **necesario definir o actualizar los archivos `.tf`** para gestionarlos correctamente.
