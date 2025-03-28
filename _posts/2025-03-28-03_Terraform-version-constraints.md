---
layout: post
title: Terraform - Version constraints
date: 2025-03-28 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code)]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

Las **version constraints** (restricciones de versión) en Terraform son una forma de controlar qué versiones de los proveedores y de Terraform se pueden utilizar en nuestra configuración. Estas restricciones permiten asegurar que la infraestructura se ejecute con versiones compatibles y evitar actualizaciones inesperadas que puedan causar problemas.

Terraform permite especificar restricciones tanto para la versión de Terraform como para las versiones de los proveedores que usemos en nuestra configuración.

### Version Constraints en Terraform

Existen varias maneras de definir restricciones de versión, y se utilizan para garantizar la estabilidad de nuestra infraestructura a lo largo del tiempo.

#### 1 - Restricción de versión de Terraform

Cuando definimos un archivo terraform con el bloque `required_version`, estamos especificando qué versiones de Terraform pueden usarse para ejecutar la configuración. Esto es útil para asegurar de que nuestra infraestructura se maneje solo con versiones de Terraform que sean compatibles con nuestra configuración.

Ejemplo:

```hcl
terraform {
  required_version = ">= 1.1.0, < 2.0.0"
}
```

En este ejemplo, indicamos que Terraform debe ser versión 1.1.0 o superior, pero menor que 2.0.0.

#### 2 - Restricción de versión de un proveedor

Al igual que podemos restringir la versión de Terraform, también podemos restringir las versiones de los proveedores (providers) que usemos en nuestra configuración. Los proveedores son las bibliotecas que Terraform utiliza para interactuar con las plataformas en la nube o servicios externos como vimos en el punto anterior dedicado a providers.

Ejemplo de versión de un proveedor:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = ">= 3.0.0, < 4.0.0"
    }
  }
}
```
En este ejemplo:

- `source`: Especifica el origen del proveedor. En este caso el proveedor oficial de AWS desde HashiCorp.

- `version`: Define la restricción de versión para el proveedor de AWS. En este caso, el proveedor debe ser versión 3.x.x pero menor que 4.0.0.

### Tipos de restricciones de versión

Terraform utiliza una sintaxis muy flexible para definir las restricciones de versiones, y podemos combinarlas de varias maneras para crear las restricciones que más nos convengan. 

A continuación se muestran algunas de las formas más comunes de definir las restricciones:

**Uso de operadores**

- `=` : Exactamente esa versión.

- `>=` : Mayor o igual que esa versión.

- `<=` : Menor o igual que esa versión.

- `>` : Mayor que esa versión.

- `<` : Menor que esa versión.

 - `,` : Especifica un rango de versiones.

Ejemplos de restricciones comunes:

\- **Versión exacta**:

```hcl
terraform {
  required_version = "= 1.1.0"
}
```

Esto asegura que solo se puede usar la versión 1.1.0 de Terraform.

\- **Mayor o igual que una versión**:

```hcl
terraform {
  required_version = ">= 1.0.0"
}
```
Esto asegura que cualquier versión de Terraform igual o superior a la 1.0.0 es compatible.

\- **Rango de versiones**:

```hcl
terraform {
  required_version = ">= 1.0.0, < 2.0.0"
}
```

En este caso, Terraform solo usará versiones desde la 1.0.0 hasta antes de la 2.0.0.

\- **Exclusión de una versión específica**:

```hcl
terraform {
  required_version = "!= 1.3.0"
}
```

Esto asegura que no se utilice la versión 1.3.0 de Terraform.

\- **Ejemplo completo con proveedores**:

```hcl
terraform {
  required_version = ">= 1.1.0, < 2.0.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = ">= 3.0.0, < 4.0.0"
    }
    google = {
      source  = "hashicorp/google"
      version = ">= 3.50.0"
    }
  }
}
```

En este ejemplo:

- Terraform debe ser >= 1.1.0 y menor que 2.0.0.

- El proveedor aws debe estar en la versión 3.x.x, pero menor que 4.0.0.

- El proveedor google debe estar en la versión >= 3.50.0.


### ¿Por qué es importante usar restricciones de versión?

1. **Evitar actualizaciones inesperadas**: Al usar restricciones de versión, evitamos que Terraform o los proveedores se actualicen a versiones no compatibles con nuestra infraestructura.

2. **Compatibilidad de configuración**: Algunas características de versiones más recientes de Terraform o de un proveedor pueden no ser compatibles con las configuraciones más antiguas, por lo que las restricciones ayudan a mantener la estabilidad.

3. **Control de cambios**: Si no usamos restricciones, Terraform podría intentar actualizarse automáticamente a la última versión disponible, lo que podría resultar en cambios no deseados en el comportamiento de nuestra infraestructura.

### Consejos sobre Buenas Prácticas

- **Bloque `required_version`**: Es recomendable el uso de un rango de versiones, por ejemplo, `>= 1.1.0, < 2.0.0` en lugar de una versión exacta como `= 1.1.0`, ya que Terraform lanza actualizaciones frecuentes que pueden incluir parches de seguridad importantes.

- **Dependencias del Proveedor**: Es útil mantener un archivo de bloqueo (`.terraform.lock.hcl`) para asegurar que todas las instancias del proyecto usen las mismas versiones de proveedores, lo que evita que los miembros del equipo tengan configuraciones incompatibles.

Cuando se utiliza un archivo de bloqueo, Terraform garantiza que las mismas versiones de los proveedores se descarguen en cada entorno, evitando diferencias de comportamiento.

```bash
terraform init  # Esto generará o actualizará el archivo .terraform.lock.hcl
```