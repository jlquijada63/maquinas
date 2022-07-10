
# scanner

```
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp  open  http    Apache httpd 2.4.41 ((Ubuntu))
445/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
```
Como vemos el puerto 445 que habitualmente esta asignado al servicio SMB en este caso hay un servidor web apache. Se trataría por tanto de
de un ***virtual host*** del tipo puerto (port virtual-host)
