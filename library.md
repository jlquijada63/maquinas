
# SCAN
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
```
`
# PUERTO 80
Tiene un formulario para mandar comentario pero realmente no hace nada (es un end). Lo unico que nos puede valer es que hay
un usuario que se llama "meliodas" que ha mandado 3 comentarios

# HYDRA
Probamos un ataque de fuerza bruta utilizando como usuario "meliodas". Inmediatamente obtenemos el password:
```
