Los métodos de Mongoose, son todas aquellas funciones que vamos a realizar con los datos de la base de datos. Ya sea cualquier tipo de CRUD, operaciones de login, verificación, JWT, etc. Todo aquello que interactue con la base de datos, debe de estar aquí.
# Creación
Los métodos van después de definir las reglas del esquema, pero antes de definir las operaciones **PRE**, o **POST**. Obviamente, también van antes de compilar el modelo, o exportarlo. Su estructura es:
```javascript
userSchema.methods.obtenerNombreCompleto = function() { 
	// 'this' apunta al documento específico de la DB 
	return `${this.nombre} ${this.apellido}`; 
};
```
# Tipos
Tenemos dos tipos de métodos, y ambos afectan a un documento, o todos los documentos de la colección.
#### Método normal
Este método es el que más vamos a usar, y tiene como finalidad realizar operaciones con un solo documento de la colección, ya sea buscar, crear, leer, eliminar, etc. Su construcción es tal como la especificamos antes:
```javascript
userSchema.buscarPorEmail = function(email) {
  return this.find({ email: new RegExp(email, 'i') });
};
```