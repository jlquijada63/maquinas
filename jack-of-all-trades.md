
# SCAN
Hay dos puertos abiertos:
1. 80: Aqui hay un servidor openssh
2. 22: Servidor apache

Es decir el mundo al reves

# FIREFOX
Lo primero que hay que hacer para poder ver la pagina es cambiar la configuracion de firefox porque por defecto no nos deja ver los "Well Known"
puertos en el navigador porque son puertos restringidos (ej: el puerto 22 estaria restringido a ssh)
Para cambiar la configuracion:
1. about:config
2. En el buscador hay que modificar el siguiente parametro

![image](https://user-images.githubusercontent.com/70599089/179743096-6be0f2e8-1159-4d5a-add6-f253e64f5b33.png)

y poner el numero de puerto a 22
(si no existe hay que crearlo)
