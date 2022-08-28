
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
