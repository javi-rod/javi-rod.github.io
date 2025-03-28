---
layout: post
title: Terraform - Providers
date: 2025-03-28 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code)]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

Un provider en Terraform es un componente que conecta Terraform con las APIs de los servicios de infraestructura que deseamos gestionar. Estos servicios pueden incluir nubes públicas como AWS o Google Cloud, plataformas SaaS o incluso infraestructura local. A través de los providers, Terraform puede crear, leer, actualizar o eliminar recursos en esos servicios.

Cada provider tiene su propia configuración, ya que depende del servicio con el que se está integrando.

Aquí tenemos un ejemplo donde se configura el provider de AWS, especificando su version mínima y configurando la región en la que se trabajará.


```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = ">= 4.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

```
La opción `version = ">= 4.0"` especifica que Terraform debe usar la versión 4.0 o posterior del provider AWS. Esto asegura que se usen características y mejoras de esa versión o versiones más nuevas, evitando problemas con versiones antiguas que podrían no ser compatibles con el código actual.

Los providers, según quién los mantiene, se pueden organizar en 3 tipos:

- **Oficiales**

	- Desarrollados y mantenidos por HashiCorp o por los proveedores de servicios (Amazon, Microsoft, Google...) en colaboración con HashiCorp
	
	- Ejemplos: AWS, Azure, GCP, Kubernetes..

- **Partners (Socios)**

	- Desarrollados por socios oficiales de HashiCorp que deseas que sus servicios sean compatibles con Terraform.
	
	- Ejemplos: Datadog, VMware, Cisco...

- **Comunidad**

	- Desarrollados y mantenidos por la comunidad de usuarios de Terraform (usuarios independientes, desarrolladores...)
	
	- Ejemplos: GitLab ...

La lista de providers podemos verla en https://registry.terraform.io/ haciendo clic en Browse Providers

<img src="https://javi-rod.github.io/assets/images/20250328/02_registry.png" alt="Registry Terraform" />


<img src="https://javi-rod.github.io/assets/images/20250328/03_providers.png" alt="Providers Terraform" />

Relacionado con el tema de los providers, tenemos el comando `terraform init`. Este comando prepara el entorno de trabajo realizando las siguientes tareas:

* **Inicialización del Directorio de Trabajo**: Configura el directorio de trabajo actual, creando archivos necesarios para manejar el estado y la configuración de los proveedores.

* **Descarga de Proveedores**: Descarga e instala los plugins necesarios para los proveedores especificados en la configuración, como AWS, Azure, Google Cloud, etc.

* **Validación de la Configuración**: Revisa que los archivos de configuración de Terraform estén correctamente estructurados y listos para su uso.

* **Preparación del Backend**: Configura el almacenamiento remoto (si se ha especificado) para mantener el estado de tu infraestructura, lo cual es esencial para la colaboración y el trabajo con equipos.

> [!NOTE]
> El comando terraform init solo necesita ejecutarse una vez al principio o cuando se añaden nuevos proveedores o cambios en el backend. Esto evita que los lectores piensen que es algo que deben ejecutar constantemente.

Aquí vemos un ejemplo de ejecución de terraform init

<img src="https://javi-rod.github.io/assets/images/20250328/04_terraform_init.png" alt="Ejemplo de ejecución del comando terraform init" />

Veamos lo que significa cada parte:

1. **Initializing the backend...** 

    Si hemos configurado un backend (como un almacenamiento remoto para el estado de Terraform, por ejemplo, en S3 o un servidor de estado compartido), Terraform intenta configurarlo. En este caso, no se menciona un backend específico, así que esto podría ser un paso opcional si no estás usando uno.

2. **Initializing provider plugins...** 

    Terraform está buscando el proveedor que hemoss especificado en el archivo de configuración (main.tf). En este caso, está buscando la versión 1.13.3 del proveedor linode/linode, que es un proveedor para interactuar con la API de Linode.


3. **Finding linode/linode versions matching "1.13.3"...** 

    Terraform está buscando la versión especificada del proveedor de Linode en el repositorio de proveedores.


4. **Installing linode/linode v1.13.3...**

    Terraform descarga e instala el proveedor de Linode en la versión especificada (en este caso, v1.13.3).


5. **Installed linode/linode v1.13.3 (signed by a HashiCorp partner, key ID F4E6BBD0EA4FE463)**

    El proveedor se ha instalado correctamente, y Terraform nos indica que ha sido firmado por un socio de HashiCorp (esto ayuda a garantizar la seguridad de los plugins).


6. **Terraform has created a lock file .terraform.lock.hcl to record the provider selections it made above.**

    Terraform ha creado un archivo llamado .terraform.lock.hcl, que guarda información sobre las versiones exactas de los proveedores que se están usando. Esto asegura que, si alguien más ejecuta terraform init en el futuro, obtendrá las mismas versiones de los proveedores, garantizando consistencia.

7. **Terraform has been successfully initialized!**

    Terraform ha completado la inicialización con éxito. Ahora el entorno está listo para ejecutar comandos como terraform plan, terraform apply, etc.


### Multiproviders en Terraform: Gestionar múltiples proveedores de infraestructura

Como hemos mencionado antes, un provider es el responsable de interactuar con las APIs de los servicios en la nube, herramientas de infraestructura, o incluso sistemas locales. Pero, ¿qué pasa cuando necesitamos gestionar recursos en varios proveedores al mismo tiempo? Aquí es donde entra el concepto de multiproviders.

Un **multiprovider** en Terraform permite usar varios proveedores dentro de un solo proyecto o configuración. Esto es especialmente útil cuando trabajemos en entornos multicloud o cuando tengamos que  integrar diferentes servicios de infraestructura en un mismo flujo de trabajo, como crear recursos en AWS, Azure y Google Cloud al mismo tiempo, o gestionar tanto servicios locales como en la nube.

### ¿Cómo funciona un multiprovider en Terraform?

Al trabajar con múltiples proveedores, es necesario configurar cada provider por separado dentro de los archivos de configuración. Para que Terraform sepa qué proveedor usar para cada recurso, podemos especificar el provider dentro de cada bloque de recurso, y también hacer uso de alias para gestionar varios proveedores del mismo tipo.

### Ejemplo de uso de multiproviders

A continuación, tenemos un ejemplo básico de cómo configurar y utilizar múltiples proveedores en Terraform:

```hcl
# Configuración para el primer proveedor: AWS
provider "aws" {
  region = "us-west-2"
}

# Configuración para el segundo proveedor: Google Cloud
provider "google" {
  project = "my-gcp-project"
  region  = "us-central1"
}

# Crear una instancia EC2 en AWS
resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}

# Crear una instancia en Google Cloud
resource "google_compute_instance" "example" {
  name         = "example-instance"
  machine_type = "f1-micro"
  zone         = "us-central1-a"
}

```
En el ejemplo anterior, se  usa AWS y Google Cloud como proveedores. Cada uno tiene su propia configuración dentro del archivo de configuración de Terraform. El bloque **aws_instance** se encargará de crear una máquina virtual en AWS, y el bloque **google_compute_instance** creará una en Google Cloud.

### Uso de Alias para Multiproviders

Como hemos mencionado anteriormente, se puede hacer uso de alias para gestionar varios proveedores. Esto resulta útil cuando tengamos que crear recursos en diferentes regiones de un mismo proveedor. El alias permite dar un nombre a una configuración del provider, facilitando la asignación de recursos a un proveedor específico.

En el siguiente ejemplo, se están configurando dos instancias de AWS, una para la región us-west-2 y otra para la región us-east-1, usando alias para diferenciarlas.

```hcl
# Configuración de AWS con alias
provider "aws" {
  region = "us-west-2"
  alias  = "uswest"
}

provider "aws" {
  region = "us-east-1"
  alias  = "useast"
}

# Instancia en la región us-west-2
resource "aws_instance" "west_instance" {
  provider      = aws.uswest
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}

# Instancia en la región us-east-1
resource "aws_instance" "east_instance" {
  provider      = aws.useast
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}

```

### Ventajas de utilizar multiproviders

* **Gestión centralizada**: Gestionar recursos de diferentes proveedores en un solo archivo de configuración, lo que facilita la administración.

* **Escalabilidad y flexibilidad**: Si en algún momento queremos migrar a un proveedor diferente o añadir más proveedores, hacerlo es fácil, no tenemos que reestructurar tu proyecto.

* **Integración de diferentes servicios**: Permite integrar servicios de diferentes proveedores de manera eficiente, lo que es ideal para arquitecturas híbridas o multicloud.

### Consideraciones al trabajar con multiproviders

Es importante tener en cuenta algunas consideraciones:

* **Estado compartido**: Si usamos múltiples proveedores en un solo proyecto, Terraform gestionará el estado de todos ellos en un solo archivo terraform.tfstate. Esto puede ser un problema si tenemos una infraestructura muy grande, por lo que es recomendable usar backends remotos para almacenar el estado de manera centralizada.

* **Compatibilidad y limitaciones**: No todos los proveedores de Terraform son 100% compatibles entre sí. Algunos pueden tener restricciones específicas al trabajar en conjunto. Es importante revisar la documentación de cada provider para evitar problemas.
