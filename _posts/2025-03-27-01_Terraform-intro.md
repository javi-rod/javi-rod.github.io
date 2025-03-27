---
layout: post
title: Terraform - Introducción
date: 2027-03-27 00:00
share: true
categories: [Terraform,IaC (Infrastructure as Code)]
tags: 
- Terraform
- IaC (Infrastructure as Code)
---

# ¿Qué es Terraform? 

Terraform es una herramienta de infraestructura como código (IaC) desarrollada por HashiCorp que nos permite definir, aprovisionar y gestionar infraestructuras en la nube y otros servicios a través de archivos de configuración. Terraform usa un lenguaje declarativo basado en HCL (HashiCorp Configuration Language), donde describimos el estado deseado de los recursos que necesitamos, como servidores, redes, bases de datos ...

En lugar de gestionar infraestructuras manualmente o a través de interfaces gráficas, Terraform nos permite que los recursos sean definidos en código y gestionados de manera programática. Esta aproximación facilita la automatización, el control de versiones, la coherencia y la reproducibilidad de las infraestructuras a lo largo del tiempo.

# Características Claves de Terraform:

**Infraestructura como código**: Define y gestiona nuestra infraestructura usando archivos de texto que describen el estado de los recursos.

**Proveedores**: Soporta una amplia variedad de proveedores de servicios como AWS, Azure, Google Cloud, y muchos más. Cada proveedor tiene su propio conjunto de recursos que podemos gestionar.

**Automatización y consistencia**: Permite la automatización del proceso de aprovisionamiento de infraestructura, asegurando que los entornos sean consistentes y fáciles de replicar.

**Planificación de cambios (Terraform Plan)**: Permite ver qué cambios se realizarán antes de aplicar cualquier modificación, lo que ayuda a evitar errores y asegurar que solo los cambios esperados sean ejecutados.

**Estado**: Mantiene un archivo de estado que refleja la configuración actual de la infraestructura, asegurando que Terraform sepa qué recursos ya existen y qué cambios son necesarios.

**Modularidad**: Podemos organizar y reutilizar bloques de configuración mediante módulos, facilitando la gestión de infraestructuras complejas.

# Beneficios de Usar Terraform:

**Reproducibilidad**: Al definir la infraestructura en código, facilita su replicación en diferentes entornos (desarrollo, pruebas, producción).

**Gestión de múltiples proveedores**: Con Terraform se puede gestionar recursos en diferentes proveedores de nube y otros servicios, todo desde una misma herramienta.

**Mejora de la colaboración**: Al tratarse de código, Terraform facilita la colaboración en equipos, ya que se puede versionar y revisar como cualquier otro código fuente.

**Control de versiones**: Gracias a su enfoque basado en código, se puede versionar los cambios realizados a la infraestructura, ayudando a mantener un historial claro de modificaciones.

# Diferencias entre Ansible y Terraform

## 1 - Propósito y enfoque:

### **Ansible**:

Se centra en la **automatización de configuraciones** y la **gestión del ciclo de vida de las aplicaciones**. Es ideal para tareas como instalar software, configurar servidores y realizar despliegues.

Utiliza un enfoque basado en procedimientos: se describen pasos específicos para lograr un estado deseado.

### **Terraform**:

Diseñado para la **gestión de infraestructura** como código (IaC). Su enfoque principal es **aprovisionar y definir recursos de infraestructura** (por ejemplo, servidores, redes, bases de datos).

Utiliza un enfoque declarativo, donde se define el estado final deseado y Terraform calcula los pasos necesarios para alcanzarlo.

## 2 - Lenguaje y formato:

### **Ansible**:

Usa **YAML** para escribir "Playbooks". Su sintaxis es sencilla y fácil de entender.

### **Terraform**:

Utiliza **HCL** (HashiCorp Configuration Language), diseñado específicamente para describir infraestructura.

## 3 - Estado:

### **Ansible**:

**No mantiene un archivo de estado**. Ejecuta las tareas descritas en los Playbooks cada vez que se ejecuta, sin verificar el estado previo de los recursos.

### **Terraform**:

**Utiliza un archivo de estado** que almacena información sobre la configuración actual de la infraestructura, ayudando a realizar cambios incrementales y administrar recursos ya existentes.

## 4 - Escenarios de uso:

### **Ansible**:

Ideal para la **gestión de configuraciones y despliegues dentro de sistemas ya provisionados**.

Ejemplo: Configurar Apache en servidores existentes o actualizar paquetes.

### **Terraform**:

Mejor para el **aprovisionamiento de infraestructura desde cero**.

Ejemplo: Crear servidores en AWS, redes en Azure o bases de datos en Google Cloud.

## 5 - Proveedores y ecosistema:

### **Ansible**:

Aunque tiene módulos para interactuar con servicios de nube, está **más orientado al sistema operativo y software**.

### **Terraform**:

Ofrece **soporte nativo y extenso para múltiples proveedores de nube y servicios** (AWS, Azure, GCP, Kubernetes, etc.).