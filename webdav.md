
# SCAN
Solo esta abierto el puerto 80 donde hay un servidor apache

# WEBENUM
Utilizando gobuster, solo aparece un directorio /webdav/ al entrar en este directorio nos pide acreditarnos

# WEBDAV
Webdab es un protocolo de comparticion de archivos (semejante a ftp, nfs, etc) que permite compartir archivos, subir archivos y descargar 
archivos. 
Tiene unas credenciales por defecto: 
```
user: wampp
pass: xampp
```
Entrando con estas credenciales en el directorio /webdav vemos que hay un fichero passwd.dav que contiene un hash:
>wampp:$apr1$Wm2VTkFL$PVNRQv7kzqXQIHe14qKA91
Las iniciales $apr1$ nos indican que es un hash md5 modificado que se utiliza en servidores apache. Intento desencriptar el hash con
JtR o hashcat pero no lo consigo

# HOW TO UPLOAD A FILE TO /WEBDAV? (MY METHOD)

There are several binaries we can use tu updload a file to the /webdav directory (v.e: cadaver), but we can use the terminal directly with 
the **curl** command.
I captured the http-request whit Burp when I authenticated with the default credentials (wampp:xampp). In the Burp capture I can see the Authorization header which is of type "Basic" and in base64 is 
>Authorization: Basic d2FtcHA6eGFtcHA=
That is credentials (user:passwd) concatenated with ":" and coded in base64

Finally I used curl to upload a file (text.txt):
>$ curl -H 'Authorization: Basic d2FtcHA6eGFtcHA=' -T test.txt http://IP_ADDRESS/webdav

and it works!!

if I refresh the web page I can see the file uploaded
In the same way I can upload a payload to obtain a reverse shell (pentestmonkey for instance)

# USER.TXT
I obtained my reverse shell and after stabilize it I get directly the user.txt

# PRIVILEGE ESCALATION
If I execute "sudo -l" I can see the user www-data can execute the command /bin/cat with root privileges through sudo. Then I can read
the root.txt directly with
>$ sudo /bin/cat /root/root.txt
