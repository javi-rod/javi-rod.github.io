---
layout: post
title: Licenciar VMware vSphere Hypervisor (ESXi)
date:   2022-02-13 09:30
share: true
categories: [VMware]
tags: 
- VMware
---

Después de mucho tiempo, vuelvo a escribir por aquí. Hoy os voy a mostrar como licenciar un host de **vSphere Hypervisor**, pero antes hablaremos de que se trata ese producto.

VMware nos da la posibilidad de tener su famoso Hypervisor ESXi con una versión gratuita, para que podamos usarlo por ejemplo para nuestros laboratorios. Este software de virtualización se trata de un Hypervisor de tipo 1, lo que quiere decir que es un sistema operativo en sí, lo podemos instalar como quién instala un Windows o un Linux en un equipo, al contrario de un tipo 2 que se instalaría como un programa, como por ejemplo los famosos VMware Workstation o Virtual Box.

Para poder instalar este Hypervisor necesitamos cumplir con estos requisitos que están publicados en su página.

**CPU**
Requisitos técnicos mínimos: un zócalo con dos núcleos
Mínimo recomendado: dos zócalos con cuatro o más núcleos por CPU

**Memoria**
Requisitos técnicos mínimos: 4 GB
Mínimo recomendado: 8 GB o más

**Red**
Requisitos técnicos mínimos: un adaptador de red de 1 GbE
Mínimo recomendado: dos adaptadores de red de 1 GbE

**Almacenamiento local (SATA/SAS)**
Requisitos técnicos mínimos: una unidad de 4 GB
Mínimo recomendado: unidades redundantes  

**Almacenamiento compartido**
NFS, iSCSI o Fibre Channel para el almacenamiento de máquinas virtuales

Al tratarse de una versión gratuita, obviamente, conlleva una serie de limitaciones. 

Una de las mayores limitaciones que tiene esta versión con respecto la versión comercial, es que no podemos hacer uso del vCenter, por lo que si tenemos varios Host, debemos administrarlos por separado. Esto nos impide crear un clúster y por tanto no podremos hacer uso por ejemplo de HA (Alta Disponibilidad) entre otras muchas cosas.

Con la versión gratuita tenemos la opción API vStorage desactivada, con lo que no vamos a poder hacer copias de seguridad mediante los programas que hacen uso de dicha API.

Ahora que ya conocemos algo sobre VMware Hypervisor, vamos a dar paso al licenciamiento. 

Cuando se instala por primera vez, el Host está en modo evaluación durante 60 días, lo que quiere decir que en ese tiempo vamos a poder usar el Host sin restricciones como si tuviéramos una licencia Enterprise. Pasado ese tiempo, tendremos la opción de introducir una licencia que hayamos adquirido, o bien usar la gratuita que nos dan cuando lo descargamos.

La forma de licenciar es bastante sencilla como veremos a continuación.

Lo primero que haremos es conectarnos, vía web, a nuestro host ESXi. Una vez allí, en el panel **Navegador**, desplegamos **Host** si no estuviera desplegado y a continuación hacemos clic en **Administrar**

<img src="https://javi-rod.github.io/assets/images/20220213/licencia_ESXi_1.png" alt="Navegador-Host-Administrar" width="300" />

Luego iremos a **Licencias** y haremos clic en **Asignar licencia**

<img src="https://javi-rod.github.io/assets/images/20220213/licencia_ESXi_2.png" alt="Licencias-Asignar licencia" width="600" />

Después introduciremos la clave de licencia y la comprobaremos

<img src="https://javi-rod.github.io/assets/images/20220213/licencia_ESXi_3.png" alt="Clave" width="600" />

Si es correcta, nos lo indiciará como vemos en la imagen y ya podemos asignarla

<img src="https://javi-rod.github.io/assets/images/20220213/licencia_ESXi_4.png" alt="Asignar Clave" width="600" />

Ahora cuando vayamos a licencias ya la veremos asignada y no aparecerá el mensaje Evaluation Mode

<img src="https://javi-rod.github.io/assets/images/20220213/licencia_ESXi_5.png" alt="Clave asignada" width="600" />

Así de sencillo es licenciar el host. Espero que os sea de utilidad y os haya gustado.

