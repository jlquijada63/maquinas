
# SCAN
```
PORT   STATE SERVICE VERSION                                                                                                                                 
21/tcp open  ftp     vsftpd 3.0.3                                                                                                                            
22/tcp open  ssh     OpenSSH 8.0 (protocol 2.0)                                                                                                              
80/tcp open  http    Apache httpd 2.4.37 ((centos))
Service Info: OS: Unix
```
# FTP SCAN

Anonymous not seems to be avalaible

# WEB ENUM
Hay un directorio **backups** donde hay un fichero comprimido *backup.zip* que contiene una clave publica (priv.key) y un archivo encriptado pgp: CustomerDetails.xlsx.gpg que parece contener un excel para desencriptar el fichero gpg, se hace en 2 pasos
1. Importar la clave privada
> $ gpg --import priv.key
2. Desencriptar
> $ gpg -d -o output.xlsx CustomerDetails.xlsx

In the file decrypted is a spreadsheet of excel with a table where the columns are: user, username, password, credit card number, cvs
```
Customer Name,Username,Password,Credit card number,CVC
Par. A. Doxx,paradox,ShibesAreGreat123,4111 1111 4555 1142,432
0day Montgomery,0day,OllieIsTheBestDog,5555 3412 4444 1115,642
Muir Land,muirlandoracle,A11D0gsAreAw3s0me,5103 2219 1119 9245,737
```


