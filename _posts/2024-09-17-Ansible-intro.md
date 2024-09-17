---
layout: post
title: Ansible - Introducción
date:   2024-09-17 09:00
share: true
categories: [Ansible]
tags: 
- Ansible
---

# ¿Qué es Ansible? 

Ansible es un poderosa herramienta open source de automatización, que fue desarrollada por Michael DeHaan en el año 2012. Posteriormente, en el 2015, Red Hat adquiría Ansible.

La palabra ansible es un término que aparece en una de las novelas de Ursula K. Le Guin refiriéndose a sistemas de comunicación instantáneos.

Con Ansible podemos automatizar el aprovisionamiento, la gestión de la configuración, el despliegue de aplicaciones, entre otras cosas.

Usar esta herramienta nos aporta, entre otros, los beneficios siguientes:

+ Dejamos atrás tareas repetitivas simplificando los flujos de trabajo.

+ Administramos y mantenemos la configuración de los sistemas de forma más sencilla.

+ Permite que despleguemos software complejo.

+ No hay tiempo de inactividad con las actualizaciones continuas.

Una ventaja de Ansible es que no requiere agentes en los equipos dónde necesitemos actuar. Se basa en conexiones SSH o WinRM.

Para realizar las automatizaciones, se hace uso de ficheros YAML que se denominan playbooks.

Destacar que se trata de un sistema idempotente. Esto quiere decir que cuando ejecutemos un playbook y los cambios solicitados en éste ya estén presentes, no se realizan cambios.

<img src="https://javi-rod.github.io/assets/images/20240917/Ansible_logo.jpeg" alt="Ansible Logo" width="75" />