
# SCAN

```
┌──(root㉿kali)-[/home/kali/boxes/hacker-hacker]
└─# nmap -Pn -sV 10.10.0.89
Starting Nmap 7.92 ( https://nmap.org ) at 2022-08-15 02:39 EDT
Nmap scan report for 10.10.0.89
Host is up (0.084s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.48 seconds
```
# WEB SCAN
```
/upload.php           (Status: 200) [Size: 552]
/images               (Status: 301) [Size: 309] [--> http://10.10.0.89/images/]
/css                  (Status: 301) [Size: 306] [--> http://10.10.0.89/css/]    
/cvs                  (Status: 301) [Size: 306] [--> http://10.10.0.89/cvs/]    
/dist                 (Status: 301) [Size: 307] [--> http://10.10.0.89/dist/] 
```

# ENTRY POINT
Existe un sitio donde el anterior hacker ha guardado un payload con el que ha conseguido una "backdoor", que consiste en un parametro que nos
permite ejecutar RCE (Remote Command Execution):
> nope.thm/cvs/shell.pdf.php?cmd=

Podemos introducir aqui un payload y obtener un reverse shell (el payload debe ir codificado en URL)
Con esto conseguimos un reverse shell y entrando en el home del usuario lachlan conseguimos el user.txt
En este mismo directorio en el fichero .bash_history vemos que el antiguo atacante ha realizado un cambio de contraseña:
```
www-data@b2r:/home/lachlan$ cat .bash_history
cat .bash_history
./cve.sh
./cve-patch.sh
vi /etc/cron.d/persistence
echo -e "dHY5pzmNYoETv7SUaY\nthisistheway123\nthisistheway123" | passwd
ls -sf /dev/null /home/lachlan/.bash_history
```
# CLAVE
La clave esta en que el anterior hacker ha creado un fichero de tareas llamado persistence donde machaca los terminales. El unico binario que 
no utiliza el path completo es pkill y ademas esta en el path donde puede escribir el usuario lachlan con lo cual se puede cargar con un payload y
conseguir un reverse shell con root
