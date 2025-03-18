---
layout: post
title: Ansible - Separación de ficheros
date: 2025-03-18 00:00
share: true
categories: [Ansible,IaC (Infrastructure as Code)]
tags: 
- Ansible
- IaC (Infrastructure as Code)
---

En los proyectos de Ansible, mantener una estructura clara y bien definida es crucial para garantizar la escalabilidad y la facilidad de mantenimiento. Separar los archivos en directorios bien organizados no solo ayuda a localizar rápidamente lo que necesitas, sino que también permite colaborar de manera más eficiente en equipos grandes. 

A continuación, tenemos un ejemplo detallado de cómo organizar un proyecto para maximizar su orden y funcionalidad.

```
├── ansible.cfg
├── inventories/
│   ├── production/
│   │   ├── group_vars/
│   │   ├── host_vars/
│   │   └── inventory
│   ├── staging/
│   │   ├── group_vars/
│   │   ├── host_vars/
│   │   └── inventory
├── playbooks/
│   ├── site.yml
│   ├── webservers.yml
│   └── dbservers.yml
├── roles/
│   ├── common/
│   │   ├── tasks/
│   │   ├── handlers/
│   │   ├── templates/
│   │   ├── files/
│   │   ├── vars/
│   │   ├── defaults/
│   │   ├── meta/
│   │   └── README.md
│   ├── webserver/
│   │   ├── tasks/
│   │   ├── handlers/
│   │   ├── templates/
│   │   ├── files/
│   │   ├── vars/
│   │   ├── defaults/
│   │   ├── meta/
│   │   └── README.md
│   └── dbserver/
│       ├── tasks/
│       ├── handlers/
│       ├── templates/
│       ├── files/
│       ├── vars/
│       ├── defaults/
│       ├── meta/
│       └── README.md
└── README.md
```

`ansible.cfg`: Archivo de configuración de Ansible donde puedes definir parámetros como la ubicación del inventario, la clave SSH, etc.

`inventories/`: Directorio que contiene los inventarios de hosts organizados por entorno (producción, staging, etc.).

`group_vars/` y `host_vars/`: Carpetas donde puedes definir variables específicas para grupos de hosts o hosts individuales.

`playbooks`/: Directorio donde guardas tus playbooks. Los playbooks son archivos YAML que definen las tareas a realizar en los hosts.

`roles/`: Directorio que contiene los roles. Cada rol se organiza en varias subcarpetas.

* `tasks/`: Contiene los archivos YAML que definen las tareas a ejecutar.

* `handlers/`: Tareas que se ejecutan cuando son notificadas por otras tareas.
	
* `templates/`: Archivos de plantilla que pueden contener variables.
	
* `files/`: Archivos estáticos que se copian directamente a los hosts.
	
* `vars/`: Archivos YAML que definen variables utilizadas en las tareas.
	
* `defaults/`: Variables por defecto para el rol.
	
* `meta/`: Información sobre el rol, como dependencias.

`README.md`: Archivo de documentación donde puedes describir el propósito y la estructura de tu proyecto Ansible.

Aquí tenemos otro ejemplo de una estructura básica del proyecto que incluye `group_vars` y `host_vars`:

```
proyecto_ansible/
├── ansible.cfg
├── inventory
├── group_vars/
│   └── webservers.yml
├── host_vars/
│   └── 192.168.1.10.yml
└── site.yml
```

**Archivo de Inventario**

El archivo `inventory` define nuestros grupos de hosts:

```
[webservers]
192.168.1.10
192.168.1.11

[dbservers]
192.168.1.20
192.168.1.21
```

**Variables de Grupo (group_vars)**

Podemos definir variables específicas para el grupo webservers creando un archivo `webservers.yml` dentro del directorio `group_vars`.

```yaml
# Variables para el grupo webservers
apache_port: 8080
document_root: /var/www/html
```

**Variables de Host (host_vars)**

Podemos definir variables específicas para el host 192.168.1.10 creando un archivo `192.168.1.10.yml` dentro del directorio `host_vars`.

```yaml
# Variables para el host 192.168.1.10
apache_port: 9090
document_root: /srv/www
```

**Plantilla (template)**

Vamos a crear una plantilla apache.conf.j2 que utilice las variables definidas en group_vars y host_vars.


```jinja
<VirtualHost *:{{ apache_port }}>
    DocumentRoot {{ document_root }}
    <Directory {{ document_root }}>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

**Playbook**

Por último, vamos a crear un playbook `site.yml` que utilice estas variables.

```yaml
- name: Configurar servidores web
  hosts: webservers
  tasks:
    - name: Instalar Apache
      apt:
        name: apache2
        state: present

    - name: Configurar Apache
      template:
        src: apache.conf.j2
        dest: /etc/apache2/sites-available/000-default.conf

    - name: Reiniciar Apache
      service:
        name: apache2
        state: restarted
```

Cómo Funciona

1. Cuando ejecutemos el playbook, Ansible buscará las variables en `group_vars` primero y luego en `host_vars`. Si una variable está definida en ambos lugares, la definición en host_vars tendrá prioridad.

2. En este caso, el host 192.168.1.10 usará apache_port: 9090 y document_root: /srv/www, mientras que el host 192.168.1.11 usará apache_port: 8080 y document_root: /var/www/html.

Para ejecutar el playbook:

```bash
ansible-playbook -i inventory site.yml
```
Esto aplicará las configuraciones especificadas en el playbook `site.yml` utilizando las variables definidas en `group_vars` y `host_vars`.

