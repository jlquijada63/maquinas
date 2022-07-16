
# SCAN
```
PORT   STATE SERVICE VERSION                                                                                                                                 
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)                                                                            
80/tcp open  http    WebSockify Python/3.8.10  
```
Hay dos puertos abiertos:
- En el 22 esta ssh luego nos va a permitir comunicarnos con nuestra kali por ssh
- En el 80 hay un websocket implementado en python. Cuando intentamos cumunicarnos con el websocket, obtenemos un mensaje

El CTF nos deja acceder directamente a la maquina (mediante "split view") y nos dice que en el directorio /home/ubuntu/commited esta el repositorio
local git. Efectivamente asi es, hay un fichero comprimido "commited.zip" donde hay tres archivo:
1. Readme.md: no nos presta ninguna ayuda
2. main.py: Es un script de python para crear una base de datos, una tabla y añadir un usuario. En principio no aporta nada
3. .git: Un directorio tipico git donde probablemente estan los commits

Prefiero bajarme el fichero commited.zip a mi kali para analizarlo. Como tengo abierto ssh lo hago con scp: scp commited.zip kali@<ip_my_kali>:/home/kali

# ESTRUCTURA BASICA DE UN DIRECTORIO .git
Un directorio .git (repositorio local) tiene una estructura un poco peculiar y en principio puede parecer enrevesada. Contiene varios ficheros y directorios. Entre los mas destacados estan:
- HEAD: Contiene la rama (branch) activa:

```
──(kali㉿kali)-[~/boxes/committed/commited/.git]
└─$ cat HEAD
ref: refs/heads/master
```
En este caso es la rama master

- config: contiene la configuracion basica del repositorio
- objects: Es el directorio fundamental, donde se guardan los objetos. En git hay tres clases de objetos:
- 1 Commit: Es el objeto principal. Es una instantanea del estado de nuestro projecto en un instante del tiempo. Aqui se reflejan los cambios que
se han realizado en el projecto desde el ultimo commit y un mensaje informativo
- 2 Tree: Esta formado por otros arboles y ficheros
- 3 Blob: Son los ficheros

la peculariaridad es que los objetos se guardan en forma de un hash. En concreto se utiliza el algoritmo sha-1 que tiene 40 caracteres. Pues bien
los dos primeros caracteres del hash son los que dan nombre a la carpeta.
Es en realidad un galimatias, pero hay unas funciones que nos permiten leer e interpretar el fichero de forma mas comoda
1. git cat-file -t <hash>: Nos dice el tipo de objeto (tree, commit, blob)
2. git cat-file -p <hash>: Nos muestra el contenido del objeto
3. git log: Nos muestra todos los commits que se han realizado
4. git show: nos muestra el contenido del commit
  
