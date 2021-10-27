---
layout: post
title:  "Infraestructura global de AWS"
date:   2021-10-27 09:30
categories: [Cloud,AWS]
tags: 
- Cloud
- AWS
---

En el post de hoy vamos hablar la Infraestructura de global de AWS formada por Regiones, Zonas de disponibilidad (AZ - availability zones), Ubicaciones de borde (EDGE locations), Zonas locales (Local Zones), AWS Wavelength y AWS Outposts

### **Regiones**

Una Región consiste en una zona geográfica en la cual se disponen centros de datos agrupados. AWS crea regiones para estar cerca de donde el tráfico empresarial lo exige.

Dentro de cada una de las regiones se cuentan con varios centros de datos. Las regiones pueden conectarse entre si mediante una fibra de alta velocidad administrada por AWS.

Cada región se encuentra separada de todas las demás, ningún dato entra o sale de nuestro entorno en esa región sin que nosotros concedamos explícitamente permiso para que se muevan esos datos. 

A la hora de seleccionar una región, debemos tener en cuenta los 4 factores siguientes:

- **Conformidad con los requisitos legales y de gobernanza de datos**

En función del ámbito de la empresa, así como de la ubicación, es posible que los datos no puedan salir del área para cumplir con los requisitos de conformidad y residencia de datos. Si esto sucede podemos seleccionar la región de AWS más cercana a la ubicación.

- **Proximidad con los clientes**

Eligir una región próxima ayudará a que la entrega de contenido se realice más rápido al haber menor latencia.

- **Servicios disponibles dentro de una región**

En ciertas ocasiones, puede darse el caso que la región no tenga todas las características que necesitemos. AWS suele crear nuevos servicios y ampliar características que requieren de un Hardware que no está disponible en todas las regiones.

- **Precios**

El precio es otro factor a tener en cuenta. Hay diferencia entre ejecutar aplicaciones en una u otra región ya que varía el coste.

<center>
    <img src="https://javi-rod.github.io/assets/images/20211027/Mapa_Regiones_AWS.png" alt="Regiones AWS" width="800" />
    <figcaption>Mapa de Regiones de AWS. Fuente:AWS.</figcaption>
</center>




### **Zonas de disponibilidad (AZ - availability zones)**

Dentro de la región, tenemos una o varias zonas de disponibilidad. Una zona de disponibilidad no es más que un centro de datos único o un grupo de centros de datos. Esos centros de datos, están lo suficientemente cerca como para que haya una baja latencia entra las zonas de disponibilidad pero lo suficientemente lejos como para que en caso de desastre no se vean afectadas varias zonas de disponibilidad.

### **Ubicaciones de borde (EDGE locations)**

Las ubicaciones de borde y las regiones se encuentran separadas. Podemos mandar contenido desde dentro de una región a un conjunto de ubicaciones repartidas por el mundo, con el objeto de acelerar la comunicación y la entrega de contenido.

Las ubicaciones de borde de AWS ejecutan CloudFront y Amazon Route 53.

Amazon CloudFront es un servicio que ayuda a entregar datos, video, aplicaciones y API a los clientes en todo el mundo con baja latencia y altas velocidades de transferencia. Mientras que Amazon Route 53 es un servicio de DNS.

Resumiendo, una ubicación de borde es un sitio que Amazon CloudFront utiliza a fin de almacenar copias en caché de su contenido más cerca de los clientes para una entrega más rápida.

### **Zonas locales de AWS (AWS Local Zones)**

Las zonas locales de AWS son una extensión de una región de AWS donde se ejecutan las aplicaciones sensibles a la latencia. Ponen el cómputo,almacenamiento, las bases de datos y otros servicios de AWS más cerca del usuario final.

Con las zonas locales de AWS, puede ejecutar fácilmente aplicaciones sensibles que requieren latencias de milisegundos de un solo dígito para sus usuarios finales, como la creación de contenido multimedia y de entretenimiento, juegos en tiempo real, simulaciones de depósitos, automatización de diseño electrónico y aprendizaje automático.

### **AWS Wavelength**

AWS Wavelength consiste en una infraestructura de AWS orientada a la optimización de aplicaciones móviles de informática en el borde.

### **AWS Outposts**

Se trata de un bastidor ubicado en cualquier centro de datos, ya esa en una coubicación o en instalación local. Podemos utilizar las mismas API, herramientas e infraestructura de AWS en las instalaciones y en la nube de AWS ofreciendo una experiencia híbrida. 
