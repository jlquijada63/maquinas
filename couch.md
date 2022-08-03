
# scan
```
PORT     STATE SERVICE
22/tcp   open  ssh
5984/tcp open  couchdb
```
En el puerto 5984 funciona un gestor de base de datos **couchdb**. Se trata de una base de datos no relacional de apache. Las caracteristicas
fundamentales son:
1. Guarda los datos en formato json
2. Utiliza la API REST para realizar las operaciones de la base. Por ejemplo para recuperar una consulta utiliza el metodo GET o para crear
una nueva base de datos utiliza PUT
3. Aunque se pueden hacer operaciones desde la CLI con curl, para facilitar el manejo, tiene una GUI que se llama **Futon** a la que se accede
desde http://ip:2984/_utils. Aqui apararece una tabla con las bases de datos existentes y para ver el contenido de una base de datos lo unico
que tenemos que hacer es picar en el _ui. El contenido se puede ver en formato json o en un formato mas legible. Si hacemos esto en la base
***secret*** nos encontramos con las credenciales:
> atena:t4qfzcc4qN##

con estas credenciales podemos log on en ssh y acceder facilmente al user.flag


