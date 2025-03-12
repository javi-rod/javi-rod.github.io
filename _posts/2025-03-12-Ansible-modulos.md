---
layout: post
title: Ansible - Módulos
date: 2025-03-11 00:00
share: true
categories: [Ansible,IaC (Infrastructure as Code)]
tags: 
- Ansible
- IaC (Infrastructure as Code)
---

## Qué es un módulo en Ansible

Un módulo en Ansible es una pieza de código que ejecuta una tarea específica en los nodos administrados. Estos módulos son el núcleo de Ansible y permiten realizar acciones como gestionar archivos, instalar paquetes, configurar servicios o ejecutar comandos. En resumen, son bloques de construcción que podemos usar dentro de playbooks o como comandos ad-hoc. Además, Ansible incluye una amplia biblioteca de módulos predefinidos, pero también podemos crear los nuestros personalizados si necesitamos funcionalidades más específicas.

En este post vamos a ver los módulos dedicados a:

* [Paquetes](#paquetes)

* [Servicios](#servicios)

* [Reglas Firewall](#reglas-firewall)

* [Contenido de archivo](#contenido-de-archivo)

* [Archivado](#archivado)

* [Tareas Programadas](#tareas-programadas)

* [Usuarios y grupos](#usuarios-y-grupos)

## Paquetes

En esta sección, veremos cómo gestionar paquetes en sistemas basados en Red Hat usando el módulo `yum` de Ansible. Este módulo es ideal para automatizar tareas comunes relacionadas con la instalación, desinstalación y actualización de paquetes. 

Cuando hagamos uso de este módulo vamos a tener los siguientes estados (state):

* **present**: Asegura que el paquete está **presente o instalado** en el sistema.

* **absent**: Asegura que el paquete está **ausente o desinstalado** del sistema

* **latest**: Asegura que el paquete está **instalado y actualizado** a la última versión disponible.

* **removed**: Asegura que el paquete esté **desinstalado**.

<u>Recomendación</u>: Usar absent en lugar de removed, ya que está más aceptado y entendido por los módulos de Ansible.

A continuación, se presentan ejemplos detallados que ilustran diferentes estados, así como escenarios específicos como la instalación de un archivo RPM, la eliminación de versiones anteriores de paquetes, y la instalación de paquetes específicos.

NOTA: Aunque los ejemplos de esta sección se centran en el uso del módulo `yum` para distribuciones basadas en Red Hat, es importante mencionar que en sistemas basados en Debian, como Ubuntu, se utiliza el gestor de paquetes `apt`. Este cuenta con su propio módulo en Ansible llamado apt, que permite realizar operaciones similares como instalar, actualizar o eliminar paquetes.

1. Instalar el paquete httpd en web1 usando el módulo yum.

    ```yaml
    ---
    - name: Install httpd package
      hosts: web1
      become: yes
      tasks:
        - name: Install httpd
          yum:
            name: httpd
            state: present
    ```

2. Instalar el rpm (https://mirror.stream.centos.org/9-stream/AppStream/x86_64/os/Packages/wget-1.21.1-8.el9.x86_64.rpm) en web1 usando el módulo yum

    ```yaml
    ---
    - name: Download and install package
      hosts: web1
      become: yes
      tasks:
        - name: Download the package
          get_url:
            url: "https://mirror.stream.centos.org/9-stream/AppStream/x86_64/os/Packages/wget-1.21.1-8.el9.x86_64.rpm"
            dest: "/tmp/wget-1.21.1-8.el9.x86_64.rpm"

        - name: Install the package
          yum:
            name: "/tmp/wget-1.21.1-8.el9.x86_64.rpm"
            state: present

        - name: Remove the package file
          file:
            path: "/tmp/wget-1.21.1-8.el9.x86_64.rpm"
            state: absent
    ```

3. Instalar el paquete unzip-5.52 en web1.

    ```yaml
    ---
    - hosts: web1
      tasks:
        - name: Install unzip package
          yum:
            name: unzip-5.52
            state: present

    ```
4. Instalar la última versión de iotop.

    ```yaml
    ---
    - hosts: all
      tasks:
        - name: Install iotop package
          yum:
            name: iotop
            state: latest      
    ```
5. Instalar en web1 la última versión de sudo y poner la version vsftpd 3.0.3, ya está presente vsftpd v3.0.5 que deseamos eliminar.

    ```yaml
    ---
    - name: Install multi-pkgs
      hosts: web1
      become: yes
      tasks:
        - name: Install sudo latest
          yum:
            name: sudo
            state: latest
        - name: Install vsftpd v3.0.3
          yum:
            name: vsftpd-3.0.3
            state: present
            allow_downgrade: yes    
     ```

## Servicios

En este punto, vamos a ver los estados disponibles para gestionar servicios en Ansible utilizando los módulos `service` y `systemd`. Estos módulos permiten controlar servicios de manera eficaz, automatizando tareas comunes como iniciar, detener, reiniciar o habilitar servicios en sistemas Linux.

Para el **módulo service**, tendremos estos estados:

* **started:** Inicia el servicio si no está ya en ejecución.

* **stopped**: Detiene el servicio si está en ejecución.

* **restarted**: Reinicia el servicio, primero lo detiene y luego lo inicia.

* **reloaded**: Recarga la configuración del servicio sin detenerlo.

En el caso del módulo **systemd**, además de los anteriores, tenemos estos estados adicionales:

* **enabled**: Habilita el servicio para que se inicie en el arranque del sistema

* **disabled**: Deshabilita el servicio para que no se inicie automáticamente en el arranque del sistema

* **masked**: Se usa para impedir que un servicio sea iniciado, tanto manualmente como por dependencias. Cuando un servicio está enmascarado (masked), systemd crea un enlace simbólico (symlink) que apunta a /dev/null, esencialmente bloqueando cualquier intento de iniciar el servicio.

* **unmasked**: Quita la máscara del servicio, permitiendo que sea iniciado manualmente o por dependencias

* **static**: Indica que el servicio no tiene archivos de unidades habilitadas (generalmente definido por systemctl is-enabled).

* **indirect**: Similar a static, pero puede ser iniciado por dependencias.

Ejemplos:

1. Asegurarse de que el servicio httpd se inicia en el nodo web1.

    ```yaml
    ---
    - name: Start httpd
      hosts: web1
      gather_facts: no
      tasks:
        - name: Start httpd service
          service:
            name: httpd
            state: started
    ```

2. Copiar un archivo con un mensaje de bienvenida bajo la raíz de documentos del servidor httpd en el nodo web1. Recargar el servicio sin reiniciar el servidor.
  
    ```yaml
    ---
    - hosts: web1
      gather_facts: no
      tasks:
        - name: Copy Apache welcome file
          copy:
            src: index.html
            dest: /var/www/html/index.html
        - service:
            name: httpd
            state: reloaded
            enabled: yes
    ```

3. El servicio httpd en el nodo web1 se inicie siempre automáticamente después de reiniciar el sistema.

    ```yaml
    ---
    - name: Start httpd
      hosts: web1
      gather_facts: no
      tasks:
        - name: Start httpd service
          service:
            name: httpd
            state: started
            enabled: true
    ```

4. Habilitar el puerto 443 para httpd en el nodo web1 en lugar del puero 80.

    ```yaml
    ---
    - hosts: web1
      gather_facts: no
      tasks:
        - name: Make changes in Apache config
          replace:
            path: /etc/httpd/conf/httpd.conf
            regexp: "^Listen 80"
            replace: "Listen 443"

        - name: Restart Apache
          service:
            name: httpd
            state: restarted
    ```

5. Instalar nginx en el nodo web1 y asegurarnos de que el servicio nginx se inicia. Debe iniciarse siempre, incluso después de reiniciar el sistema.

    ```yaml
    ---
    - name: Install NGINX
      hosts: web1
      become: yes
      tasks:
        - name: Install nginx
          yum:
            name: nginx
            state: present

        - name: Start and enable nginx
          service:
            name: nginx
            state: started
            enabled: yes
    ```

## Reglas Firewall

En los ejemplos siguientes vamos a ver como administrar tanto firewalld cómo UFW. Firewalld es más común en distribuciones basadas en Red Hat (como CentOS y Fedora), mientras que UFW (Uncomplicated Firewall) es común en distribuciones basadas en Debian (como Ubuntu). Esto permitirá cubrir las configuraciones de cortafuegos en una amplia variedad de sistemas operativos Linux.

1. Instalar firewalld en el nodo web1, iniciar y habilitar su servicio también.


    ```yaml
    ---
    - name: Install and configure firewalld
      hosts: web1
      become: yes

      tasks:
        - name: Install firewalld
          yum:
            name: firewalld
            state: present

        - name: Start firewalld service
          systemd:
            name: firewalld
            state: started
            enabled: yes

    ```

2. En el nodo web1, poner en lista blanca la dirección IP 172.20.1.101 del nodo web2 en el cortafuegos. Añadir la IP en la zona interna.

    ```yaml
    ---
    - name: Whitelist IP address in firewalld
      hosts: web1
      become: yes

      tasks:
        - name: Ensure firewalld is running
          systemd:
            name: firewalld
            state: started
            enabled: yes

        - name: Add web2 IP to trusted zone
          firewalld:
            zone: internal
            source: 172.20.1.101
            permanent: yes
            state: enabled

        - name: Reload firewalld to apply changes
          command: firewall-cmd --reload

    ```

3. Bloquear el puerto 161/udp en el nodo web1 permanentemente. Usar zona: block

    ```yaml
    ---
    - name: Block 161/udp port in firewalld
      hosts: web1
      become: yes

      tasks:
        - name: Ensure firewalld is running
          systemd:
            name: firewalld
            state: started
            enabled: yes

        - name: Add 161/udp port to block zone
          firewalld:
            zone: block
            port: 161/udp
            permanent: yes
            state: enabled

        - name: Reload firewalld to apply changes
          command: firewall-cmd --reload

    ```

4. En el nodo web1 añadir una regla de cortafuegos en la zona interna para permitir la conexión https desde la máquina controladora de Ansible y asegurarnos de que la regla persiste incluso después de reiniciar el sistema. La dirección IP del controlador ansible es 172.20.1.2.


    ```yaml
    ---
    - name: Add HTTPS firewall rule in internal zone
      hosts: web1
      become: yes
      tasks:
        - name: Add firewall source rule for Ansible controller IP
          firewalld:
            zone: internal
            source: 172.20.1.2
            permanent: yes
            immediate: yes
            state: enabled

        - name: Add HTTPS service rule in internal zone
          firewalld:
            zone: internal
            service: https
            permanent: yes
            immediate: yes
            state: enabled

        - name: Reload firewalld to apply changes
          command: firewall-cmd --reload

    ```

5. Instalar el paquete firewalld e iniciar/habilitar su servicio. Como ahora Apache escucha en el puerto 8082, agregar una regla de firewall en la zona publica para que Apache pueda permitir todo el trafico entrante.


    ```yaml
    ---
    - hosts: web2
      tasks:
        - name: Install pkgs
          yum:
            name: httpd, firewalld
            state: present

        - name: Start/Enable services
          service:
            name: "{{ item }}"
            state: started
            enabled: yes
          with_items:
            - httpd
            - firewalld

        - name: Change Apache port
          replace:
            path: /etc/httpd/conf/httpd.conf
            regexp: '^(Listen)\s+80\s*$'
            replace: "Listen 8082"

        - name: restart Apache
          service:
            name: httpd
            state: restarted

        - name: Add firewall rule for Apache
          firewalld:
            port: 8082/tcp
            zone: public
            permanent: yes
            state: enabled
            immediate: true
    ```

6. Permitir puertos 22,80 y 443 usando UFW

    ```yaml
    ---
    - name: Allow ports 22, 80, and 443 in firewall
      ufw:
        rule: allow
        port: [22,80,443]

    - name: Verify firewall rules
      command: ufw status
      register: firewall_status

    - name: Display firewall status
      debug:
        var: firewall_status.stdout_lines
    ```

## Contenido de archivo

En este punto, exploraremos cómo realizar diversas tareas administrativas en el nodo web1. Cada sección contiene instrucciones específicas, desde la creación y configuración de archivos hasta la modificación de puertos y el uso de módulos como `file`, `lineinfile`, `blockinfile`, y `replace`. Estos ejemplos nos muestran el uso práctico de Ansible en la automatización de tareas cotidianas en servidores.

1. Crear un archivo en blanco /opt/data/perm.txt con permisos 0640 en el nodo web1.


    ```yaml
    ---
    - name: Create blank file /opt/data/perm.txt with 0640 permisions
      hosts: web1
      tasks:
        - name: Create file with permissions
          file:
            path: /opt/data/perm.txt
            mode: '0640'
            state: touch
    ```


2. Crear /var/www/html/index.html en web1 con este contenido This line was added using Ansible lineinfile module!.


    ```yaml
    ---
    - name: Create /var/www/html/index.html and add content
      hosts: web1
      tasks:
        - name: Create file index.html and add content
          lineinfile:
            path: /var/www/html/index.html
            line: 'This line was added using Ansible lineinfile module!'
            create: yes
    ```


3. Buscar recursivamente ficheros en el directorio /opt/data con una antigüedad superior a 2 minutos y un tamaño igual o superior a 1 megabyte. También copia esos ficheros en el directorio /opt. 


    ```yaml
    ---
    - hosts: web1
      tasks:
        - name: Find files
          find:
            paths: /opt/data
            age: 2m
            size: 1m
            recurse: yes
          register: file

        - name: Copy files
          command: "cp {{ item.path }} /opt"
          with_items: "{{ file.files }}"
    ```


4. En el archivo /var/www/html/index.html en el nodo web1 añadir un contenido adicional usando el módulo blockinfile. A continuación se muestra el contenido:

    ```
    ¡Bienvenido a KodeKloud!

    Esto es Ansible Lab.
    ```

    El usuario propietario y el grupo propietario del archivo debe ser apache. También hay que asegurarse de que el bloque se añade al principio del archivo. 


    ```yaml
    ---
    - name: Add block to index.html
      hosts: web1
      tasks:
      - blockinfile:
          owner: apache
          group: apache
          insertbefore: BOF
          path: /var/www/html/index.html
          block: |
            Welcome to KodeKloud!
            This is Ansible Lab.
    ```


5. En el nodo web1 queremos ejecutar nuestro servidor httpd en el puerto 8080. Hay que cambiar el puerto 80 a 8080 en el archivo /etc/httpd/conf/httpd.conf usando el módulo replace. También asegurarse de que Ansible reinicia el servicio httpd después de hacer el cambio.

    Listen 80 es el parámetro que necesita ser cambiado en /etc/httpd/conf/httpd.conf


    ```yaml
    ---
    - name: replace port 80 to 8080
      hosts: web1
      tasks:
      - replace:
          path: /etc/httpd/conf/httpd.conf
          regexp: '^(Listen)\s+80\s*$'
          replace: 'Listen 8080'
      - service: name=httpd state=restarted
    ```
## Archivado

A continuación, vamos a ver unos ejemplos de tareas para gestionar la compresión, descompresión y configuración de archivos, así como la instalación y personalización de servicios como Nginx. Además, se hace uso de módulos específicos como `community.general.archive`, `ansible.builtin.unarchive`, y otros útiles para escenarios prácticos de administración de sistemas.


Parámetros Comunes **community.general.archive** (Usado para comprimir)

* **path**: Ruta del archivo o directorio de origen.

* **dest**: Ruta destino del archivo comprimido.

* **format**: Tipo de compresión a usar (por ejemplo, gz, bz2, zip).

* **remove**: Indica si eliminar los archivos de origen después de comprimido

NOTA: Asegurarse de tener instalada la colección `community.general` en tu entorno Ansible. Puedes instalarla usando el siguiente comando: `ansible-galaxy collection install community.general`

1. Crear archivo opt.zip del directorio /opt en el nodo web1 y guardarlo en el directorio /root del propio nodo web1.

    ```yaml
    ---
    - name: Archive files
      hosts: web1
      tasks:
        - name: Create a compressed archive of files
          community.general.archive:
            path: /opt
            dest: /root/opt.zip
            format: zip
            remove: true

    ```

2. En el controlador Ansible, tenemos un archivo local.zip. Queremos extraer su contenido en web1 en el directorio /tmp.

    ```yaml
    ---
    - name: Extract local.zip on web1
      hosts: web1
      tasks:
        - name: Unarchive the file on the remote host
          ansible.builtin.unarchive:
            src: /home/thor/playbooks/local.zip
            dest: /tmp/
            remote_src: no

    ```

    Para descomprimir usamos el módulo `ansible.builtin.unarchive`


    * **src**: Ruta del fichero que deseamos descomprimir.

    * **dest**: Directorio de destino donde se descomprimirá el contenido del archivo.

    * **remote_src**: Indica si el archivo fuente está en el host remoto (yes) o en el controlador local (no).

3. En el nodo web1 tenemos un archivo data.tar.gz bajo el directorio /root, extraerlo en /srv 
Asegurarse de que el archivo data.tar.gz es eliminado después de eso.

    ```yaml
    ---
    - name: Extract local.zip on web1
      hosts: web1
      tasks:
        - name: Unarchive the file on the remote host
          ansible.builtin.unarchive:
            src: /root/data.tar.gz
            dest: /srv/
            remote_src: yes

        - name: Remove the archive after extraction
          ansible.builtin.file: 
            path: /root/data.tar.gz
            state: absent
    ```

4. Descargar y extraer el archivo zip https://github.com/kodekloudhub/Hello-World/archive/master.zip en el directorio /root del nodo web1.

    ```yaml
    ---
    - name: Download and extract master.zip on web1
      hosts: web1
      tasks:
        - name: Download the zip file from GitHub
          ansible.builtin.get_url:
            url: https://github.com/kodekloudhub/Hello-World/archive/master.zip
            dest: /root/master.zip

        - name: Extract the zip file
          ansible.builtin.unarchive:
            src: /root/master.zip
            dest: /root/
            remote_src: yes

    ```

5. Tenemos tres ficheros en el nodo web1 /root/file1.txt, /usr/local/share/file2.txt y /var/log/lastlog. Crea un archivo bz2 de todos estos ficheros y guárdalo en el directorio /root, nombra el archivo como ficheros.tar.bz2. 

    ```yaml
    ---
    - name: Create bz2 archive of specific files
      hosts: web1
      tasks:
        - name: Create a bz2 archive of the specified files
          community.general.archive:
            path:
              - /root/file1.txt
              - /usr/local/share/file2.txt
              - /var/log/lastlog
            dest: /root/files.tar.bz2
            format: bz2

    ```

6. Queremos configurar nginx en el nodo web1 con algún código html de ejemplo.  A continuación se presentan los detalles acerca de la tarea:

    A.  Instalar el paquete nginx e iniciar/activar su servicio.

    B.  Extrae el archivo /root/nginx.zip del directorio /usr/share/nginx/html.

    C.  Dentro de /usr/share/nginx/html/index.html sustituye la línea This is sample html code por la línea This is KodeKloud Ansible lab.


    ```yaml
    ---
    - name: Setup nginx on web1 with sample html code
      hosts: web1
      tasks:
        - name: Install nginx
          yum:
            name: nginx
            state: present
          become: yes

        - name: Start and enable nginx service
          service:
            name: nginx
            state: started
            enabled: yes
          become: yes

        - name: Extract nginx.zip archive
          unarchive:
            src: /root/nginx.zip
            dest: /usr/share/nginx/html
            remote_src: yes
          become: yes

        - name: Replace line in index.html
          lineinfile:
            path: /usr/share/nginx/html/index.html
            regexp: 'This is sample html code'
            line: 'This is KodeKloud Ansible lab'
          become: yes

    ```

## Tareas Programadas

Los siguientes ejemplos, muestran  cómo utilizar Ansible para gestionar tareas de cron en el nodo node00. Las tareas incluyen limpiar archivos de registros, programar la ejecución de scripts y comandos específicos, eliminar crons previos, realizar limpieza automática después del reinicio, y configurar actualizaciones automáticas de paquetes. Los módulos de Ansible, como `cron`, permiten automatizar estas operaciones esenciales de manera eficiente y reproducible.

1. Cree un playbook ~/playbooks/lastlog.yml para añadir una tarea cron Clear Lastlog en node00 para vaciar el archivo de registros /var/log/lastlog. La tarea debe ejecutarse todos los días a las 12 de la mañana.

    Puede utilizar el comando `echo «» > /var/log/lastlog` para vaciar el archivo lastlog y el horario debe ser `0 0 * * *`.

      ```yaml
        ---
        - name: Add a cron job to clear lastlog
          hosts: node00
          tasks:
            - name: Ensure the cron job is present
              cron:
                name: "Clear Lastlog"
                minute: "0"
                hour: "0"
                job: 'echo "" > /var/log/lastlog'
      ```

2. Tenemos un script /root/free.sh en node00 que se utiliza para comprobar la memoria libre del sistema.Nos gustaría crear un cron Free Memory Check para ejecutar este script cada 2 horas (es decir, 12am, 2am, 4am etc), el comando para ejecutar el script es sh /root/free.sh y el horario debe ser 0 */2 * * *.

    Puede crear un playbook ~/playbooks/script_cron.yml para esto.

    ```yaml
    ---
    - name: Add a cron job to check free system memory
      hosts: node00
      tasks:
        - name: Add the cron job 
          cron:
            name: "Free Memory Check"
            minute: "0"
            hour: "*/2"
            job: 'sh /root/free.sh'
    ```


3. Teníamos un cron anterior con el nombre Check Memory, para ejecutar un script diferente - /root/free.sh en node00.
Ese trabajo estaba configurado para ejecutarse cada 1 hora. Sin embargo, como ahora tenemos un nuevo Cronjob configurado vamos a deshacernos del antiguo. Crea un playbook ~/playbooks/remove_cron.yml para eliminar este cron del nodo00.

    ```yaml
    ---
    - name: remove cron job from node00
      hosts: node00
      tasks:
      - name: Remove cron job
        cron:
          name: "Check Memory"
          state: absent
    ```


4. Debido a algunas limitaciones de espacio en disco, queremos limpiar la ubicación /tmp en el nodo00 después de cada reinicio.
Cree un playbook ~/playbooks/reboot.yml para añadir un cron llamado cleanup en node00 que se ejecutará después de cada reinicio y limpiará la ubicación /tmp.

    El comando debe ser `rm -rf /tmp/*`.

    ```yaml
    ---
    - name: Cleanup /tmp after every reboot
      hosts: node00
      tasks:
      - cron:
          name: cleanup
          job: rm -rf /tmp/*
          special_time: reboot
    ```


5. En node00 queremos mantener actualizados los paquetes instalados, por lo que nos gustaría ejecutar actualizaciones de yum regularmente.

    Cree un playbook ~/playbooks/yum_update.yml y cree una tarea cron como se describe a continuación:

    A. No añada cron directamente usando crontab en su lugar cree un archivo cron /etc/cron.d/ansible_yum.

    B. El cron debe ejecutarse todos los domingos a las 8:05 am.

    C. El nombre del cron debe ser yum update.

    D. El cron debe ser agregado para el usuario root


    Utilice el comando `yum -y update`


    ```yaml
    ---
    - name: Create cron for yum
      hosts: node00
      gather_facts: no
      tasks:
        - name: Creates a cron
          cron:
            name: yum update
            weekday: 0
            minute: 5
            hour: 8
            user: root
            job: "yum -y update"
            cron_file: ansible_yum
    ```

## Usuarios y grupos

En este último punto, veremos cómo gestionar usuarios y grupos en sistemas Linux. Veremos cómo crear usuarios con permisos especiales, asociarlos a grupos, configurar la expiración de cuentas, y también cómo eliminar usuarios y grupos de manera eficiente. Estas tareas son esenciales para gestionar de forma segura y automatizada los accesos en servidores remotos.

1. Crear usuario javier y añadirlo a sudoers

    ```yaml
    ---
    - name: Create user
      user:
        name: javier
        state: present

    - name: Add user to sudoers
      lineinfile:
        path: /etc/sudoers
        line: "javier ALL=(ALL) NOPASSWD: ALL"
        state: present
    ```

2. Crear grupo admin y un usuario admin con grupo admin y uid 2048

    ```yaml
    ---

    - hosts: all
      gather_facts: no
      tasks:
        - group:
            name: 'admin'
            state: present
        - user:
            name: 'admin'
            uid: 2048
            group: 'admin'
    ```

3. Supongamos que Sabin Nepal se unió a su equipo el primer día de 2020 como contratista especial para trabajar durante un período de 3 años, es decir, hasta el final del año 2023.
Él necesita sus cuentas en los hosts remotos hasta su período de trabajo.

    Escribe un playbook add_user.yml para crear su cuenta de usuario con el nombre de usuario neymarsabin que expirará después de 3 años. La opción expires en el módulo users está en la época. Así que domingo, 31 de diciembre de 2023 11:59:59 PM GMT== 1704067199 como tiempo de época
    
    Recuerda: tu playbook debe estar dentro de /home/thor/playbooks y usar el archivo de inventario allí.

    ```yaml
    ---
    - hosts: all
      gather_facts: no
      tasks:
        - user:
            name: 'neymarsabin'
            expires: 1704067199
    ```

4. Eliminar en todos los servidores el usuario y  grupo admin

    ```yaml
    ---
    - hosts: all
      gather_facts: no
      tasks:
        - user:
            name: 'admin'
            state: absent
        - group:
            name: 'admin'
            state: absent
    ```