# SCAN
Solo hay un puerto abierto que es el 80 donde hay un servidor ngnix. Nos dice que hay un directorio /.git
```
PORT   STATE SERVICE
80/tcp open  http
| http-git: 
|   10.10.76.126:80/.git/
|     Git repository found!
|_    Repository description: Unnamed repository; edit this file 'description' to name the...
|_http-title: Super Awesome Site!
```
# WGET
podemos descargarnos el directorio con:
```
wget -r http:<ip>/.git
```
Podemos buscar recursivamente con ***grep -iRL "password"*** y nos da que encuentra password en un fichero index.html. Pero ¿en cual?
