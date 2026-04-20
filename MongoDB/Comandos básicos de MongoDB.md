Antes de empezar a crear, editar, leer, o eliminador datos en [[MongoDB]], es importante entender como funciona la herramienta en la terminal. En este caso, este tutorial es exclusivo si usted está corriendo la base de datos de manera local.
Para iniciar, primero veamos como encender MongoDB en nuestra terminal, es tan sencillo como escribir:
```bash
mongosh
```
# Ayuda y soporte
Veamos algunos comandos que nos pueden ayudar a recordar como usar otros comandos, o como manejar bien una colección.
#### db.help()
Este comando nos ayuda a recibir una GRAN lista de comandos.
```bash
db.help()
```
También se puede usar así:
```bash
db.<nombre_colección>.help()
```
Entonces recibimos asistencia con los comandos que podemos usar sobre la colección.
# Bases de datos, y colecciones
Ahora veamos comandos que nos ayudar a precisamente explorar, o crear bases de datos, y colecciones.
#### Listado de bases de datos
Para ver las bases de datos actuales:
```bash
show dbs
```
#### Cambio y creación de bases de datos
Para cambiar de una base de datos a otra, o crea una nueva:
```bash
use <nombre_base_de_datos>
```
#### Listado de colecciones
Para ver las colecciones actuales:
```bash
show collections
```
#### Creación de colecciones
```bash
db.createCollection("miColeccion")
```
# Usuarios
Ahora veamos comandos que nos pueden ayudar con la gestión de usuarios.
#### Ver los usuarios actuales
```bash
show users
```
#### Ver los roles
```bash
show roles
```
# Monitoreo de operaciones
Para revisar las operaciones hechas en la base de datos, podemos usar:
```
show profile
```
# Cierre de sesión
```
exit
```