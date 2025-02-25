---
layout: post
title: Ansible - Definición de playbook e inventario
date: 2025-02-26 00:00
share: true
categories: [Ansible,IaC (Infrastructure as Code)]
tags: 
- Ansible
- IaC (Infrastructure as Code)
---

Hasta el  momento hemos visto:

* [Qué es Ansible](https://javi-rod.github.io/2024/09/Ansible-intro/)

* [Montar un laboratorio en VirtualBox](https://javi-rod.github.io/2024/09/Ansible-lab/)

* [Formas de instalar Ansible y qué es el nodo de control](https://javi-rod.github.io/2025/02/Ansible-instalacion/)

Ahora vamos a ver otros dos conceptos que nos van acompañar siempre en Ansible, estos son playbook e inventario. Aunque ya se mencionó el término playbook en el primer post, vamos a profundizar más en el concepto y ver un ejemplo.

**¿Qué es un Playbook de Ansible?**

Un playbook de Ansible es un archivo en formato YAML que describe un conjunto de tareas que queremos automatizar. Los playbooks son la base de Ansible y permiten a los administradores de sistemas definir de manera clara y legible las configuraciones, despliegues y tareas repetitivas que deben ejecutarse en una infraestructura.

Los playbooks de Ansible contienen:

* Hosts: Los sistemas en los que se ejecutarán las tareas. También se les conoce como targets o nodos gestionados.

* Tareas: Acciones que Ansible debe llevar a cabo, como instalar paquetes, copiar archivos o ejecutar comandos.

* Variables: Información dinámica que se puede utilizar en las tareas para personalizarlas.

* Handlers: Tareas especiales que se ejecutan cuando son notificadas por otras tareas, útiles, por ejemplo, para reiniciar servicios después de un cambio.

Un ejemplo simple de un playbook de Ansible podría verse así:

```yaml
---
- hosts: servidores_web
  become: yes
  tasks:
    - name: Instalar Apache
      apt:
        name: apache2
        state: present

    - name: Iniciar el servicio de Apache
      service:
        name: apache2
        state: started
```

Antes de seguir,  vamos a detallar lo que hace este playbook.

**hosts**: Esta línea especifica el grupo de hosts en los que se ejecutarán las tareas definidas en el playbook. En este caso, el playbook se ejecutará en los hosts que pertenecen al grupo servidores_web.

**become**: Este campo indica que Ansible debe obtener privilegios de superusuario (root) para ejecutar las tareas. Es útil cuando se necesitan permisos elevados para realizar ciertas acciones.

**tasks**: Esta sección contiene una lista de tareas que Ansible debe ejecutar en los hosts especificados. Cada tarea se define como un diccionario con varios campos.

**name**: Este campo proporciona una descripción legible de la tarea. En el ejemplo, hay dos tareas:

- Instalar Apache: Esta tarea instalará el paquete apache2 usando el módulo apt.

- Iniciar el servicio de Apache: Esta tarea iniciará el servicio apache2 usando el módulo service.

**apt**: Este es un módulo de Ansible que gestiona paquetes en sistemas basados en Debian (como Ubuntu). Los subcampos name y state especifican el nombre del paquete (apache2) y el estado deseado (present), respectivamente.

**service**: Este es un módulo de Ansible que gestiona servicios en los hosts. Los subcampos name y state especifican el nombre del servicio (apache2) y el estado deseado (started), respectivamente.

En resumen, este playbook de Ansible hará lo siguiente:

- Se ejecutará en los hosts del grupo servidores_web.

- Obtendrá privilegios de superusuario.

- Instalará el paquete apache2 en los hosts.

- Iniciará el servicio apache2.

**¿Qué es un Inventario en Ansible?**

El inventario en Ansible es un archivo que define los hosts y grupos de hosts en los que se ejecutarán los playbooks. Puede ser un archivo estático en formato INI o YAML, o dinámico, generando los hosts sobre la marcha desde una fuente de datos externa.

El inventario permite organizar y categorizar los hosts de manera lógica y fácil de gestionar. Por ejemplo, puedes agrupar servidores web, servidores de bases de datos y servidores de aplicaciones por separado.

Un ejemplo de archivo de inventario en formato INI podría verse así:

```ini
[servidores_web]
web1.example.com
web2.example.com

[servidores_db]
db1.example.com
db2.example.com
```

El mismo ejemplo anterior, pero en formato YAML se ve así:

```yaml
all:
  children:
    servidores_web:
      hosts:
        web1.example.com:
        web2.example.com:
    servidores_db:
      hosts:
        db1.example.com:
        db2.example.com:
```
En este formato, el inventario define una estructura jerárquica utilizando el nivel superior all, que contiene los grupos de hosts servidores_web y servidores_db, cada uno con sus respectivos hosts.