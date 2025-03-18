---
layout: post
title: Ansible - Flujos playbook
date: 2025-03-18 00:00
share: true
categories: [Ansible,IaC (Infrastructure as Code)]
tags: 
- Ansible
- IaC (Infrastructure as Code)
---

Los flujos dentro de los playbooks de Ansible son fundamentales para organizar y secuenciar las tareas necesarias para alcanzar un estado deseado en los sistemas gestionados. Estas secuencias no solo permiten estructurar los pasos necesarios, sino que también permiten orquestar pasos que son dependientes de otros, manejar condiciones, ejecutar tareas en paralelo y garantizar que los resultados sean predecibles,mediante bucles, condicionales, bloques o manejo de errores.

En el post vamos a ver lo siguiente:

* [Bucles](#bucles)

* [Condicionales](#condicionales)

* [Bloques](#bloques)

* [Manejo de errores](#manejo-de-errores)

## Bucles

Los bucles o loops,  son útiles para ejecutar tareas repetitivas sobre un conjunto de elementos, como archivos, usuarios, paquetes o cualquier lista definida.

Ejemplos:

### - Uso de whit_items

En el siguiente ejemplo, la tarea apt se ejecuta por cada elemento en la lista `with_items`, instalando cada uno de los paquetes.

```yaml
---
- name: Ejemplo de uso de with_items
  hosts: all
  tasks:
    - name: Instalar varios paquetes
      apt:
        name: "{{ item }}"
        state: present
      with_items:
        - git
        - vim
        - htop

```

### - Uso de loop

A partir de la v2.5 se recomienda usar `loop` en lugar de `with_items`. 

Las ventajas de usar loop:

* Simplicidad y consistencia
* Compatibilidad con nuevas funcionalidades
* Flexibilidad en datos estructurados
* Desambiguación de Múltiples Iteradores

A continuación el mismo ejemplo anterior pero con loop.

```yaml
---
- name: Ejemplo de uso de loop
  hosts: all
  tasks:
    - name: Instalar varios paquetes
      apt:
        name: "{{ item }}"
        state: present
      loop:
        - git
        - vim
        - htop

```

### - Anidar loops

La anidación de loops está permitida y se usa para iterar sobre listas de listas.

```yaml
---
- name: Ejemplo de anidación de loops
  hosts: all
  tasks:
    - name: Crear usuarios y asignarles grupos
      user:
        name: "{{ item.0 }}"
        groups: "{{ item.1 }}"
        state: present
      loop:
        - [ 'user1', 'group1' ]
        - [ 'user2', 'group2' ]
        - [ 'user3', 'group3' ]

```

### - Loops con Diccionarios

Los loops también se pueden usar sobre diccionarios.

En el siguiente ejemplo, se usa `dict2items`. Se trata de un filtro muy útil en Ansible para transformar un diccionario en una lista de elementos clave-valor. Este formato resultante es mucho más fácil de iterar en un bucle, ya que cada entrada se convierte en un objeto con atributos key y value

```yaml
---
- name: Ejemplo de loops con diccionarios
  hosts: all
  vars:
    users:
      user1: "group1"
      user2: "group2"
      user3: "group3"
  tasks:
    - name: Crear usuarios y asignarles grupos desde diccionario
      user:
        name: "{{ item.key }}"
        groups: "{{ item.value }}"
        state: present
      loop: "{{ users | dict2items }}"

```

Usar dict2items es especialmente útil cuando trabajamos con diccionarios dinámicos o provenientes de datos externos, ya que facilita la iteración sobre pares clave-valor sin necesidad de transformaciones manuales adicionales.

### - Condicionales en Loops

Se pueden combinar loops con condicionales para que se ejecuten tareas cuando una condición se cumpla.

En el ejemplo siguiente, los paquetes se instalan sólo si el sistema operativo pertenece a la familia Debian.

```yaml
---
- name: Ejemplo de loops con condiciones
  hosts: all
  tasks:
    - name: Instalar paquetes si no están ya instalados
      apt:
        name: "{{ item }}"
        state: present
      loop:
        - git
        - vim
        - htop
      when: ansible_facts['os_family'] == "Debian"

```

## Condicionales

Los condicionales permiten ejecutar tareas en función del cumplimiento de ciertas condiciones, haciendo que los playbooks sean más dinámicos y adaptables. Usan la directiva `when`, que evalúa expresiones basadas en variables, hechos (facts) recopilados por Ansible o cualquier lógica personalizada. Esto es especialmente útil para realizar tareas específicas solo en ciertos entornos, sistemas operativos o configuraciones.

### - when

Es la más común. Se usa para ejecutar una tarea solo si una condición es **verdadera**

En el ejemplo siguiente, la tarea solo se ejecuta si el Sistema Operativo de destino pertenece a la familia Debian.

```yaml
- name: Instalar Apache en servidores Debian
  apt:
    name: apache2
    state: present
  when: ansible_os_family == 'Debian'

```

### - with items

Como vimos en el apartado de bucles, se puede usar condiciones con bucles.

En este ejemplo, se crearán los usuarios solo si su estado es 'present'.

```yaml
- name: Crear usuarios
  user:
    name: "{{ item.name }}"
    state: present
  when: item.state == 'present'
  with_items:
    - { name: 'john', state: 'present' }
    - { name: 'doe', state: 'absent' }

```

**NOTA**: Se puede sustituir with_items por loop

```yaml
- name: Crear usuarios
  user:
    name: "{{ item.name }}"
    state: present
  when: item.state == 'present'
  loop:
    - { name: 'john', state: 'present' }
    - { name: 'doe', state: 'absent' }

```

### - Operadores lógicos y de comparación

Podemos usar operadores lógicos para combinar múltiples condiciones.

El siguiente ejemplo, apache se reiniciará solo si el SO es de la familia Debian y la versión es la 10.

```yaml
- name: Reiniciar el servicio si es necesario
  service:
    name: apache2
    state: restarted
  when: ansible_os_family == 'Debian' and ansible_distribution_version == '10'

```
#### Operadores Lógicos en Condicionales

`and` Combina dos o más condiciones y evalúa como **verdadero** solo si todas las condiciones se cumplen. Ejemplo:

```yaml
when: ansible_os_family == 'Debian' and ansible_distribution_version == '10'
```

`or` Evalúa como verdadero si al menos una de las condiciones se cumple. Ejemplo:

```yaml
when: ansible_os_family == 'Debian' or ansible_os_family == 'RedHat'
```

`not` Invierte el resultado de una condición, evaluándola como falsa si era verdadera y viceversa. Ejemplo:

```yaml
when: not ansible_os_family == 'Windows'
```

`Paréntesis ()` Utilizados para agrupar condiciones y controlar el orden de evaluación. Esto es útil en expresiones complejas. Ejemplo:

```yaml
when: (ansible_os_family == 'Debian' and ansible_distribution_version == '10') or ansible_os_family == 'RedHat'
```

#### Operadores de Comparación

Además de los operadores lógicos, Ansible también permite utilizar operadores de comparación dentro de las condiciones:

`==` Igual a

```yaml
when: ansible_os_family == 'Debian'
```

`!=` Diferente de

```yaml
when: ansible_os_family != 'Windows'
```

`> / <` Mayor o menor que (útil para versiones numéricas)

```yaml
when: ansible_distribution_version | int > 10
```

`>= / <=` Mayor o igual / menor o igual que

```yaml
when: ansible_distribution_version | int >= 10
```


### - Registrar variables

En algunas ocasiones puede que necesitemos usar la salida de una tarea previa como una condición.

En el ejemplo siguiente, la segunda tarea solo se ejecutará si la salida de la primera es diferente de active.

```yaml
- name: Obtener el estado del servicio
  shell: systemctl is-active apache2
  register: apache_status

- name: Reiniciar Apache si está inactivo
  service:
    name: apache2
    state: restarted
  when: apache_status.stdout != 'active'

```

El atributo `register` permite almacenar la salida de una tarea como una variable en el contexto del playbook. Esta varible contiene:

* `stdout`: La salida estándar del comando ejecutado

* `stderr`: La salida de errores, si los hay.

* `rc`: El código de retorno del comando. Útil para saber si una tarea se completó con éxito o con fallo.

En nuestro ejemplo anterior:

`apache_status.stdout` contiene la respuesta del comando *systemctl is-active apache2*, que debería ser 'active', 'inactive', etc.

La condición `when: apache_status.stdout != 'active'` evalúa si el servicio no está activo y toma acción en consecuencia.

## Bloques

Los bloques o blocks se usan para agrupar tareas y manejar errores de manera más eficiente.

Un bloque puede contener tres secciones:

`block`: En donde se definen las tareas

`rescue`: Es una sección opcional. En ésta se definen las tareas a ejecutar si alguna tarea del bloque principal falla

`always`: Es otra sección opcional en la cual se definen las tareas que siempre deben ejecutarse, independientemente del éxito o fallo de las tareas del bloque principal o de rescate.

A continuación un ejemplo básico donde se ven las 3 secciones.


```yaml
- name: Ejemplo de uso de blocks
  hosts: localhost
  tasks:
    - name: Tarea que puede fallar
      block:
        - name: Crear un archivo
          file:
            path: /tmp/ejemplo.txt
            state: touch

        - name: Ejecutar comando
          command: /bin/false

      rescue:
        - name: Notificar sobre el error
          debug:
            msg: "La tarea falló, ejecutando acciones de rescate."

      always:
        - name: Limpiar recursos
          file:
            path: /tmp/ejemplo.txt
            state: absent

```




## Manejo de errores

El manejo de errores es una parte esencial en la automatización con Ansible, ya que permite controlar cómo deben reaccionar los playbooks frente a fallos inesperados. Con herramientas como `ignore_errors`, `failed_when`, bloques `rescue` y `always`, entre otras, es posible personalizar la ejecución, garantizar la continuidad o realizar acciones correctivas para minimizar interrupciones y asegurar que las tareas críticas se cumplan de forma eficiente.

`ignore_errors`

El módulo ignore_errors te permite continuar la ejecución de un playbook incluso si una tarea falla. Esto puede ser útil cuando un fallo en una tarea no debería detener todo el playbook.

```yaml
- name: This task will ignore any errors
  command: /bin/false
  ignore_errors: yes
```

`failed_when`

El módulo failed_when te permite definir condiciones personalizadas para determinar cuándo una tarea debería considerarse fallida.

```yaml
- name: This task will fail if the output does not contain 'SUCCESS'
  command: /bin/true
  register: result
  failed_when: "'SUCCESS' not in result.stdout"
```

`rescue` y `always`

Ansible 2.0 introdujo los bloques block, rescue y always para manejar errores. Estos bloques permiten definir una lógica de recuperación cuando ocurren errores.

```yaml
- name: Attempt a command that might fail
  block:
    - command: /bin/false

  rescue:
    - debug: msg="There was an error, but we recovered"

  always:
    - debug: msg="This task always runs"
```

`handlers` y `notify`

Utiliza handlers y notify para manejar errores y notificaciones. Los handlers son tareas especiales que solo se ejecutan cuando son notificadas por otra tarea.

En el siguiente ejemplo, en la tarea principal, el módulo apt se utiliza para instalar el paquete nginx, y con la clave notify se configura que, tras la instalación, se active un handler llamado restart nginx. Este handler, definido más adelante en el playbook, emplea el módulo service para reiniciar el servicio nginx.

```yaml
- name: Install a package
  apt:
    name: nginx
    state: present
  notify: restart nginx

handlers:
  - name: restart nginx
    service:
      name: nginx
      state: restarted
```

`until` y `retries`

El módulo until se utiliza para repetir una tarea hasta que se cumpla una condición específica, mientras que retries y delay controlan cuántas veces se intenta la tarea y cuánto tiempo se espera entre cada intento, respectivamente.

```yaml
- name: Wait for the service to be available
  command: curl http://localhost:8080
  register: result
  until: result.rc == 0
  retries: 5
  delay: 10
```

`run_once`

El módulo run_once garantiza que una tarea se ejecute solo una vez, sin importar cuántos hosts estén en juego. Esto es útil para evitar errores cuando solo necesitas que una acción se realice una vez.

```yaml
- name: Run this task only once
  command: /bin/true
  run_once: true
```

`max_fail_percentage`

Con el módulo max_fail_percentage, puedes definir un umbral de porcentaje de fallos permitido. Si se excede este porcentaje, el playbook se detendrá.

```yaml
- hosts: all
  max_fail_percentage: 10
  tasks:
    - name: This task might fail
      command: /bin/false
```

`any_errors_fatal`

El módulo any_errors_fatal detiene la ejecución en todos los hosts si un error ocurre en cualquier host.

```yaml
- hosts: all
  any_errors_fatal: true
  tasks:
    - name: This task will cause all hosts to stop if it fails
      command: /bin/false
```

`notify` con múltiples handlers

Puedes notificar múltiples handlers desde una misma tarea, lo que te permite manejar errores de manera más flexible y organizada.

En el ejemplo siguiente, la tarea principal utiliza el módulo apt para instalar el paquete nginx y, una vez completada, notifica a dos handlers: *restart nginx* y *notify admin*. El primer handler reinicia el servicio nginx utilizando el módulo service, mientras que el segundo envía un correo electrónico al administrador (admin@example.com) para informar que el servicio ha sido reiniciado.


```yaml
- name: Install a package
  apt:
    name: nginx
    state: present
  notify:
    - restart nginx
    - notify admin

handlers:
  - name: restart nginx
    service:
      name: nginx
      state: restarted
  - name: notify admin
    mail:
      to: "admin@example.com"
      subject: "nginx was restarted"

```

