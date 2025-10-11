---
layout: post
title: Terraform - Debugging
date: 2025-03-29 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code),devops]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

## Debugging en Terraform

Terraform ofrece varias herramientas y enfoques para ayudarnos a diagnosticar y solucionar problemas. A continuación, tenemos algunas de las mejores prácticas y herramientas para realizar debugging en Terraform:

### 1. Habilitar la salida de depuración con variables de entorno

Terraform ofrece una forma de obtener información detallada utilizando variables de entorno. Habilitaremos el modo de depuración estableciendo las siguientes variables antes de ejecutar los comandos de Terraform:

- `TF_LOG`: Especifica el nivel de registro que deseas obtener. Los niveles disponibles son:

    - `TRACE`: La salida más detallada (para un diagnóstico profundo).

    - `DEBUG`: Información detallada, útil para la depuración.

    - `INFO`: Información sobre las acciones que está realizando Terraform (predeterminado).

    - `WARN`: Advertencias de problemas posibles.

    - `ERROR`: Solo errores graves.

Para habilitar el modo de depuración, debemos establecer la variable de entorno `TF_LOG` en el nivel que deseamos antes de ejecutar los comandos de Terraform.

**Ejemplo 1**:

```bash
export TF_LOG=DEBUG
```

Esto hará que Terraform imprima mucha más información de depuración en la salida estándar. 

Si queremos ver los **logs** en un archivo para revisarlos más tarde, podemos **redirigir la salida** a un archivo de log:

```bash
export TF_LOG=DEBUG
export TF_LOG_PATH=./terraform_debug.log
```

Esto creará un archivo `terraform_debug.log` con todos los logs detallados.

**Ejemplo 2**:

Tenemos un directorio de configuración llamado `/root/terraform-projects/ProjectA`. Habilitaremos el registro con el nivel de registro establecido en `ERROR` y **exportaremos** los registros a la ruta `/tmp/ProjectA.log`.

<img src="https://javi-rod.github.io/assets/images/20250329/08_export_TF_LOG.png" alt="Formas de Instalar Terraform, vía Gestor Paquetes o descarga de binario"/>

Al ejecutar `terraform apply` recibimos un error. Ese error quiere decir que no puede autenticarse correctamente con AWS

<img src="https://javi-rod.github.io/assets/images/20250329/09_terraform_apply_fail.png" alt="Formas de Instalar Terraform, vía Gestor Paquetes o descarga de binario" />

Ahora, si revisamos el fichero generado para los logs,  vemos el mismo error que nos ha dado el `terraform apply`

<img src="https://javi-rod.github.io/assets/images/20250329/10_cat_log.png" alt="Formas de Instalar Terraform, vía Gestor Paquetes o descarga de binario"/>


### 2. Verifica el estado de Terraform

El archivo de **estado** de Terraform (`terraform.tfstate`) es crucial para entender el estado actual de la infraestructura que gestionamos. Si algo no está funcionando como esperamos, revisaremos este archivo para asegurarnos de que Terraform esté gestionando los recursos correctamente.

Para ver el **estado** de la infraestructura, usaremos:

```bash
terraform show
```

Este comando mostrará el estado actual de todos los recursos gestionados por Terraform, lo cual puede ayudar a identificar discrepancias.

### 3. Revisar los planes de Terraform

Antes de aplicar cualquier cambio, es una buena práctica generar un plan de ejecución usando el comando terraform plan. Esto mostrará qué cambios intentará hacer Terraform, y es una excelente manera de detectar problemas antes de que ocurran.

```bash
terraform plan
```

El plan mostrará los cambios que se realizarán en los recursos. Si Terraform intenta realizar cambios inesperados, este comando nos permitirá analizar qué y por qué.

### 4. Usar terraform validate
 
El comando `terraform validate` valida tanto los **módulos** como los **valores de las variables**, además de la **sintaxis de los archivos** `.tf`

```bash
terraform validate
```

Este comando puede ser útil para detectar errores obvios en los archivos `.tf` antes de intentar aplicar los cambios.

### 5. Revisar las dependencias de los recursos

A veces los errores en Terraform ocurren debido a dependencias no declaradas o incorrectamente configuradas entre recursos. Terraform permite definir explícitamente dependencias con `depends_on`, pero en la mayoría de los casos, es suficiente con confiar en su sistema de dependencias implícitas.

Si dudamos sobre las dependencias entre recursos, tenemos que asegurarnos de que Terraform esté manejando los recursos en el orden correcto y de que las dependencias entre ellos estén correctamente configuradas.

### 6. Revisar la salida de los comandos apply y destroy

Al ejecutar `terraform apply` o `terraform destroy`, asegurarnos de revisar la salida para ver si Terraform está informando de algún error o advertencia. Terraform intentará explicar por qué no pudo crear, modificar o destruir recursos, lo que puede ser muy útil para encontrar el origen del problema.

### 7. Utilizar terraform taint y terraform untaint

Si tenemos un recurso que parece estar en un estado inconsistente o no está funcionando como se espera, podemos usar `terraform taint` para marcarlo como "dañado". Esto forzará su recreación en el próximo `terraform apply`, lo cual puede ser útil para hacer pruebas y depurar.

```bash
terraform taint <tipo_recurso>.<nombre_recurso>
```

Si deseamos revertir la acción, podemos usar `terraform untaint`, lo que **eliminará** la marca de 'dañado' y evitará que el recurso sea recreado.

### 8. Usar terraform console para inspeccionar valores

Terraform tiene una consola interactiva que nos permite inspeccionar el estado y los valores de las variables, recursos y otros objetos de Terraform. Esto puede ser útil para verificar cómo se están evaluando ciertas expresiones o investigar variables y outputs.

Para abrir la consola:

```bash
terraform console
```

Con terraform console, podemos realizar consultas interactivas, como evaluar expresiones de variables o verificar el estado de los recursos definidos.

**Ejemplo**

```
> var.mi_variable
> aws_instance.mi_instancia.public_ip
```

### 9. Usar módulos con cuidado

Cuando utilicemos **módulos** en Terraform, debemos asegurarnos de que estén correctamente definidos y configurados. A veces, los problemas de depuración provienen de la forma en que se gestionan los módulos o de versiones incompatibles.

### 10. Revisar la documentación del proveedor

Cada proveedor (AWS, Google Cloud, Azure, etc.) tiene su propia documentación y es posible que Terraform esté enfrentando un problema específico relacionado con ese proveedor. Revisar la documentación de la API del proveedor para asegurarnos de que los recursos y las configuraciones que usamos son válidos.

## Resumen

Para hacer **debugging** en Terraform, puedes seguir estos pasos y utilizar las herramientas mencionadas:

1. Habilitar el **modo de depuración** con `TF_LOG` y ver la salida detallada.

2. **Verificar el estado** actual de la infraestructura con `terraform show`.

3. Usar `terraform plan` para **revisar los cambios propuestos** antes de aplicarlos.

4. Ejecutar `terraform validate` para **validar la sintaxis y configuración**.

5. Revisar **dependencias** entre recursos.

6. Revisar la salida de `terraform apply` y buscar mensajes de error o advertencias.

7. Usar `terraform taint` y `untaint` para forzar la recreación de recursos dañados.

8. Utilizar `terraform console` para inspeccionar valores de variables y recursos.