
# SCAN

```
PORT      STATE SERVICE      VERSION                                                                                                                         
22/tcp    open  ssh          OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)                                                                    
80/tcp    open  http         nginx 1.18.0 (Ubuntu)                                                                                                           
4040/tcp  open  ssl/yo-main?                                                                                                                                 
9009/tcp  open  pichat?                                                                                                                                      
54321/tcp open  ssl/unknown 
```
Hay un servidor web nginx en el puerto 80 que nos redirige a un servidor "hecho a medida" TSL en el puerto 4040
