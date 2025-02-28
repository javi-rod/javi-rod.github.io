---
layout: post
title: Ansible - Ejecución de playbooks
date: 2025-02-28 00:00
share: true
categories: [Ansible,IaC (Infrastructure as Code)]
tags: 
- Ansible
- IaC (Infrastructure as Code)
---

En el post de hoy vamos a ver lo siguiente:
- Comprobar sintáxis del playbook
- Comprobar que las tareas se ejecutarán correctamente
- Ejecutar un playbook
- Opción _--start-at_
- Uso de tags


## **Comprobar sintáxis del playbook**

Antes de ejecutar un playbook, podemos realizar una comprobación de la sintaxis del playbook para detectar errores. Para hacerlo usaremos la opción **`--syntax-check`**

Ejemplo:

`ansible-playbook install_apache.yml --syntax-check`

## **Comprobar que las tareas se ejecutarán correctamente**

Con la opción **check mode**, también conocida como **dry run** nos aseguramos que las tareas se ejecuten correctamente. El playbook se ejecutará normalmente y reportará si hará cambios en las máquinas gestionadas aunque **estos cambios realmente no se harán.**

Ejemplo:

`$ ansible-playbook playbook.yml --check`

Ventajas:

1. **Validación y Pruebas**: Permite validar los cambios antes de aplicarlos, reduciendo el riesgo de errores en entornos de producción.

2. **Auditoría**: Proporciona una visión clara de lo que se cambiará, lo que es útil para la auditoría y la revisión de cambios.

3. **Ahorro de Tiempo**: Permite identificar problemas potenciales sin necesidad de revertir cambios aplicados incorrectamente.

## **Ejecutar un playbook**

Podemos ejecutar un playbook indicando el inventario o no. Recordemos que si no indicamos el inventario, Ansible buscará un archivo de inventario predeterminado en su sistema. Por defecto, este archivo se encuentra en _/etc/ansible/hosts_. Sin embargo, podemos definir la ubicación del inventario predeterminado en el archivo de configuración de Ansible (**ansible.cfg**).

Ejemplo de cómo se vería en el archivo ansible.cfg:

```ini
[defaults]
inventory = /ruta/al/inventario
```
Es importante asegurarse de que este archivo exista y esté correctamente configurado para que Ansible pueda localizar y gestionar los hosts correctamente.

Ejemplo sin especificiar ruta de inventario

`ansible-playbook configure_apache.yml`

Ejemplo usando opción `-i` para especificar ruta de inventario

`ansible-playbook -i hosts configure_apache.yml`

## **Opción --start-at**

La opción `--start-at` es útil para que el playbook se ejecute a partir de una tarea concreta. Las tareas previas se omiten y se empieza a ejecutar desde la tarea indicada.

Para utilizar la opción _--start-at_, simplemente tenemos que especificar el nombre de la tarea desde la cual deseas reanudar la ejecución. 

Ejemplo:

`ansible-playbook playbook.yml --start-at-task="nombre_de_la_tarea"`

En este comando, _playbook.yml_ es el nombre del archivo del playbook y _nombre_de_la_tarea_ es el nombre de la tarea desde la cual deseas empezar.

Veamos un ejemplo más específico. Supongamos que tenemos este playbook:

```yaml
---
- name: Configuración del servidor web
  hosts: webservers
  become: yes

  tasks:
    - name: Instalar Apache
      apt:
        name: apache2
        state: present

    - name: Configurar el puerto de Apache
      lineinfile:
        path: /etc/apache2/ports.conf
        regexp: '^Listen '
        line: "Listen 8080"
      notify:
        - Reiniciar Apache

  handlers:
    - name: Reiniciar Apache
      systemd:
        name: apache2
        state: restarted
```

Si hubo un error en la tarea _"Configurar el puerto de Apache"_ y lo hemos solucionado, podemos reanudar la ejecución desde esa tarea, usando el siguiente comando:

`ansible-playbook playbook.yml --start-at-task="Configurar el puerto de Apache"`

De esta manera, Ansible comenzará la ejecución del playbook desde la tarea _"Configurar el puerto de Apache"_ y continuará con las tareas siguientes.

**Beneficios de --start-at**

* **Ahorro de Tiempo**: No tenemos que volver a ejecutar todas las tareas anteriores,nos ahorra tiempo especialmente en playbooks largos.

* **Eficiencia**: Permite reanudar la ejecución de manera eficiente después de solucionar errores.

* **Flexibilidad**: Ofrece más control sobre la ejecución del playbook.

## **Uso de  tags**

Las tags son etiquetas que puedes asignar a tareas individuales o a roles completos en un playbook. Al ejecutar un playbook, puedes especificar qué tags deseas incluir o excluir, lo que nos da un control granular sobre la ejecución.

Podemos asignar tags a nuestras tareas añadiendo la palabra clave _tags_ seguida de una lista de las etiquetas que desees asignar. 

Ejemplo:

```yaml
---
- name: Configurar servidor web
  hosts: webservers
  become: yes

  tasks:
    - name: Instalar Apache
      apt:
        name: apache2
        state: present
      tags:
        - install
        - apache

    - name: Configurar el puerto de Apache
      lineinfile:
        path: /etc/apache2/ports.conf
        regexp: '^Listen '
        line: "Listen 8080"
      notify:
        - Reiniciar Apache
      tags:
        - configure
        - apache

  handlers:
    - name: Reiniciar Apache
      systemd:
        name: apache2
        state: restarted
      tags:
        - restart
```

**Ejecutar Tareas con Tags Específicas**

Podemos ejecutar tareas específicas utilizando la opción --tags seguida de las etiquetas que deseamos ejecutar. 

Ejemplo para ejecutar las tareas etiquetadas con _install_

`ansible-playbook playbook.yml --tags install`

Ejemplo para ejecutar varias tags (hay que separarlas por comas):

`ansible-playbook playbook.yml --tags install,configure`

**Excluir Tareas con Tags** 

Con las tags también podemos omitir tareas. Para ello usaremos la opción _--skip-tags_.

En el siguiente ejemplo, se ejecutará el playbook omitiendo las tareas etiquetadas con _restart_:

`ansible-playbook playbook.yml --skip-tags restart`


**Beneficios de Usar Tags**

* **Flexibilidad**: Permiten ejecutar solo partes específicas de un playbook, lo que es útil para pruebas y desarrollo.

* **Eficiencia**: Evitan la necesidad de ejecutar todo el playbook, ahorrando tiempo.

* **Mantenimiento**: Facilitan el mantenimiento de playbooks grandes y complejos al permitir una segmentación clara de las tareas.