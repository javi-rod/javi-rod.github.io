---
layout: post
title: Terraform - taint y untaint
date: 2025-03-29 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code),devops]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

## Taint en Terraform

En Terraform, el comando `taint` se usa para marcar un recurso como "dañado" o "corrupto". Es decir, cuando marcamos un recurso como **tainted** (dañado), Terraform lo considerará inestable o fuera de estado. En la siguiente ejecución de `terraform apply`, Terraform destruirá ese recurso y lo volverá a crear desde cero, incluso si la configuración no ha cambiado. Esto puede ser útil cuando un recurso está en un estado inconsistente, o si deseamos forzar la recreación de un recurso sin tener que modificar la configuración.

Terraform mantiene un archivo de estado que guarda la configuración actual de los recursos en nuestra infraestructura. Si algo cambia fuera de Terraform o si un recurso se comporta de manera inesperada, puedes marcarlo como **tainted** para forzar que Terraform lo vuelva a crear desde cero.

**Ejemplo**:

```bash
terraform taint aws_instance.mi_instancia
```

Esto marca el recurso `aws_instance.mi_instancia` como tainted, es decir, lo marca como dañado.

## Untaint en Terraform

El comando `untaint` se usa para revertir la marca de "dañado" a un recurso que previamente fue marcado con `taint`. Esto significa que el recurso ya no será considerado inconsistente, y Terraform lo dejará intacto en la próxima ejecución de `terraform apply`. Usar `untaint` es útil si accidentalmente marcamos un recurso como dañado y deseamos revertir esa acción.

**Ejemplo**:

```bash
terraform untaint aws_instance.mi_instancia
```

Esto elimina la marca de **tainted** de `aws_instance.mi_instancia`, y Terraform ya no lo tratará como un recurso dañado.

### Ejemplo práctico.

Imagina que tenemos una instancia de EC2 en AWS que ha fallado y necesitamos recrearla sin cambiar la configuración de la infraestructura. Podemos usar `taint` para marcarla y forzar su recreación:

#### 1. Marcas el recurso como dañado:

```bash
terraform taint aws_instance.mi_instancia
```

#### 2. Al ejecutar `terraform apply`, Terraform destruirá y recreará la instancia EC2.

Si luego decidimos que no es necesario destruir el recurso y queremos que permanezca intacto, podemos usar `untaint` para revertirlo.

### En resumen:

- **Taint**: Marca un recurso como **dañado**, lo que provoca que se **destruya** y se vuelva a **crear** en el próximo `apply`.

- **Untaint**: **Revierte** la acción de **taint**, lo que significa que el recurso ya **no se destruirá** en el siguiente `apply`.

Los **estados** de **taint** y **untaint** **no son automáticos** por sí mismos durante un `terraform apply` estándar, ya que son comandos que debemos ejecutar manualmente. Sin embargo, hay algunos escenarios en los que Terraform puede decidir que un recurso necesita ser recreado, aunque no sea específicamente por el uso de **taint**. Vamos a ver más de cerca cómo funciona esto.

## ¿Pueden ser automáticos los estados tainted?

### 1. Causas externas: 

Terraform **no marca automáticamente los recursos** como **tainted** a menos que haya un cambio en el estado que lo requiera. 

Si un recurso cambia fuera de Terraform, por ejemplo manualmente desde AWS, el siguiente `terraform apply` podría detectar una discrepancia entre el estado de Terraform y el estado real del recurso. Esto, en algunos casos, puede hacer que Terraform decida **destruir** y **recrear** el recurso (si es necesario), pero no es lo mismo que marcarlo manualmente con **taint**. En este caso, Terraform solo podría hacer una re-creación si detecta una inconsistencia, pero no directamente un **taint**.

### 2. Cambios en la configuración: 

Si modificamos la configuración de un recurso en los archivos de Terraform y esa modificación afecta a la naturaleza del **recurso** (por ejemplo, cambias un atributo importante como el tipo de máquina EC2), Terraform marcará ese **recurso** para recrearse (no es un taint como tal, pero sí será destruido y creado nuevamente). Este comportamiento es distinto a un **taint**, ya que se basa en la lógica de Terraform para aplicar cambios a la infraestructura.

### 3. Automatización con herramientas externas:

Aunque **taint** y **untaint** son manuales por defecto, puedes integrar estas acciones en flujos de trabajo automatizados. Si tienes un sistema de monitoreo que detecta un fallo en un recurso, puedes configurar un script para ejecutar automáticamente `terraform taint` para marcar el recurso como dañado, lo que forzará su recreación en el siguiente `terraform apply`.

Por ejemplo, si tienes un recurso EC2 y experimenta fallos a nivel de hardware o en su configuración interna que lo dejan inutilizable, puedes integrar una solución automatizada que marque el recurso como tainted, lo que forzará su recreación en el siguiente `terraform apply`.

## Resumen:

- **Taint** y **untaint** no son automáticos en Terraform, requieren comandos manuales.

- Terraform puede destruir y recrear recursos automáticamente si detecta inconsistencias en el estado, pero esto no es lo mismo que marcar el recurso como tainted explícitamente.

- Si quieres una solución más automática, puedes integrar taint en algún flujo de trabajo automatizado, pero eso no ocurre de forma predeterminada con solo ejecutar `terraform apply`.