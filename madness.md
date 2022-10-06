
##scan
```
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

```
##WEB
En la url http://madness.thm podemos ver la pagina inicial del servidor apache a la que se le ha añadido una imagen: thm.png que no se muestra. Analizando la imagen en principio parece que se trata de una imagen en formato png, pero en este formato no se visualiza. Probamos a cambiar el formato cambiando el "magic number" con hexeditor y vemos que efectivamente cambiando el magic number por FF D8 FF E0 00 10 4A 46  que es el magic number de una version del formato jpg si se visualiza

file:///home/kali/maquinas/madness/thm.jpg![image](https://user-images.githubusercontent.com/70599089/194276155-07c36fce-0622-4cb8-9713-2e147f22f748.png)
Es decir hay un directorio oculto que es:
/th1s_1s_h1dd3n

Si hacemos un fuzz a 
>http://madness.thm/th1s_1s_h1dd3n/?secret= vemos que cuando introducirmos 73 nos aparece una pagina con una palabra rara:
>y2RPJ4QaPF!B
