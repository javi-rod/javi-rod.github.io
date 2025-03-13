---
layout: post
title: Ansible - Variables
date: 2025-03-13 00:00
share: true
categories: [Ansible,IaC (Infrastructure as Code)]
tags: 
- Ansible
- IaC (Infrastructure as Code)
---

En Ansible, las variables se utilizan para almacenar valores que luego podemos reutilizar en nuestros playbooks. Estas variables permiten mantener la configuración más organizada y flexible. Una variable puede definirse de varias maneras, pero aquí tenemos un ejemplo básico:

```yaml
# archivo vars.yml
mi_variable: "Este es el valor de mi variable"
```
Para usar esta variable en un playbook, primero tenemos cargar el archivo de variables (por ejemplo, vars.yml) y luego referenciar la variable utilizando la sintaxis Jinja2 ({{ }}). Así sería un ejemplo sencillo:

```yaml
- hosts: servidores
  vars_files:
    - vars.yml
  tasks:
    - name: Mostrar el valor de mi_variable
      debug:
        msg: "El valor es: {{ mi_variable }}"
```

## Registrar la salida de las tareas ejecutadas usando variables

Para capturar la salida del primer comando y pasarlo al segundo usaremos la directiva `register` con el primer comando, especificando un nombre de variable. La salida de ese comando se almacenará en esa variable. Para usarla posteriormente usaremos el módulo `debug` con la opción `var`.

<img src="https://javi-rod.github.io/assets/images/20250313/01_register_debug.png" alt="Gathering en Ansible"/>

```yaml
---
- name: Check /etc/host file
  hosts: all
  tasks:
  - shell: cat /etc/hosts
    register: result
      
  - debug:
      var: result
```

Aquí tenemos la salida del ejemplo anterior, donde podemos ver el contenido de /etc/hosts de cada nodo además de información como el código de retorno (rc), el tiempo (delta) ,étc.

<img src="https://javi-rod.github.io/assets/images/20250313/02_salida_register_debug.png" alt="Gathering en Ansible"/>

Si 	quisiéramos sacar el rc (return code = 0 existo !=0 fallido) usaríamos var: result.rc

<img src="https://javi-rod.github.io/assets/images/20250313/03_rc.png" alt="Gathering en Ansible"/>

<img src="https://javi-rod.github.io/assets/images/20250313/04_rc_salida.png" alt="Gathering en Ansible"/>


Y si lo único que queremos, es ver el contenido de /etc/hosts sin más, usaremos la salida estándar (stdout)

<img src="https://javi-rod.github.io/assets/images/20250313/05_stdout.png" alt="Gathering en Ansible"/>

<img src="https://javi-rod.github.io/assets/images/20250313/06_stdout_salida.png" alt="Gathering en Ansible"/>

Si dejamos el playbook sin register y sin debug podemos usar la opción -v al ejecutar el playbook

<img src="https://javi-rod.github.io/assets/images/20250313/07_playbook_sin_register_debug.png" alt="Gathering en Ansible"/>

<img src="https://javi-rod.github.io/assets/images/20250313/08_ejecutar_playbook_verbose.png" alt="Gathering en Ansible"/>

## Variables mágicas (Magic Variables)

Son variables especiales que NO requieren una declaración previa porque son provistas automáticamente por el entorno de ejecución de Ansible. Son útiles para obtener  información del entorno de ejecución, inventario o de los playbooks.

Algunas de las más comunes:

* **hostvars**: Permite acceder a las variables de otros hosts en el inventario.

* **group_names**: Lista con los nombres de los grupos a los que pertenece el host actual.

* **groups**: Diccionario con todos los grupos en el inventario, mapeando cada grupo a sus hosts.

* **inventory_hostname**: Nombre del host actual según el inventario.

* **playbook_dir**: Directorio donde se encuentra el Playbook que se está ejecutando.

* **ansible_version**: Versión de Ansible que se está utilizando.

* **ansible_check_mode**: Indica si Ansible se está ejecutando en modo check (comprobación).

Ejemplo:

El siguiente playbook simplemente utiliza la tarea debug para imprimir la información de las Magic Variables. Es una manera útil de ver cómo funcionan y qué datos puedes obtener de ellas.


```yaml
---
- name: Ejemplo de Magic Variables en Ansible
  hosts: all
  tasks:
    - name: Mostrar la versión de Ansible
      debug:
        msg: "La versión de Ansible es {{ ansible_version.full }}"

    - name: Mostrar el nombre del host actual según el inventario
      debug:
        msg: "El nombre del host es {{ inventory_hostname }}"

    - name: Mostrar los grupos a los que pertenece el host actual
      debug:
        msg: "El host pertenece a los siguientes grupos: {{ group_names }}"

    - name: Mostrar las variables de un host específico
      debug:
        msg: "Las variables del host {{ inventory_hostname }} son: {{ hostvars[inventory_hostname] }}"

```

* Utilizamos **ansible_version** para mostrar la versión completa de Ansible.

* Utilizamos **inventory_hostname** para mostrar el nombre del host actual según el inventario.

* Utilizamos **group_names** para listar los grupos a los que pertenece el host actual.

* Utilizamos **hostvars** para acceder a las variables del host específico actual.

El resultado de la ejecución sería el siguiente

<img src="https://javi-rod.github.io/assets/images/20250313/09_variables_magicas.png" alt="Gathering en Ansible"/>

## Jinja2

### Qué es Jinja2

Es un motor de plantillas que permite generar archivos de configuración y scripts personalizados. Podemos usar Jinja2 dentro de playbooks, plantillas y variables. Su extensión es .j2

### Ejemplos

#### 1 - Plantillas Jinja2 para archivos de configuración

Ejemplo de una plantilla de configuración para un archivo de hosts de Nginx. La plantilla podría verse así (nginx.conf.j2):

```
server {
    listen {{ http_port }};
    server_name {{ server_name }};
    
    location / {
        proxy_pass http://{{ proxy_address }};
    }
}
```

Y un Playbook que use esta plantilla:

```yaml
---
- name: Configurar Nginx
  hosts: webservers
  vars:
    http_port: 80
    server_name: "example.com"
    proxy_address: "192.168.1.1"
  tasks:
    - name: Plantilla de configuración para Nginx
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/sites-available/default
      notify:
        - Reiniciar Nginx

  handlers:
    - name: Reiniciar Nginx
      service:
        name: nginx
        state: restarted
```

#### 2 - Uso de Jinja2 en Playbooks

Ejemplo de Jinja2 directamente en los Playbooks para asignar valores a las variables o para condicionales. 

```yaml
---
- name: Ejemplo de uso de Jinja2 en Playbook
  hosts: all
  vars:
    list_of_items: ["item1", "item2", "item3"]
  tasks:
    - name: Mostrar cada ítem en la lista
      debug:
        msg: "{{ item }}"
      with_items: "{{ list_of_items }}"

    - name: Mostrar un mensaje condicional
      debug:
        msg: "El host es un servidor de base de datos"
      when: "'db' in group_names"
```

#### 3 - Filtrado y manipulación de datos

Jinja2 también nos permite usar filtros para manipular datos. Aquí tenemos un ejemplo que muestra cómo filtrar y transformar una lista de elementos:

```yaml
---
- name: Ejemplo de filtros Jinja2
  hosts: all
  vars:
    my_list: [1, 2, 3, 4, 5]
  tasks:
    - name: Convertir lista a cadena de texto
      debug:
        msg: "{{ my_list | join(', ') }}"

    - name: Obtener valores mayores que 3
      debug:
        msg: "{{ my_list | select('greaterthan', 3) | list }}"
```

