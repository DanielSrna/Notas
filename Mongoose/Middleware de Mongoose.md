Los middleware de [[Mongoose]] nos permiten realizar acciones antes y después de que se ejecute una operación CRUD dentro de la base de datos.
# Operaciones PRE
Las operaciones PRE, se ejecutan antes de que se ejecute una operación CRUD. El uso más extendido de este tipo de operación es para encriptar información, como tokens, o contraseñas. Su estructura es:
```javascript
modelSchema.pre("operaciónCRUD", function(next) {
	//Validaciones, o formateos
	//Al final se lanza un next() para confirmar, o un next(error) para cancelar
	//El error debe ser creado por nosotros mismos con New Error().
})
```
Ejemplo sencillo de una operación PRE que normaliza nombres:
```javascript
//Empezamos el Middleware, y en sus argumento la operación CRUD indicada
//y la función a operar
userSchema.pre("create", function(nex) {
	//Verificamos que exista el nombre
	if(this.name) {
		this.name = this.name.trim() //Eliminamos los espacios en blanco
		//Ahora colocamos la primera letra en mayuscula, y el resto minuscula
		this.name = this.name.charAt(0).toUpperCase() + this.name.slice(1).toLowerCase()
	}
	next() //Proseguimos con otro middleware, o con la operación CRUD
})
```
# Operaciones POST
Estás operaciones casi no se usan, puesto que no hay mucho que hacer después de que una operación CRUD ya fue realizada, además, si se intenta operar la base de datos con una operación CRUD desde aquí, sería mala idea, mejor se implementa una transacción en la operación original y ya.
Su estructura es:
```javascript
modelSchema.post("operaciónCRUD", async function(doc) {
	//Cambios en la base de datos luego de haber realizado la operación
	//con doc vamos a revisar lo que se ha cambiado, creado o eliminado
	//y con esa información vamos a hacer los cambios adecuados en otros lados
})
```
> [!info] Sobre su ubicación
> Estás operaciones deben ir justo después de haber terminado el esquema del modelo, y justo antes de exportarlo. Obviamente, dentro del mismo archivo.

