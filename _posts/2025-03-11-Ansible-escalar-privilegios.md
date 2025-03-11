---
layout: post
title: Ansible - Escalar privilegios
date: 2025-03-11 00:00
share: true
categories: [Ansible,IaC (Infrastructure as Code)]
tags: 
- Ansible
- IaC (Infrastructure as Code)
---

En este post vamos a ver, lo sencillo que es escalar privilegios. Sólo debemos usar el parámetro **become**.

Este parámetro puede ir en:

| **Nivel**             | **Descripción**                       |
|-----------------------|---------------------------------------|
| Línea de comando      | Especificado con `--become`          |
| Playbook              | Configurado con `become: yes`        |
| Inventario            | Usando `ansible_become=yes`          |
| Fichero de configuración | Definido globalmente en `ansible.cfg` |


En caso de coincidencias, la precedencia es la siguiente:

*línea de comando > playbook > inventario > fichero de configuración*

Por ejemplo, si especificamos *become_user: admin* en un playbook pero usamos *--become-user=root* en la línea de comandos, la línea de comandos tendrá prioridad y se usará root.

Cuando se especifica `become: yes` **por defecto se usa sudo**. Si queremos usar otro método (pfexec, doas, ksu, runas) debemos añadir `become_method`.

En caso de querer usar otro usuario, podemos especificarlo con `become_user`.

En algunas situaciones, es necesario proporcionar una contraseña para obtener privilegios elevados. En Ansible, podemos especificar la contraseña directamente desde la línea de comandos utilizando la opción `--ask-become-pass`

Aquí un ejemplo de uso. Crear un fichero llamado *ejemplo.txt* en el directorio */root/* teniendo privilegios de root.

```yaml
---
- hosts: all
  become: yes
  tasks:
    - name: Crear un archivo como root
      file:
        path: /root/ejemplo.txt
        state: touch

```