
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
