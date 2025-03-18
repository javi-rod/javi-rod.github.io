---
layout: post
title: Ansible - Roles
date: 2025-03-18 00:00
share: true
categories: [Ansible,IaC (Infrastructure as Code)]
tags: 
- Ansible
- IaC (Infrastructure as Code)
---

En Ansible, los roles son una herramienta poderosa para organizar y reutilizar configuraciones. Un rol agrupa tareas, plantillas, archivos y variables necesarias para llevar a cabo una función específica. Esto no solo simplifica el desarrollo, sino que también facilita la escalabilidad y el mantenimiento del proyecto, ya que permite aplicar configuraciones de manera consistente en múltiples entornos. 

Supongamos que tenemos que configurar un servidor web. Podemos crear un rol llamado `webserver` que incluya:

1. **Tareas (tasks)**: Las acciones que se ejecutarán, como instalar Apache, configurar archivos, etc.

2. **Archivos (files)**: Archivos estáticos que necesitas copiar al servidor.

3. **Plantillas (templates)**: Archivos de configuración dinámicos que se llenarán con variables.

4. **Variables (vars)**: Valores que podemos usar en las tareas y plantillas.

Aquí tenemos un esquema básico de un rol `webserver`:

```
webserver/
├── tasks/
│   └── main.yml  # Aquí defines tus tareas
├── files/
│   └── index.html  # Archivo estático que se copiará
├── templates/
│   └── httpd.conf.j2  # Plantilla para configuración de Apache
├── vars/
│   └── main.yml  # Variables que usarás en el rol
└── meta/
    └── main.yml  # Información sobre el rol
```    

Este es el aspecto del fichero `main.yml`

```yaml
---
- name: Instalar Apache
  apt:
    name: apache2
    state: present

- name: Copiar archivo index.html
  copy:
    src: index.html
    dest: /var/www/html/index.html

- name: Aplicar configuración desde plantilla
  template:
    src: httpd.conf.j2
    dest: /etc/apache2/apache2.conf
```
    
Cuando tengamos que configurar otro servidor web, solo tenemos que llamar al rol webserver en el playbook.

```yaml
---
- hosts: webservers
  roles:
    - webserver
```