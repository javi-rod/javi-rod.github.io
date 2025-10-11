---
layout: post
title: Terraform - Funciones
date: 2025-03-30 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code),devops]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

Las **funciones** en Terraform son **bloques de código predefinidos** que toman uno o más **valores de entrada (argumentos)** y **devuelven un resultado**. Se utilizan para trabajar con **variables**, **recursos**, **cadenas de texto**, **listas**, **mapas**, y mucho más.

Las funciones permiten manipular y transformar datos dentro de los archivos de configuración de Terraform, ayudando a crear recursos de forma más flexible y automatizada.

En los ejemplos siguientes vamos a ver palabras como `variable`y `output`. Como recordatorio, en Terraform, las **variables** son valores reutilizables dentro de nuestra configuración, mientras que los **outputs** son resultados que se muestran al final de la ejecución.

## Tipos Comunes de Funciones en Terraform

Terraform tiene muchas funciones útiles, pero algunas de las más comunes incluyen:

- `length`: Devuelve la longitud de una lista o cadena.Es útil para saber el tamaño de una lista o cadena. Muy útil en la validación de datos o para la creación de múltiples recursos basados en la longitud de una lista.

- `join`: Une los elementos de una lista en una sola cadena.Es útil cuando quieres convertir una lista en una cadena para mostrarla o usarla en un recurso que requiera un solo valor de texto.

- `lower` y`upper`: Convierte una cadena de texto a minúsculas o mayúsculas.

- `concat`: Combina dos o más listas en una sola.Ideal para combinar varias listas sin tener que crear una nueva lista manualmente.

- `lookup`: Busca un valor en un mapa.

- `count`: Se usa para crear múltiples instancias de un recurso.

### Diferencia entre count y for_each

`count`: Se usa cuando necesitamos crear un número fijo de instancias, y cada una será prácticamente igual (aunque podemos usar índices para hacer algunas variaciones si es necesario).

`for_each`: Se usa cuando queremos crear recursos basados en un conjunto o mapa de valores, y cada recurso puede tener configuraciones diferentes, dependiendo de los valores que le pases.


### Ejemplo 1: Función length

Supongamos que tenemos una lista de instancias y necesitamos saber cuántas instancias hemos definido.

```hcl
variable "instancias" {
  type = list(string)
  default = ["ec2-1", "ec2-2", "ec2-3"]
}

output "cantidad_instancias" {
  value = length(var.instancias)
}
```

En este ejemplo:

`length` devuelve la longitud de la lista instancias, que es 3.

### Ejemplo 2: Función join

En el siguiente ejemplo tenemos una lista de nombres de servidores y queremos unirlos en una sola cadena para usarlos, por ejemplo,  en una etiqueta de AWS.

```
variable "servidores" {
  type = list(string)
  default = ["web-01", "db-01", "app-01"]
}

output "servidores_unidos" {
  value = join(", ", var.servidores)
}
```

En este caso:

`join(", ", var.servidores)` une los elementos de la lista servidores con una coma y un espacio entre ellos, dando como resultado `web-01, db-01, app-01`.


### Ejemplo 3: Función lookup

Tenemos un mapa de configuraciones y queremos obtener un valor basado en una clave usando la función lookup.

```
variable "configuracion" {
  type = map(string)
  default = {
    "region" = "us-east-1",
    "tipo"   = "t2.micro"
  }
}

output "region_seleccionada" {
  value = lookup(var.configuracion, "region", "us-west-1")
}
```
En el anterior ejemplo:

Si no se encuentra la clave `region` en el mapa, la función `lookup` devolverá el valor predeterminado `us-west-1`, lo cual ayuda a prevenir errores en la configuración cuando ciertas claves pueden ser opcionales.

### Ejemplo 4: Función upper y lower

Estas funciones son útiles cuando necesitamos cambiar el formato de una cadena.

```
variable "nombre_recurso" {
  type    = string
  default = "miRecurso"
}

output "nombre_en_mayusculas" {
  value = upper(var.nombre_recurso)
}

output "nombre_en_minusculas" {
  value = lower(var.nombre_recurso)
}
```
Aquí:

- `upper(var.nombre_recurso)` convierte `miRecurso` en `MIRECURSO`.

- `lower(var.nombre_recurso)` convierte `miRecurso` en `mirecurso`.

### Ejemplo 5: Función concat

Si tenemos varias listas y queremos combinarlas,  usaremos `concat`.

```
variable "nombres" {
  type    = list(string)
  default = ["web", "db"]
}

variable "instancias" {
  type    = list(string)
  default = ["t2.micro", "t2.medium"]
}

output "nombres_instancias" {
  value = concat(var.nombres, var.instancias)
}
```

Este ejemplo:

`concat(var.nombres, var.instancias)` combina las dos listas nombres e instancias en una sola: `["web", "db", "t2.micro", "t2.medium"]`.

### Ejemplo 6: Función count

Imaginemos que necesitamos crear 3 instancias de máquinas virtuales en AWS (o cualquier otro recurso), pero no queremos repetir el mismo bloque de configuración tres veces. En lugar de eso, usaremos `count` para crear las tres instancias de forma dinámica.

```hcl
resource "aws_instance" "example" {
  count = 3
  ami           = "ami-12345678"
  instance_type = "t2.micro"
}
```
En el ejemplo se crearán 3 instancias de `aws_instance` debido a que se ha configurado `count = 3`

## Resumen

Las funciones en Terraform son herramientas poderosas que permiten manejar datos de manera eficiente. Ya sea que necesites **manipular listas**, **cadenas de texto**, o incluso **crear recursos de manera dinámica**, estas funciones te ayudarán a automatizar y simplificar tu configuración de infraestructura