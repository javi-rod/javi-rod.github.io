---
layout: post
title: Terraform - Variables de entrada
date: 2025-03-28 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code),devops]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

# ¿Qué son las variables de entrada?

Las **variables de entrada** (input variables) son valores que podemos definir en un archivo de configuración y luego pasar a los recursos, módulos u otros componentes de Terraform. Estos valores no están fijos dentro del código, lo que permite que la misma infraestructura sea desplegada en diferentes entornos (como desarrollo, staging o producción) solo cambiando los valores de las variables.

# ¿Cómo se definen y se usan las variables de entrada?

Las variables de entrada se definen en un archivo `.tf` usando el bloque `variable`, y se pueden utilizar en otros bloques de recursos de Terraform.

## Definición de una variable de entrada

Para definir una variable de entrada, usamos la palabra clave `variable` seguida del nombre de la variable y sus propiedades (como tipo, valores predeterminados, descripciones, etc.).

```hcl
variable "nombre_de_variable" {
  type        = tipo
  description = "Descripción de la variable"
  default     = valor_por_defecto
}
```
En el ejemplo anterior:

- `nombre_de_variable`: Es el nombre de la variable que vamos usar en la configuración.

- `type`: Define el tipo de datos que aceptará la variable. Puede ser string, number, bool, list, map, entre otros. Esto ayuda a evitar errores de tipo y proporciona una validación al momento de ejecutar Terraform.

- `description`: (Opcional) Una descripción que explique para qué se utiliza la variable. Es útil para documentar el código.

- `default`: (Opcional) Es un valor predeterminado para la variable. Si no se proporciona un valor cuando se ejecuta el código de Terraform, se utilizará este valor.

Ejemplo básico de definición:

```
variable "instance_type" {
  description = "El tipo de instancia de AWS"
  type        = string
  default     = "t2.micro"
}
```

En el ejemplo anterior se crea una variable llamada `instance_type` que se utiliza para definir el tipo de instancia en AWS. La variable tiene una descripción, un tipo (string), y un valor predeterminado (t2.micro).

Ahora veamos cómo usar esa variable de entrada

```
resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = var.instance_type
}
```

Aquí, estamos utilizando la variable `instance_type` (referida como `var.instance_type`) para asignar el tipo de instancia a la máquina virtual que estamos creando en AWS. Si no se proporciona un valor al ejecutar Terraform, se usará el valor predeterminado (t2.micro).

## Proveer valores a las variables

Tenemos diferentes formas de proporcionar valores a las variables definidas en Terraform:

1. **Valores por defecto**: Como en el ejemplo anterior, proporcionar un valor por defecto en la definición de la variable.

2. **Archivos de variables**: Definir las variables y sus valores en un archivo `.tfvars`, lo que permite separar la configuración de las variables de los recursos.

Ejemplo de archivo llamado `variables.tfvars`:

```hcl
instance_type = "t2.small"
```

Para hacer uso del fichero

```bash
terraform apply -var-file="variables.tfvars"
```
3. **Línea de comandos**: Al ejecutar los comandos de Terraform, podemos pasar los valores de las variables a través de la línea de comandos con la opción `-var`.

```bash
terraform apply -var="instance_type=t2.large"
```

4. **Variables de entorno**: Tenemos opción de definir las variables mediante variables de entorno, lo que es útil cuando trabajamos en entornos automatizados. El nombre de la variable debe seguir el formato `TF_VAR_<nombre_variable>`. Por ejemplo, para la variable `instance_type` anterior,  debemos establecer una variable de entorno llamada `TF_VAR_instance_type`.

Ejemplo:

Supongamos que tenemos una variable para una clave de API y una variable para la región:

```hcl
variable "api_key" {
  description = "Clave de API"
  type        = string
}

variable "region" {
  description = "Región para el despliegue"
  type        = string
}
```
Para asignar valores a estas variables utilizando variables de entorno, se deben seguir ciertos pasos.

- **Nombrar las variables de entorno**

Terraform busca automáticamente las variables de entorno que sigan el patrón `TF_VAR_<NOMBRE_DE_LA_VARIABLE>` como ya indicamos anteriormente. En este ejemplo, para la variable `api_key`, la variable de entorno será `TF_VAR_api_key`, y para la variable `region`, será `TF_VAR_region`.

- **Configurar las variables de entorno**

Para establecer las variables de entorno en sistemas basados en Unix (como Linux o macOS) desde la terminal:

```bash
export TF_VAR_api_key="my_api_key_value"
export TF_VAR_region="us-west-2"
```

En Windows (usando PowerShell):

```powershell
$env:TF_VAR_api_key="my_api_key_value"
$env:TF_VAR_region="us-west-2"
```
- **Ejecutar Terraform**

Con las variables de entorno configuradas, Terraform puede acceder a ellas automáticamente cuando se ejecutan comandos como terraform plan o terraform apply.

```bash
terraform plan
terraform apply
```
Terraform leerá las variables `TF_VAR_api_key` y `TF_VAR_region` y las usará para el despliegue de la infraestructura.


## Tipos de variables más comunes

Terraform permite varios tipos de variables. Los más comunes son:

- **string**: Cadenas de texto (por ejemplo, nombres, descripciones).

```hcl
variable "region" {
  type        = string
  description = "Región de AWS"
  default     = "us-east-1"
}
```

- **number**: Números enteros o decimales.

```hcl
variable "instance_count" {
  type        = number
  description = "Cantidad de instancias"
  default     = 3
}
```

- **bool**: Valores booleanos (true o false).

```hcl
variable "enable_ssl" {
  type        = bool
  description = "Habilitar SSL"
  default     = true
}
```

- **list**: Definir una colección de elementos del mismo tipo, como una **lista de cadenas de texto**, **números**, **booleanos**, etc. El tipo **list** se utiliza cuando quieres manejar múltiples valores de manera ordenada.

```hcl
variable "availability_zones" {
  type        = list(string)
  description = "Lista de zonas de disponibilidad"
  default     = ["us-east-1a", "us-east-1b"]
}
```

Puedes acceder a los elementos de la lista utilizando **índices**, que **comienzan en 0**, como en los arrays tradicionales.

Si queremos usar estos valores dentro de un recurso o alguna otra configuración en Terraform, lo haríamos de esta forma:

```hcl
resource "aws_instance" "example" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
  availability_zone = var.availability_zones[0]  # Accediendo al primer elemento
}
```

Si necesitamos iterar sobre todos los elementos, lo hacemos con un **bucle for**:

```hcl
output "all_availability_zones" {
  value = [for zone in var.availability_zones : zone]
}
```

Este bloque devolverá la lista completa de zonas de disponibilidad.

Terraform ofrece varias **funciones** para utilizar con listas:

- **length()**: Devuelve el número de elementos en la lista.

```hcl
length(var.availability_zones) # Devuelve 2 si la lista tiene dos elementos
```

- **element()**: Devuelve el valor de un elemento de la lista según el índice.

```hcl
element(var.availability_zones, 0) # Devuelve "us-east-1a"
```

- **join()**: Convierte una lista en una cadena de texto, uniendo los elementos con un delimitador.

```hcl
join(", ", var.availability_zones) # Devuelve "us-east-1a, us-east-1b"
```

- **contains()**: Verifica si un valor está presente en la lista.

```hcl
contains(var.availability_zones, "us-east-1c") # Devuelve true si "us-east-1c" está en la lista
```

- **flatten()**: En una lista de listas, flatten() las aplana en una sola lista.

```hcl
flatten([["a", "b"], ["c", "d"]]) # Devuelve ["a", "b", "c", "d"]
```

- **map**: Un conjunto de claves y valores (también conocido como diccionario).

```hcl
variable "ami_map" {
  type        = map(string)
  description = "Mapa de IDs de AMI"
  default     = {
    "us-east-1" = "ami-0c55b159cbfafe1f0"
    "us-west-1" = "ami-0a313d6098716f372"
  }
}
```

- **set**: **Colección de elementos únicos** que pueden **ser de cualquier tipo de datos**, como **cadenas de texto** (string), **números** (number), **booleanos** (bool), y más. Si agregamos un elemento duplicado a un set, Terraform simplemente lo ignorará. No garantiza el orden de inserción.

```hcl
variable "my_set" {
  type        = set(string) # Un set de cadenas de texto
  description = "Un conjunto de cadenas"
  default     = ["a", "b", "c"]
}
```

- **object**: Un tipo más complejo, que puede contener varios atributos con **diferentes tipos de datos**.

```hcl
variable "instance_config" {
  type = object({
    instance_type = string
    instance_name = string
    region        = string
  })
  description = "Configuración de la instancia EC2"
}
```

- **tuples**: Permiten agrupar múltiples elementos, pero a diferencia de las listas o sets, **las tuplas pueden contener elementos de diferentes tipos de datos**.

```hcl
variable "my_tuple" {
  type        = tuple([string, number, bool]) # Tupla con una cadena, un número y un booleano
  description = "Tupla con diferentes tipos de datos"
  default     = ["my_string", 42, true]
}
```

Para acceder a los elementos de una tupla,usamos el índice, como si fuera una lista. El índice comienza en 0.

### Comparación entre Tupla, Lista y Set

| Característica                         | **Tupla**                      | **Lista (list)**               | **Set**                         |
|----------------------------------------|--------------------------------|--------------------------------|---------------------------------|
| **Tipo de elementos**                  | Pueden ser de tipos diferentes | Solo un tipo de datos          | Solo elementos únicos           |
| **Orden de los elementos**             | Mantiene el orden              | Mantiene el orden              | No garantiza el orden           |
| **Acceso por índice**                  | Sí                             | Sí                             | No                              |
| **Duplicados permitidos**              | No                             | Sí                             | No                              |

## Precedencia de las variables

1. **Línea de comandos (-var y -var-file)**: Los valores proporcionados directamente en la línea de comandos tienen la más alta precedencia.

2. **Archivos .tfvars**: Los valores definidos en archivos `.tfvars` o `.tfvars.json` tienen alta precedencia.

3. **Valores predeterminados en archivos `.tf`**: Si no se especifican otros valores, Terraform usa los valores predeterminados definidos en los archivos de configuración `.tf`.
