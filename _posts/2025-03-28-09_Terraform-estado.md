---
layout: post
title: Terraform - Estado
date: 2025-03-29 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code),devops]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

El **estado** en Terraform es un archivo que contiene la información sobre los recursos que han sido creados, modificados o eliminados en nuestra infraestructura. Este archivo permite a Terraform realizar un seguimiento de los cambios que necesita aplicar para que la infraestructura se ajuste a la configuración declarada en los archivos .tf.

El estado sirve como una base para:

- Determinar qué recursos necesitan ser creados, modificados o eliminados.

- Mantener la relación entre los recursos definidos y los de la infraestructura real.

- Mejorar la eficiencia al evitar hacer llamadas a la API en cada ejecución de terraform apply.

### ¿Dónde se guarda el estado?

Por defecto, Terraform guarda el archivo de estado de forma local en el directorio donde se ejecuta el comando, en un archivo llamado terraform.tfstate. Este archivo contiene toda la información detallada sobre los recursos de infraestructura que Terraform ha gestionado.

### Tipos de estado en Terraform

#### 1. Estado Local:

- Este es el estado por defecto que se guarda en un archivo local llamado `terraform.tfstate`. Este archivo está en el directorio de trabajo de Terraform.

- Es adecuado para proyectos pequeños o individuales, pero no es recomendado para equipos, ya que puede haber problemas de concurrencia si múltiples personas están trabajando con el mismo archivo de estado.

#### 2. Estado Remoto:

- Para proyectos más grandes o para trabajar en equipo, es recomendable almacenar el estado de forma remota.

- El estado remoto se puede almacenar en diversas ubicaciones, como AWS S3, Azure Blob Storage, Google Cloud Storage, Terraform Cloud, entre otros.

- Usar estado remoto garantiza que todos los miembros del equipo tengan acceso al mismo archivo de estado y puede habilitar el bloqueo de estado para evitar modificaciones concurrentes.

### Bloqueo de Estado

El bloqueo de estado es una característica importante cuando se usa un estado remoto. Esto evita que varias personas o procesos modifiquen el mismo archivo de estado al mismo tiempo, lo que podría generar inconsistencias o errores.
Cuando el estado está almacenado remotamente (por ejemplo, en S3 con un DynamoDB para bloqueo), Terraform puede asegurarse de que solo una persona o proceso pueda hacer cambios en el estado en un momento dado.

No todos los backends de Terraform tienen soporte para bloqueo de estado, pero los backends más comunes y utilizados en entornos colaborativos tienen esta capacidad. Algunos ejemplos son:

- **Amazon S3 + DynamoDB**: Cuando usas S3 como backend, puedes habilitar el bloqueo de estado utilizando una tabla de DynamoDB. DynamoDB maneja el mecanismo de bloqueo para evitar que múltiples usuarios realicen operaciones al mismo tiempo.

- **Azure Blob Storage**: Azure Blob Storage también puede ser usado para almacenamiento remoto y, mediante un mecanismo interno, soporta bloqueo de estado.

- **Google Cloud Storage (GCS)**: Google Cloud Storage también soporta el bloqueo de estado de manera similar.

- **Terraform Cloud/Enterprise**: Terraform Cloud y Terraform Enterprise proporcionan un backend gestionado por HashiCorp que automáticamente maneja el bloqueo de estado sin necesidad


### ¿Cómo Terraform maneja los estados?

Al ejecutar `terraform apply`, Terraform:

1. Compara el archivo de estado con la configuración definida en los archivos `.tf`.

2. Determina las diferencias entre el estado actual y la configuración deseada.

3. Aplica los cambios necesarios para que la infraestructura coincida con la configuración.

Si se realiza un cambio fuera de Terraform (por ejemplo, manualmente en la consola de la nube), el estado ya no estará sincronizado, lo que podría llevar a que Terraform detecte diferencias y realice acciones inesperadas. Para evitar esto, Terraform permite usar el comando terraform refresh para actualizar el estado a partir de la infraestructura real.

### Archivos de estado y seguridad

El archivo de estado contiene información sensible, como las claves de acceso de recursos, secretos, direcciones IP, etc. Por lo tanto, es importante manejar este archivo con precaución. Cuando se usa un estado remoto, asegúrate de habilitar la encriptación en reposo para proteger estos datos sensibles.

### Configuración de estado remoto

Para configurar el estado remoto, usaremos el bloque `backend`. 

Ejemplo para S3:

```hcl
terraform {
  backend "s3" {
    bucket = "mi-bucket-terraform"
    key    = "path/to/my/key"
    region = "us-west-2"
  }
}
```

### Comandos útiles para trabajar con el estado

- **terraform state list**: Muestra todos los recursos en el archivo de estado.

<img src="https://javi-rod.github.io/assets/images/20250328/05_terraform_state_list.png" alt="Ejemplo comando terraform state list" />

- **terraform state show <recurso>**: Muestra los detalles de un recurso específico.

<img src="https://javi-rod.github.io/assets/images/20250328/06_terraform_state_show.png" alt="Ejemplo comando terraform state show recurso" />

- **terraform state rm <recurso>**: Elimina un recurso del estado sin destruirlo en la infraestructura.

<img src="https://javi-rod.github.io/assets/images/20250328/07_terraform_state_rm.png" alt="Ejemplo comando terraform rm recurso" />

- **terraform state pull**: Extrae el estado remoto al sistema local.

- **terraform state push**: Sube un archivo de estado modificado a un backend remoto.