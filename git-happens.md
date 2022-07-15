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

# HACKING GIT
Git es un potente gestor de versiones que puede contener informacion valiosa para un hacker (credentials). El archivo .git esta formado fundamental
mente de objetos. Exiten tres tipos de objetos:
1. Commits: Es la base de git y es como si fuera una instantanea de nuestro projecto (el projecto cuyas versiones queremos gestionar con git).
2. Trees: Determina la distribución del projecto. A su vez puede estar formado de otros trees y blobs o formar parte de trees superiores
3. Blobs: Seria el equivalente a un fichero
git representa estos objetos en el directorio .git de forma muy peculiar, en forma de hashes (SHA1). De los 40 caracteres de un hash SHA1, git utiliza los dos primeros para dar el nombre al objeto 
```
# WGET
podemos descargarnos el directorio con:
```
wget -r http:<ip>/.git
```
Podemos buscar recursivamente con ***grep -iRL "password"*** y nos da que encuentra password en un fichero index.html. Pero ¿en cual?
