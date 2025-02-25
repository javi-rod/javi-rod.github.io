---
layout: post
title: Ansible - Conexión nodos gestionados
date: 2025-02-27 00:00
share: true
categories: [Ansible,IaC (Infrastructure as Code)]
tags: 
- Ansible
- IaC (Infrastructure as Code)
---

En el contexto de Ansible, un nodo se refiere a cualquier máquina, ya sea un servidor físico, una máquina virtual o incluso un dispositivo de red, que es gestionado por un nodo de control (control node) mediante Ansible.

La conexión a estos nodos se puede definir de dos formas en el inventario, vía SSH (Linux) o WinRM (Windows)

1. **Conexión SSH**

    1.1. Configuración del archivo de inventario: 
    
    El archivo de inventario (hosts) debe listar todos los nodos gestionados y sus direcciones IP o nombres de host.

    ```ini
    [webservers]
    webserver1 ansible_host=192.168.1.10 ansible_user=usuario ansible_ssh_private_key_file=~/.ssh/id_rsa

    [dbservers]
    dbserver1 ansible_host=192.168.1.20 ansible_user=usuario ansible_ssh_private_key_file=~/.ssh/id_rsa
    ```

    1.2. Autenticación SSH:

    Asegúrate de que las claves SSH están configuradas correctamente en los nodos gestionados. La clave pública del nodo de control debe estar presente en ~/.ssh/authorized_keys en cada nodo gestionado.

    1.3. Comprobación de conectividad:

    Puedes verificar la conectividad SSH ejecutando un comando de ping Ansible.

    ```
    ansible all -m ping -i hosts
    ```

2. **Conexión WinRM (Windows Remote Management)**

    Para gestionar nodos Windows, Ansible utiliza WinRM. Los pasos básicos para configurar la conexión WinRM:

    2.1. Configuración del nodo Windows:

    Habilitar WinRM en el nodo Windows.

    ```powershell
    winrm quickconfig
    winrm set winrm/config/service/auth '@{Basic="true"}'
    winrm set winrm/config/service '@{AllowUnencrypted="true"}'
    ```

    2.2. Configuración del archivo de inventario:

    El archivo de inventario debe incluir los detalles de conexión WinRM.

    ```ini
    [windows]
    winserver1 ansible_host=192.168.1.30 ansible_user=usuario ansible_password=contraseña ansible_connection=winrm ansible_winrm_transport=ntlm
    ```
    
    2.3. Instalación de módulos Ansible:

    Asegurarnos de que el módulo pywinrm esté instalado en el nodo de control.

    ```
    pip install pywinrm
    ```

    2.4 Verificación de conectividad:

    Para verificar la conectividad WinRM ejecutaremos un comando de ping Ansible.

    ```
    ansible windows -m win_ping -i 
    ```

En el laboratorio, por simplificar y sencillez, usamos en el inventario la opción ansible_password y la contraseña en plano, justo como la opción de WinRM  del ejemplo anterior. Es una  **opción desaconsejable por razones de seguridad.**


Para evitar hacer uso de la contraseña en plano en el fichero de inventario podemos usar **Ansible Vault**,  **variables de entorno** o **herramientas de gestión de secretos**

1. **Ansible Vault**

    Ansible vault es una herramienta integrada en Ansible usada para cifrar y descifrar archivos con información a proteger, como por ejemplo; contraseñas, claves API, étc.

    Características:

    - Cifrado de archivos y variables: Podemos cifrar archivos enteros o sólo ciertas variables dentro de los ficheros YAML

    - Descifrado  dinámico: Los archivos cifrados se descifran en tiempo de ejecución, sólo cuando es necesario 

    - Protección con contraseña: Los archivos que cifremos estarán protegidos por contraseña, la cual se usará para cifrar y descifrar

    Crear un archivo cifrado
    
    ``` 
    ansible-vault create archivo.yml 
    ```
    
    Cifrar el archivo de inventario:

    ```
    ansible-vault encrypt inventario.ini
    ```
    Descifrar un archivo

    ```
    ansible-vault decrypt archivo.yml
    ```

    Editar un archivo cifrado

    ```
    ansible-vault edit archivo.yml
    ```

    Cambiar contraseña a un archivo cifrado

    ```
    ansible-vault rekey archivo.yml
    ```

    Ejecutar un playbook usando el archivo cifrado:

    ```
    ansible-playbook -i inventario.ini --ask-vault-pass tu_playbook.yml
    ```

    Ejemplo:

    Dado un fichero secrets.yml con una clave API, podemos cifrarlo así:

    ```
    ansible-vault encrypt secrets.yml
    ```
    Si luego queremos usarlo en el playbook, ejecutaremos lo siguiente:

    ```
    ansible-playbook playbook.yml --ask-vault-pass
    ```

2. **Utilizar Variables de Entorno**

    Exportamos una variable de entorno con la contraseña y después la referenciamos en el archivo de inventario.

    Definir variables de entorno:

    ```
    export WIN_PASSWORD=tu_contraseña
    ```

    Archivo de inventario:

    ```ini
    [mis_nodos_windows]
    windows1 ansible_host=192.168.1.3 ansible_user=usuario ansible_password={{ lookup('env', 'WIN_PASSWORD') }} ansible_connection=winrm ansible_winrm_transport=basic
    ```

3. **Herramientas de Gestión de Secretos**

    Otra opción, es usar herramientas de terceros, como HashiCorp Vault, AWS Secrets Manager, o Azure Key Vault para almacenar y recuperar contraseñas de forma segura.

    Ejemplo con HashiCorp Vault y plugin de lookup de Ansible:

    ```ini
    [mis_nodos_windows]
    windows1 ansible_host=192.168.1.3 ansible_user=usuario ansible_password={{ lookup('hashi_vault', 'secret/data/windows1 password') }} ansible_connection=winrm 
    ```
        











