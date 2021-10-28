---
layout: post
title: Modelo de responsabilidad compartida en AWS
date:  2021-10-28 09:30
share: true
categories: [Cloud,AWS]
tags: 
- Cloud
- AWS
---

El modelo de responsabilidad compartida consiste en que el cliente y el proveedor, son responsables de algunas partes del entorno.

Las responsabilidades del cliente se conocen como **"seguridad en la nube"**, mientras que las responsabilidades del proveedor,en este caso AWS, se conocen como **"seguridad de la nube"**.

### **Clientes: seguridad en la nube**

Como clientes, somos responsables de la seguridad de todo lo que creemos y coloquemos en la nube de AWS. Al usar los servicios de AWS debemos administrar la seguridad del contenido, tanto lo que vayamos almacenar como de los servicios y accesos a ese contenido. También controlaremos cómo se conceden, administran y revocan esos accesos.

Seremos responsables de seleccionar y configurar los Sistemas Operativos que ejecutemos en las instancias EC2, lo que implica parchear esos Sistemas, además de configurar los grupos de seguridad y administrar las cuentas de usuario.

### **AWS: seguridad de la nube**

La seguridad de la nube recae en el proveedor del cloud, AWS.

AWS se encarga de operar, administrar y controlar todas las capas de la infraestructura (Sistema operativo host, virtualización, seguridad física en los centros de datos...)

AWS también es el responsable de proteger las regiones, las zonas disponibilidad así como las ubicaciones de borde.

<br>

<img src="https://javi-rod.github.io/assets/images/20211028/Esquema-responsabilidad-compartida.jpg" alt="Esquema responsabilidad compartida" width="600" />
<figcaption>Esquema modelo de responsabilidad compartida en AWS.</figcaption>
