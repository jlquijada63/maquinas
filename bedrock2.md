
# INTRODUCCION
Algunos servidores (ahora la mayoria) requieren que la comunicacion con el cliente se realice de forma segura, es decir que los datos vayan encriptados y
que el cliente o el servidor o ambos se autentifiquen mediante un certificado. En el certificado va incluida la clave publica del cliente, de forma que
el servidor utiliza esta clave para encriptar los datos y enviarselos encriptados al cliente, el cual los desencripta utilizando la clave privada que ha sido generada junto al certificado
# SCAN

```
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
4040/tcp  open  yo-main
9009/tcp  open  pichat
54321/tcp open  unknown
```
Tenemos los  siguiente:
1. En el puerto 80 corre un servidor nginx que nos redirige al puerto 4040 
2. En el puerto 4040 hay un webserver TSL
3. Hay un servidor que requiere conexion TSL, es decir con certificado 54321
4. El servidor localizado en el puerto 9009 emite los certificados

# PUERTO 9009

Utilizando netcat podemos conectar con el puerto 9009 donde obtenemos el certificado

> $ nc bedrock.thm 9009
Obtenemos la clave privada y la clave publica (certificado)

# 54321

En este puerto hay un servidor TSL y por tanto debemos acreditarnos mediante nuestra clave publica (certificado) y nuestra clave privada
Para acceder al servidor utilizamos openssl:
> $ openssl s_client -connect bedrock.thm:54321 -cert <clave-publica> -key <clave-privada>
  Esto nos da acceso a un CLI y con el comando:
  - user: obtenemos el username: 'Barney Rubble'
  - password: obtenemos el password: d1ad7c0a3805955a35eb260dab4180dd
  
# SSH
Con las anteriores credenciales intento logearme en ssh pero el password no me sirve ¿Estará codificado?
Pues no, se trata del password en plain text. El problema es que el usuario no es 'Barney Rubble' si no barney. Asi si puedo
> $ ssh barney@bedrock.thm
 
