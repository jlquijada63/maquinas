
# SCAN

```
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 fe:cc:3e:20:3f:a2:f8:09:6f:2c:a3:af:fa:32:9c:94 (RSA)
|   256 e8:18:0c:ad:d0:63:5f:9d:bd:b7:84:b8:ab:7e:d1:97 (ECDSA)
|_  256 82:1d:6b:ab:2d:04:d5:0b:7a:9b:ee:f4:64:b5:7f:64 (ED25519)
3000/tcp open  tcpwrapped
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
Hay dos puertos abiertos (22, 3000). Por el puerto 3000 se ejecuta un servicio desconocido denominado JPGchat

# OSINT
En github existe una pagina dedicada JPChat que esta gestionada por un usuario llamado *Mozzie-jpg* que contine dos ficheros:

1. README.txt: No contiene información relevante
2. jpchat.py: Es un script de python que se supone controla el servicio del JPGChat:

```
import os

print ('Welcome to JPChat')
print ('the source code of this service can be found at our admin\'s github')

def report_form():

        print ('this report will be read by Mozzie-jpg')
        your_name = input('your name:\n')
        report_text = input('your report:\n')
        os.system("bash -c 'echo %s > /opt/jpchat/logs/report.txt'" % your_name)
        os.system("bash -c 'echo %s >> /opt/jpchat/logs/report.txt'" % report_text)

def chatting_service():

        print ('MESSAGE USAGE: use [MESSAGE] to message the (currently) only channel')
        print ('REPORT USAGE: use [REPORT] to report someone to the admins (with proof)')
        message = input('')

        if message == '[REPORT]':
                report_form()
        if message == '[MESSAGE]':
                print ('There are currently 0 other users logged in')
                while True:
                        message2 = input('[MESSAGE]: ')
                        if message2 == '[REPORT]':
                                report_form()

chatting_service()
```
Este script es susceptible de RCE (Remote Command Execution) pues ejecuta (en su modo REPORT) el metodo **system** del modulo **os**
de python, cuya funcion es ejecutar un commando del sistema operativo
De esta manera podemos conseguir un reverse shell con el usuario wes

# ENUMERATI
