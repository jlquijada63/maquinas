

# SCAN
```
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
5044/tcp open  lxi-evntsvc
5601/tcp open  esmagent
```

# PORT 5061 (KIBANA)

Kibana es un plugin que permite monitorizar del forma grafica los logs y tiene una vulnerabilidad CVE-2019-7609 que consiste en la
posibilidad de ejecutar codigo remoto (RCE) basado en una vulnerabilidad llamada **Prototype Pollution** que consiste basicamente en
modificar el objeto en aquellos lenguajes de programacion (como javascript) basados en prototipos.
Existe un payload para explotar esta vulnerabilidad en: 
> https://github.com/LandGrey/CVE-2019-7609.git

Con este payload obtenemos un reverse shell que nos permite obtener de forma inmediata el user.txt



