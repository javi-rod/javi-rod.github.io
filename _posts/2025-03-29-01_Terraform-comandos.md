---
layout: post
title: Terraform - Comandos
date: 2025-03-29 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code)]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

## `terraform init`

Prepara el entorno para empezar a usar Terraform para **crear, actualizar o destruir infraestructura**. Si no lo ejecutamos primero, Terraform no podrá operar correctamente.

Realiza estos 3 pasos:

- **Inicializa el backend**: Si usamos un **backend remoto** (por ejemplo, S3, GCS, Terraform Cloud), `terraform init` configura ese backend para almacenar el estado de la infraestructura.

- **Descarga los proveedores (providers)**: Terraform necesita **"proveedores"** para interactuar con diferentes plataformas, como **AWS, Azure, Google Cloud, etc.** `terraform init` descarga los proveedores necesarios según lo especificado en los archivos `.tf`.

- **Prepara los módulos**: Si se usan **módulos** (bloques reutilizables de código), `terraform init` los descarga y los configura.

<img src="https://javi-rod.github.io/assets/images/20250329/01_terraform_init.png" alt="Ejemplo comando terraform init" />

## `terraform validate`

Revisa todos los archivos de configuración `.tf` del directorio actual. Esto nos permite verificar que la **sintaxis** de los archivos de configuración es correcta. Nos asegura de que las **referencias entre recursos y módulos** son válidas. Y también nos ayuda a detectar posibles **errores de configuración** antes de aplicar cambios en la infraestructura.

Es capaz de detectar **errores de sintaxis**, **referencias no válidas entre recursos y módulos**, y posibles **errores de configuración**.


<img src="https://javi-rod.github.io//assets/images/20250329/02_terraform_validate_01.png" alt="Ejemplo comando terraform validate donde vemos que da error porque hay un argumento no soportado" />

Tras cambiar dsa_bits por rsa_bits vemos que ahora la validación no da errores.

<img src="https://javi-rod.github.io/assets/images/20250329/03_terraform_validate_02.png" alt="Tras corregir error, lanzamos terraform init y no devuelve errores" />

## `terraform plan`

Lee los archivos de configuración de Terraform y el **estado actual** de la infraestructura. Compara el estado actual con la configuración deseada y genera un **plan** describiendo las acciones que se llevarán a cabo para alinear la infraestructura con la configuración deseada. **No hace cambios reales en la infraestructura**, sólo muestra lo que hará cuando ejecutemos `terraform apply`.


<img src="https://javi-rod.github.io/assets/images/20250329/04_terraform_plan.png" alt="Ejemplo comando terraform plan" />

## `terraform apply`

Leerá todos los ficheros de configuración y el **estado actual** de la infraestructura. Comparará el estado actual con la configuración deseada y **aplicará los cambios necesarios** para alinear la infraestructura con la configuración. Antes de aplicar los cambios se nos pedirá **confirmar**.

Usar `-auto-approve` omite la solicitud de confirmación, lo que puede ser **un riesgo en entornos de producción**, ya que aplica cambios sin una segunda verificación y por tanto se desaconseja.


<img src="https://javi-rod.github.io/assets/images/20250329/05_terraform_apply_01.png" alt="Ejemplo comando terraform apply" />

<img src="https://javi-rod.github.io/assets/images/20250329/06_terraform_apply_02.png" alt="Ejemplo comando terraform apply que da error" />

¡El comando `terraform apply` falló a pesar de que nuestra validación funcionó! Esto se debe a que el comando `terraform validate` **solo realiza una verificación general** de la configuración. **Valida el bloque de recursos y la sintaxis** de los argumentos, pero **no los valores que los argumentos esperan** para un recurso específico.

Tras resolver el problema, relacionado con el recurso `tls_private_key` y el argumento `ecdsa_curve`, ejecutamos nuevamente `terraform plan` y `luego terraform apply`, lo que permite completar la operación sin errores.

<img src="https://javi-rod.github.io/assets/images/20250329/07_terraform_apply_03.png" alt="Ejemplo comando terraform apply con salida exitosa" />

## ⚠️ **Recordatorio**:

**Recuerda** que `terraform init` debe **ejecutarse primero** antes de cualquier otro comando. Esto prepara el entorno para que Terraform pueda interactuar correctamente con proveedores, módulos y el backend. Es **obligatorio** para asegurarte de que todo esté configurado correctamente antes de realizar otras acciones.