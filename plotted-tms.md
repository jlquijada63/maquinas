
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

