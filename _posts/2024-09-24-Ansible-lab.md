---
layout: post
title: Ansible - Laboratorio
date: 2024-09-24 00:00
share: true
categories: [Ansible,IaC (Infrastructure as Code)]
tags: 
- Ansible
- IaC (Infrastructure as Code)
---

En el curso que realicé de Ansible, en KodeKloud, enseñaban a montar un laboratorio y os quiero mostrar, cómo de sencillo es.

Para este laboratorio necesitamos lo siguiente:

+ VirtualBox (Disponible en su sitio oficial [virtualbox.org](https://www.virtualbox.org/wiki/Downloads))

+ Un disco virtual de CentOS para VirtualBox (Disponible en la web de [osboxes.org](https://www.osboxes.org/))

El primer paso, será descargarnos VirtualBox e instalarlo. Voy a obviar cómo es el proceso de instalación porque va a depender de vuestro sistema, si es Windows, Mac o Linux (No obstante si tienes alguna duda, puedes comentármelo)

<img src="https://javi-rod.github.io/assets/images/20240924/01_descarga_vbox.png" alt="Descarga de VirtualBox según Sistema Operativo" width="300" height="250"/>

Una vez tengamos instalado VirtualBox, vamos a descargar la imagen del disco de osboxes.

Cuando estemos en la web, debemos seleccionar *"VM IMAGES"* y en el desplegable hacer clic en *VirtualBox Images*.

<img src="https://javi-rod.github.io/assets/images/20240924/02_osboxes.png" alt="OSBOXES web, descargar imagen VBox"/>

Luego, entre todas las distribuciones, seleccionaremos CentOS y hacemos clic en VirtualBox (VDI) image.

<img src="https://javi-rod.github.io/assets/images/20240924/03_centos_vdi.png" alt="Descarga VDI de CentOS"/>

Vamos a descargar la versión más reciente disponible, que es CentOS 9 y su versión Server.

<img src="https://javi-rod.github.io/assets/images/20240924/04_download_vdi.png" alt="Descargar CentOS 9 vdi"/>

Una vez descargado, es recomendable verificar que el hash del fichero coincide con el indicado en la web.

En el caso de usar Linux usaremos el comando siguiente:

`sha256sum <fichero>`

<img src="https://javi-rod.github.io/assets/images/20240924/05_comprobar_hash.png" alt="Comprobar hash en linux"/>

<img src="https://javi-rod.github.io/assets/images/20240924/06_comprobar_hash_web.png" alt="Comprobar hash en Windows"/>

Si usas Windows, abre una consola de PowerShell, y ejecuta el cmdlet siguiente:

`Get-FileHash <fichero>`

Tras comprobar que coinciden, abriremos VirtualBox y añadiremos una nueva máquina virtual.

<img src="https://javi-rod.github.io/assets/images/20240924/07_nueva_mv.png" alt="Crear nueva máquina virtual en VirtualBox"/>

Nos aparecerá la ventana de creación de máquina virtual, dónde daremos un nombre, seleccionaremos el lugar dónde se ubicará la máquina virtual y elegiremos el tipo de Sistema Operativo para esa máquina. Una vez rellenado vamos al siguiente apartado haciendo clic en Siguiente.

<img src="https://javi-rod.github.io/assets/images/20240924/08_nombre_so_mv.png" alt="Nombre, ubicación, tipo SO de la máquina virtual"/>

En este apartado Hardware indicaremos cuanta memoria y cuantas CPUs vamos a usar. En este caso 2GB y 2 CPUs son suficientes.

<img src="https://javi-rod.github.io/assets/images/20240924/09_ram_cpu_mv.png" alt="Hardware de la máquina virtual"/>

El siguiente paso es la creación del disco duro. En nuestro caso no vamos a crear disco, ya que lo hemos descargado previamente. Seleccionamos la opción dos, *"Usar un archivo de disco duro virtual existente"*.

<img src="https://javi-rod.github.io/assets/images/20240924/10_dd_mv.png" alt="Disco duro de la máquina virtual"/>

En la ventana selector de discos, hacemos clic en Añadir

<img src="https://javi-rod.github.io/assets/images/20240924/11_añadir_dd_mv.png" alt="Añadir disco duro a la máquina virtual"/>

Ahora buscamos el disco y lo seleccionamos haciendo doble clic o bien en Abrir.

<img src="https://javi-rod.github.io/assets/images/20240924/12_seleccionar_dd.png" alt="Seleccionar vdi"/>

Ahora veremos que el disco duro nos aparece en el selector de disco duro, le seleccionamos

<img src="https://javi-rod.github.io/assets/images/20240924/13_dd_añadido.png" alt="Disco seleccionado listo para adjuntar a la máquina virtual"/>

Y ya tendremos el disco duro asociado a la máquina virtual. Hacemos clic en Siguiente.

<img src="https://javi-rod.github.io/assets/images/20240924/14_dd_añadido_mv.png" alt="Disco duro seleccionado para usar"/>

Nos aparecerá un Resumen de la máquina que vamos a crear, si todo es correcto, hacemos clic en Terminar.

<img src="https://javi-rod.github.io/assets/images/20240924/15_resumen_mv.png" alt="Resumen de la configuración de la máquina virtual"/>

Una vez creada esto es lo que veremos en VirtualBox.

<img src="https://javi-rod.github.io/assets/images/20240924/16_mv_creada.png" alt="Vista de Virtual Box donde se ve la nueva máquina creada"/>

Antes de iniciar la máquina, vamos a seleccionar sus propiedades para cambiar el adaptador de red de NAT a Adaptador puente (Bridge). Esto nos va a permitir conectarnos a la máquina virtual vía SSH al estar en nuestra red.

<img src="https://javi-rod.github.io/assets/images/20240924/17_configurar_red.png" alt="Cambiamos el adaptador de red de NAT a Bridge"/>

Una vez hecho el cambio de red, vamos a iniciar la máquina.

<img src="https://javi-rod.github.io/assets/images/20240924/18_iniciar_mv.png" alt="Iniciar máquina virtual"/>

Esto es lo que veremos una vez haya arrancado. El usuario y la contraseña están indicados en la web de osboxes. En el laboratorio, primero haremos uso del usuario root, luego crearemos uno propio.

<img src="https://javi-rod.github.io/assets/images/20240924/19_login_mv.png" alt="login"/>

Ahora que ya estamos conectados, vamos hacer unos cambios.

Lo primero, vamos a cambiar el idioma del teclado a español, para ello ejecutaremos:

`localectl set-keymap es`

Ahora, vamos a cambiar la zona horaria:

`timedatectl set-timezone Europe/Madrid`

Por último vamos a editar el fichero `/etc/hostname` para añadir el nombre de la máquina y editaremos el fichero `/etc/hosts` para añadir también el nombre de la máquina.

Una vez que hayamos hecho estos cambios, vamos a crear un usuario propio y añadirlo al grupo wheel para hacer uso del comando sudo.

Para añadir el usuario ejecutaremos el comando siguiente, donde usuario será el nombre para nuestro usuario:

`useradd <usuario>`

Ahora añadiremos el usuario al grupo wheel:

`usermod -a -G wheel <usuario>`

Después de hacer los pasos anteriores vamos a ver que IP tenemos asignada, para ello lanzaremos el comando siguiente:

`ip address`

Veréis que la interfaz de red no tiene una IP asignada, es porque no está activada la conexión.

Debemos ejecutar `nmtui` y seleccionar *"Activar una conexión"*.

<img src="https://javi-rod.github.io/assets/images/20240924/20_net_manager.png" alt="Herramienta NetworkManager TUI.Nos permite modificar los datos de una interfaz, activar una interfaz y cambiar el nombre de host"/>

Después reiniciamos la máquina ejecutando `reboot`. Una vez hecho, volvemos a ejecutar `ip address`.

<img src="https://javi-rod.github.io/assets/images/20240924/21_ip_mv.png" alt="IP de la máquina de Ansible"/>

El siguiente paso no es obligatorio, pero puede ayudarte a mejorar la organización de máquinas en VirtualBox.

Seleccionamos la máquina virtual, hacemos clic derecho y seleccionaremos *Mover a grupo*, *Nueva*. 

<img src="https://javi-rod.github.io/assets/images/20240924/22_mover_mv_a_grupo.png" alt="Mover máquina virtual a un grupo"/>

Una vez tengamos el grupo, le renombraremos haciendo clic botón derecho sobre éste.

<img src="https://javi-rod.github.io/assets/images/20240924/23_renombrar_grupo.png" alt="Renombrar grupo"/>

Y ya tenemos listo nuestro grupo.

<img src="https://javi-rod.github.io/assets/images/20240924/24_grupo_ansible.png" alt="Grupo creado"/>

Ahora que ya tenemos esta primera máquina lista, vamos a clonarla. Seleccionamos la máquina, clic derecho ratón y seleccionamos *Clonar*.

<img src="https://javi-rod.github.io/assets/images/20240924/25_clonar.png" alt="Clonar máquina"/>

Nos aparecerá la ventana "Clonar máquina virtual". Debemos darle un nombre a la máquina clonada y la ruta dónde se alojará. Además tendremos que seleccionar en "Tipo de Clonado (Type Clone)", la opción *"Clonación enlazada"* y en Opciones adicionales, elegir *"Generar nuevas direcciones MAC para todos los adaptadores de red"*. Tras lo anterior, hacemos clic en Terminar para que se haga la clonación.

<img src="https://javi-rod.github.io/assets/images/20240924/26_datos_clonar.png" alt="Opciones para clonar máquina"/>

Volveremos a repetir el paso anterior para tener un segunda máquina objetivo.

Ahora arrancaremos las dos máquinas que hemos clonado.

<img src="https://javi-rod.github.io/assets/images/20240924/27_grupo_ansible_corriendo.png" alt="Grupo Ansible-Lab corriendo las máquinas de ansible y objetivos"/>

Nos conectaremos con el usuario root y modificaremos los ficheros `/etc/hostname`y `/etc/hosts` para añadir el nombre de estas máquinas. Luego le daremos un reinicio a las dos máquinas.

Una vez reiniciadas, nos conectamos con el usuario que creamos anteriormente y ejecutamos un `ip address` para ver las IP que se nos ha asignado. Al ser un clon de la máquina origen, no es necesario activar la interfaz de red.

<img src="https://javi-rod.github.io/assets/images/20240924/28_ip_tg1_tg2.png" alt="IPs de las máquinas objetivo"/>

Después vamos a probar que podemos acceder vía SSH a las máquinas con el usuario que creamos anteriormente desde nuestro equipo.

<img src="https://javi-rod.github.io/assets/images/20240924/29_ssh_host_mv.png" alt="Conexión SSH a máquina Ansible"/>

<img src="https://javi-rod.github.io/assets/images/20240924/30_ssh_host_tg1.png" alt="Conexión SSH a máquina objetivo 1"/>

<img src="https://javi-rod.github.io/assets/images/20240924/31_ssh_host_tg2.png" alt="Conexión SSH a máquina objetivo 2"/>

Realizada la comprobación anterior, vamos a proceder a instalar Ansible, siguiendo la documentación oficial para nuestra distribución. 

Os dejo el enlace https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html#installing-ansible-from-epel

<img src="https://javi-rod.github.io/assets/images/20240924/32_ansible_doc.png" alt="Doc. oficial Ansible para EPEL"/>

Cómo podéis ver, para CentOS necesitamos el repositorio EPEL (Extra Packages for Enterprise Linux).

Podéis leer la documentación de EPEL en el siguiente enlace: https://docs.fedoraproject.org/en-US/epel/getting-started/

En nuestro caso, para CentOS 9 necesitamos estos dos paquetes: `epel release` y `epel-next-release`. Y también hay que habilitar el repositorio crb.

<img src="https://javi-rod.github.io/assets/images/20240924/33_doc_el9.png" alt="Doc.oficial EPEL"/>

Así que ejecutaremos:

`sudo dnf config-manager --set-enable crb`

`sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm`

`sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-next-release-latest-9.noarch.rpm`

<img src="https://javi-rod.github.io/assets/images/20240924/34_instalar_paquete_epel_release.png" alt="Instalar paquete epel-release"/>

<img src="https://javi-rod.github.io/assets/images/20240924/35_instalar_paquete_epel_next_release.png" alt="Instalar paquete epel-next-release"/>

Realizado el paso anterior, procederemos a la instalación de Ansible.

`sudo dnf install ansible`

<img src="https://javi-rod.github.io/assets/images/20240924/36_instalar_ansible.png" alt="Instalar paquete de Ansible"/>

Una vez instalado, para verificar que se ha instalado correctamente, ejecutaremos lo siguiente:

`ansible --version`

<img src="https://javi-rod.github.io/assets/images/20240924/37_ver_version_ansible.png" alt="Comprobar que se ha instalado correctamente, viendo la versión"/>

Ahora vamos a comprobar que hay conexión SSH entre la máquina que tiene instalado Ansible y una de las máquinas clonadas.

<img src="https://javi-rod.github.io/assets/images/20240924/38_ssh_ansible_tg1.png" alt="SSH máquina Ansible contra máquina objetivo 1"/>

Verificado que hay conexión, regresamos a la máquina de Ansible y crearemos un directorio llamado test-project para hacer pruebas y accederemos a él.

`mkdir test-project`

`cd test-project/`

<img src="https://javi-rod.github.io/assets/images/20240924/39_crear_carpeta_test.png" alt="Comando para crear la carpeta test-project y acceder a ella"/>

Dentro de este directorio, crearemos un fichero que va a ser nuestro inventario, el que va a contener la información de las máquinas objetivo.

`cat > inventory.txt`

`target1 ansible_host=ip_target1 ansible_ssh_pass=contraseña_target1`

Debéis cambiar ip_target1 por la IP de vuestra máquina virtual y en contraseña_target1 poner la del usuario que creaste anteriormente.

<img src="https://javi-rod.github.io/assets/images/20240924/40_crear_inventario.png" alt="Crear fichero de inventario que contendrá la información de las máquina objetivo"/>

Si hacemos un cat al fichero veremos que ahora tenemos en nuestro inventario uno de los host.

Para comprobar que funciona nuestro inventario, vamos a ejecutar el siguiente comando de ansible.

`ansible target1 -m ping -i inventory.txt`

<img src="https://javi-rod.github.io/assets/images/20240924/41_ansible_ping_tg1.png" alt="PING a una máquina del inventario mediante ansible"/>

Cómo vemos ha funcionado.

Ahora hay que añadir al inventario el target2 editando con `vi` o `nano` el fichero de inventory.txt

Una vez añadido al inventario, si intentamos hacer un ping como hicimos anteriormente, fallará.

`ansible target2 -m ping -i inventory.txt`

<img src="https://javi-rod.github.io/assets/images/20240924/42_ansible_ping_tg2_fallo.png" alt="PING a una máquina medianete ansible que ha fallado"/>

Si vemos el error, dice que utilizando contraseña SSH en lugar de una key no es posible porque en la configuración de ansible está habilitado Key checking y sshpass no lo soporta. Ese mensaje también nos está dando cómo solucionarlo. Debemos añadir el fingerprint a nuestro fichero de hosts conocidos.

Hacer lo anterior, es sencillo, ejecutar un SSH desde la máquina de Ansible al target2 para almacenar el fingerprint.

<img src="https://javi-rod.github.io/assets/images/20240924/43_ssh_ansible_tg2.png" alt="SSH máquina Ansible a la máquina objetivo 2"/>

Después de acceder vía SSH y salir de la máquina target2, ejecutamos el ping vía Ansible, veremos que ahora no falla.

<img src="https://javi-rod.github.io/assets/images/20240924/44_ansible_ping_tg2.png" alt="PING con ansible a la máquina objetivo 2"/>

Y con esto tendremos un laboratorio de Ansible funcionando para practicar y seguir aprendiendo.