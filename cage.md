
# SCAN

```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             396 May 25  2020 dad_tasks
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.11.62.57
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dd:fd:88:94:f8:c8:d1:1b:51:e3:7d:f8:1d:dd:82:3e (RSA)
|   256 3e:ba:38:63:2b:8d:1c:68:13:d5:05:ba:7a:ae:d9:3b (ECDSA)
|_  256 c0:a6:a3:64:44:1e:cf:47:5f:85:f6:1f:78:4c:59:d8 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Nicholas Cage Stories
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```
# FTP
Podemos entrar como anonymous y bajar el fichero **dad_tasks** que contiene un texto codificado

# WEB ENUM
Enumeramos la pagina con gobuster dir
```
/images               (Status: 301) [Size: 305] [--> http://cage.thm/images/]                                         
/html                 (Status: 301) [Size: 303] [--> http://cage.thm/html/]  
/scripts              (Status: 301) [Size: 306] [--> http://cage.thm/scripts/]
/contracts            (Status: 301) [Size: 308] [--> http://cage.thm/contracts/]
Progress: 21686 / 87665 (24.74%)                                               [ERROR] 2022/08/15 13:15:48 [!] Get "ht
tp://cage.thm/1746": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
[ERROR] 2022/08/15 13:15:48 [!] Get "http://cage.thm/4670": context deadline exceeded (Client.Timeout exceeded while a
waiting headers)
[ERROR] 2022/08/15 13:15:48 [!] Get "http://cage.thm/2348": context deadline exceeded (Client.Timeout exceeded while a
waiting headers)
[ERROR] 2022/08/15 13:15:48 [!] Get "http://cage.thm/2133": context deadline exceeded (Client.Timeout exceeded while a
waiting headers)
[ERROR] 2022/08/15 13:15:48 [!] Get "http://cage.thm/4394": context deadline exceeded (Client.Timeout exceeded while a
waiting headers)
Progress: 26920 / 87665 (30.71%)                                               [ERROR] 2022/08/15 13:16:11 [!] Get "ht
tp://cage.thm/f-40": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
[ERROR] 2022/08/15 13:16:11 [!] Get "http://cage.thm/f-33": context deadline exceeded (Client.Timeout exceeded while a
waiting headers)
Progress: 35418 / 87665 (40.40%)                                               [ERROR] 2022/08/15 13:16:54 [!] Get "ht
tp://cage.thm/ftr_right": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
[ERROR] 2022/08/15 13:16:54 [!] Get "http://cage.thm/news1165006800": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
Progress: 55703 / 87665 (63.54%)                                               [ERROR] 2022/08/15 13:18:16 [!] Get "ht
tp://cage.thm/1997-08": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
/auditions            (Status: 301) [Size: 308] [--> http://cage.thm/auditions/]
```
Dentro del directorio **/auditions** hay un archivo de audio:
> must_practice_corrupt_file.mp3

Podemos analizar el espectrograma con Audacity y encontramos algo que parece ser un password:
> namelesstwo

# VIGNERE
El texto que hemos encontrado codificado en el fichero ftp **dad_tasks** se decodifica en dos pasos:
1. base64
2. vignere utilizando como clave **namelesstwo**
Obtenemos el siguiente texto decodificado:
```
Dads Tasks - The RAGE...THE CAGE... THE MAN... THE LEGEND!!!!
One. Revamp the website
Two. Put more quotes in script
Three. Buy bee pesticide
Four. Help him with acting lessons
Five. Teach Dad what "information security" is.

In case I forget.... Mydadisghostrideraintthatcoolnocausehesonfirejokes
```
# SSH
Probamos logearnos en ssh con weston:Mydadisghostrideraintthatcoolnocausehesonfirejokes
y conseguimos un shell del usuario weston

# PRIVILEGE ESCALATION
Dos vectores:
1. Sudo: weston puede ejecutar como sudo /usr/bin/bees. Pero este ejecutable no tiene nada mas que un texto y no podemos escribir en el
2. En /opt/.dads_scripts hay un ejecutable python que es el responsable de mostrar frases lapidarias de vez en cuando en la pantalla
Abriendo el ejecutable vemos que ejecuta una instruccion system y que el propietario es cage. Podriamos utilizarlo como medio para aceder
al usuario cage?

## USUARIO CAGE
Dentro de /opt/.dads_scripts tenemos un fichero .files y dentro de este otro .quotes (/opt/.dads_scripts/.files/.quotes) que es donde se guardan las
frases lapidarias (quotes) que se muestran periodicamente en la pantalla. Es fichero lo utiliza el script de python *spread_the_quotes.py* para des
plegar las frases mediante una llamada al sistema os.system. Podemos aprovechar esta llamada para incluir un reverse shell payload en el fichero .quotes
Asi conseguimos un reverse shell con el usuario cage
Dentro de los backups de los correos encontramos una frase rara:
> haiinspsyanileph

Dentro del directorio /home/cage no se ve explicitamente donde esta el user flag. Para buscarlo como sabemos que tiene que empezar en "THM{", podemos
buscarlos con el comando
> $ grep -iRl "THM{" que nos dara el nombre de los archivos donde se den matches con "THM{" en este caso:
```
cage@national-treasure:~$ grep -iRl "THM{"
Super_Duper_Checklist
cage@national-treasure:~$ cat Super_Duper_Checklist 
1 - Increase acting lesson budget by at least 30%
2 - Get Weston to stop wearing eye-liner
3 - Get a new pet octopus
4 - Try and keep current wife
5 - Figure out why Weston has this etched into his desk: THM{M37AL_0R_P3N_T35T1NG}
```
Del directorio personal del usuario cage, podemos obtener su clave privada en /home/cage/.ssh/id_rsa
con esta clave nos podemos logear directamente en ssh. La clave no contiene passphrase.
# ROOT
