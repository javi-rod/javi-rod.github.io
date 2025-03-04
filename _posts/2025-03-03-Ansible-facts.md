---
layout: post
title: Ansible - facts
date: 2025-03-03 00:00
share: true
categories: [Ansible,IaC (Infrastructure as Code)]
tags: 
- Ansible
- IaC (Infrastructure as Code)
---

Un **fact** en Ansible es una pieza de información o un dato sobre un sistema gestionado (host) que Ansible recopila automáticamente al ejecutar un playbook. Este proceso de recolección, conocido como **gathering** en inglés, se centra en obtener información básica de los nodos gestionados (arquitectura del sistema, versión, detalles de la CPU, etc.), que luego se almacena como variables utilizables en los playbooks para configuraciones dinámicas.

<img src="https://javi-rod.github.io/assets/images/20250303/01_ansible_gathering.png" alt="Gathering en Ansible"/>


Todos esos datos son recopilados por el **módulo setup**. Éste se ejecuta automáticamente cuando ejecutamos un playbook aunque no usemos el módulo en el playbook.

<img src="https://javi-rod.github.io/assets/images/20250303/02_ansible_facts.png" alt="Gathering en Ansible"/>

Los facts se almacenan en la variable **ansible_facts**

<img src="https://javi-rod.github.io/assets/images/20250303/03_ansible_var_ansible_facts.png" alt="Variable ansible_facts"/>

Aquí podemos ver un ejemplo de lo que almacena la variable.

<img src="https://javi-rod.github.io/assets/images/20250303/04_ansible_ejemplo_facts.png" alt="Contenido de ansible_facts"/>

Podemos evitar la recopilación de facts añadiendo la opción **gather_facts** y **no** en el playbook.

<img src="https://javi-rod.github.io/assets/images/20250303/05_ansible_deshabilitar_facts.png" alt="Evitar recopilar facts"/>

Al cambiar la opción de recolección de facts, ahora la variable no contiene datos.

<img src="https://javi-rod.github.io/assets/images/20250303/06_ansible_ejemplo_deshabilitar_facts.png" alt="Resultado de no recolectar facts, la variable ansible_facts está vacía"/>

El comportamiento del gathering facts viene en el fichero de configuración **/etc/ansible/ansible.cfg** en el apartado *gathering*. Por defecto está en **implicit** , lo que quiere decir que Ansible recopilará los facts lo especifiquemos o no en el playbook. Si queremos que no se recopilen, indicaremos **explicit**. El playbook tiene más prioridad que el .cfg

<img src="https://javi-rod.github.io/assets/images/20250303/07_ansible_fact_config.png" alt="En el fichero etc/ansible/ansible.cfg el gathering está implicito, se puede modificar a explicito o bien modificarlo en el playbook.Éste tiene más prioridad que el fichero. "/>