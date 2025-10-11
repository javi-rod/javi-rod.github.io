---
layout: post
title: "Write-Up: Pickle Rick [THM]"
date: 2025-10-11 00:00
share: true
categories: [THM,CTF,writeup]
tags: 
- THM
- CTF
- writeup
---

# Introducción

Este desafío temático de Rick y Morty requiere que explotes un servidor web y encuentres tres ingredientes para ayudar a Rick a preparar su poción y transformarse de nuevo en humano desde un pepinillo.

# Descripción del reto

- Contexto: reto disponible en la plataforma TryHackMe.

- Objetivo: obtener los tres ingredientes (flags) y demostrar el proceso.

- Archivos provistos: Ninguno

# Paso 1: Reconocimiento / Información inicial

A continuación se describen los comandos y herramientas usadas para hacer un reconocimiento inicial.

```bash
# Ejecución de nmap para ver los puertos abiertos en la máquina.
nmap -sS -sV -sC -p- -T4 -Pn 10.10.142.229
```
<img src="https://javi-rod.github.io/assets/images/20251011/01_NMAP.png" alt="Captura de salida de nmap mostrando puertos 22 y 80 abiertos" />


Opciones usadas:

* `-sS` → Escaneo SYN (half-open scan)
* `-sV` → Detectar versión del servicio
* `-sC` → Ejecutar scripts por defecto de Nmap
* `-p-` → Escanear todos los puertos TCP (1 al 65535)
* `-T4` → Timing (más rápido, más agresivo)
* `-Pn` → No realizar host discovery (asume que el host está UP)

⚠️ **Advertencia**: A la hora de realizar el write-up me he dado cuenta que el comando anterior debe ejecutarse con `sudo`para que se ejecute el SYN scan (-sS), de lo contrario hará un TCP connect scan (-sT), lo cual es más ruidoso

Del resultado anterior, vemos que tenemos el puerto 80 abierto, corriendo un servidor Apache. Además del puerto 22 para el servicio SSH. 


# Paso 2: Enumeración web

Vamos a centrarnos en el servidor web. Para ello, vamos a introducir la IP de la máquina en la URL.

Este es el aspecto de la web.

<img src="https://javi-rod.github.io/assets/images/20251011/02_WEB.png" alt="Aspecto de la web" />

Lo primero que vamos a hacer es inspeccionar el código fuente de la página. En Firefox, haciendo clic en el botón derecho en cualquier parte, y seleccionando *Ver código fuente de la página.*

Del código llaman la atención dos cosas. Hay un directorio *assets* y un comentario con un nombre de usuario, *R1ckRul3s*

<img src="https://javi-rod.github.io/assets/images/20251011/03_COD_FUENTE_WEB.png" alt="Código fuente de la página.En éste vemos un comentario donde se indica un usuario." />

Antes de ejecutar la herramienta Gobuster, vamos a revisar si el directorio assets es accesible y su contenido.

Vemos una serie de imágenes, así como dos javascript y un css. Nada que llame la atención a simple vista.

<img src="https://javi-rod.github.io/assets/images/20251011/04_ASSETS.png" alt="Código fuente de la página.En éste vemos un comentario donde se indica un usuario." />

A continuación se describen los comandos y herramientas usadas para realizar la enumeración de directorios.

```bash
# Ejecución de gobuster para enumerar los directorios y archivos.
gobuster dir -u http://10.10.142.229 -w /usr/share/seclist/Discovery/Web-Content/common.txt -t 16 -x php,htm,txt
```
<img src="https://javi-rod.github.io/assets/images/20251011/05_GOBUSTER.png" alt="Captura salida gobuster, donde vemos un login.php y un robots.txt accesibles." />

Opciones usadas:

* `dir`→  buscar carpetas y archivos en el sitio web.

* `-u:` →  URL objetivo.

* `-w:` → wordlist con nombres a probar.

* `-t 16` →  usar 16 hilos para hacer varias peticiones al mismo tiempo-

* `-x php,htm,txt` →  probar extensiones en cada palabra

De la ejecución anterior, vemos que tenemos un *login.php* y un *robots.txt* llamativos.

Si introducimos en el navegador la URL http://10.10.142.229/login.php veremos un portal de login, donde necesitamos un usuario y una contraseña. Un usuario probable ya lo tenemos de antes (R1ckRul3s)

<img src="https://javi-rod.github.io/assets/images/20251011/06_PORTAL_LOGIN.png" alt="Portal de login." />

Antes de seguir, vamos a ver que hay en el robots.txt. Vemos un texto extraño sin sentido, no obstante tomamos nota del dato.

<img src="https://javi-rod.github.io/assets/images/20251011/07_ROBOTS.png" alt="Contenido del robots.txt." />

# Paso 3: Acceso inicial

Ahora vamos a regresar al portal de login, y vamos a probar con el usuario que ya teníamos anteriormente, pero ¿cuál es la contraseña? Intentemos con el texto raro que estaba en el robots.txt

Como vemos, accedemos al portal donde vemos una serie de pestañas.

<img src="https://javi-rod.github.io/assets/images/20251011/08_COMMANDS.png" alt="Portal de login." />

Si intentamos acceder a cualquiera de las otras pestañas, veremos lo siguiente (*Viendo el código fuente todas ellas apuntan al mismo lugar*)

<img src="https://javi-rod.github.io/assets/images/20251011/09_POTIONS.png" alt="Portal de login." />

# Paso 4: Localización de flags / Exploración

Regresamos a la pestaña Commands, y vamos a ejecutar `whoami`. El resultado nos dice que somos el usuario www-data.

<img src="https://javi-rod.github.io/assets/images/20251011/10_WHOAMI.png" alt="Portal de login." />

A continuación vamos a ver en qué ruta estamos, por lo que ejecutamos un `pwd`

<img src="https://javi-rod.github.io/assets/images/20251011/11_PWD.png" alt="Portal de login." />

Ahora que sabemos que estamos en la ruta /var/www/html (*ruta por defecto en servidores Apache*) vamos a listar el contenido con `ls -al`

Como resultado, vemos dos archivos .txt llamativos, uno de estos es uno de  los ingredientes y primera flag (Sup3rS3cretPickl3Ingred.txt) y el otro es una pista (clue.txt)

<img src="https://javi-rod.github.io/assets/images/20251011/12_LS.png" alt="Portal de login." />

Si abrimos la pista, nos indica que sigamos revisando por el sistema de ficheros para encontrar los otros ingredientes.

<img src="https://javi-rod.github.io/assets/images/20251011/13_CLUE.png" alt="Portal de login." />

Para leer el primer ingrediente, no es necesario usar ningún comando. Con ponerlo en la URL nos valdrá.

<img src="https://javi-rod.github.io/assets/images/20251011/14_PRIMER_INGREDIENTE.png" alt="Primera flag." />

Ahora vamos a listar los directorios que hay en /home. Como resultado vemos que hay un home para rick.

<img src="https://javi-rod.github.io/assets/images/20251011/15_LS_HOME.png" alt="Portal de login." />

Listamos con `ls -al /home/rick/` el contenido y vemos un fichero *second ingredients*. 

<img src="https://javi-rod.github.io/assets/images/20251011/16_LS_HOME_RICK.png" alt="Portal de login." />

Como vemos el propietario y el grupo son **root**, y nosotros somos **www-data**, pero si nos fijamos, otros tiene el permiso de lectura,escritura y ejecución, por lo que cualquiera puede leer el contenido.

Si hacemos uso del comando `cat`veremos lo siguiente

<img src="https://javi-rod.github.io/assets/images/20251011/17_ERROR_COMANDO.png" alt="Comando no disponible." />

Si pruebas con otros comandos como `head` o  `tail` también nos dirá que están deshabilitados. Un comando que sí está disponible es `less`.

Ejecutando un `less '/home/rick/second ingredients'`obtendremos la segunda flag.

Ojo: la ruta se pone entre comillas simples (' ') para salvar el espacio.

<img src="https://javi-rod.github.io/assets/images/20251011/18_LESS_SEGUNDO_INGREDIENTE.png" alt="Contenido segunda flag." />

# Paso 5: Escalada de privilegios

Nos falta el último ingrediente, pero no tenemos privilegios para seguir revisando por ejemplo en /root. Así que vamos a ver  como escalar privilegios.

Ejecutaremos un `sudo -l` para averiguar qué comandos podemos ejecutar con `sudo`. Este es el resultado

<img src="https://javi-rod.github.io/assets/images/20251011/19_SUDO_L.png" alt="No tenemos restricción para el uso de sudo." />

¡Oh sorpresa! no tenemos restricciones para el uso de sudo.

Vamos a listar el contenido de  /root con `sudo ls -al /root`

<img src="https://javi-rod.github.io/assets/images/20251011/20_SUDO_LS_ROOT.png" alt="Listado del directorio /root donde está la tercera flag" />

Como vemos ahí tenemos el tercer ingrediente (3rd.txt).

Para verlo ejecutamos `sudo less /root/3rd.txt`

<img src="https://javi-rod.github.io/assets/images/20251011/21_SUDO_LESS_TERCER_INGREDIENTE.png" alt="Contenido de la tercera flag" />

Con eso ya tendríamos los 3 ingredientes (flags) completando el CTF.

<img src="https://javi-rod.github.io/assets/images/20251011/22_DESAFIO_COMPLETADO.png" alt="Reto completado." />

# Paso 6: Post‑explotación y evidencia

Se encontraron las 3 flags solicitadas

# Paso 7: Remediaciones y recomendaciones

- No almacenar credenciales en comentarios HTML. Quitar usuarios/credenciales del código público.

- Restringir sudo: el usuario no debería tener capacidad para ejecutar comandos arbitrarios como root. Usar la mínima necesaria en /etc/sudoers.

- Revisar permisos de ficheros en /home (evitar others con rwx).

- Deshabilitar ejecución de comandos arbitrarios desde web. Validar entrada y sanear parámetros.

- Registro/auditoría: habilitar logs de comandos web y alertas para uso de sudo.

# Conclusión

Se consiguió acceso inicial mediante credenciales encontradas en robots.txt, se explotó el ability to run commands desde el panel web para enumerar el sistema, y la escalada a root se realizó aprovechando permisos sudo no restringidos. **Lecciones**: minimizar credenciales en el repositorio, restringir sudo, y revisar permisos de ficheros.

# Herramientas utilizadas

nmap, gobuster, navegador, less, sudo