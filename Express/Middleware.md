En [[Express]] existe un concepto llamado "middleware" que se trata básicamente de una función que se ejecuta antes de que la petición sea procesada por el endpoint, y sirve para:
- Autorizar, y autenticar un usuario.
- Manejar niveles de seguridad.
- Permisos.
Si tuviera que ponerlo en una analogía, podría ser como la empresa de mensajería cuando envías un paquete, ellos no solo se limitan a entregar el paquete, sino, que hacen controles sobre el contenido enviado, para garantizar la seguridad, y que sea un movimiento legal de bienes.
## ¿Cómo se usa?
Nos vamos a vale de una estructura muy parecida a una ruta:
```javascript
const miMiddleware = (req, res, next) => {
	//Operaciones
}
```
Lo único que ha cambiado es que ahora uno de los operadores es "next", y su función es hacer continuar el flujo de la petición hacía el endpoint, si todo salió bien, sino, simplemente se usa "res", y se devuelve al cliente un error.
Obviamente no lo podemos usar tal cual, ya que no apunta exactamente a una ruta en especifico, sino, que es más bien una función, vamos a usar recursividad, por ejemplo:
```javascript
// Este ejmplo es uno de un middleware de autorización
const comprobarRol = (rolPermitido) => {
	return (req, res, next) => {
		// Si la función de busqueda manda la coincidencia como false
		// la pasamos como true para devolver el error, o continuar
		// hacía el endpoint
		if (!rolPermitido.includes(req.user.rol)) {
			res.status(403).send("acceso denegado")
		} else {
			next()
		}
	}
}
```
Bueno, y ahora, ¿Cómo rayos usamos eso?
```javascript
app.get("/admin", comprobarRol(["admin"]), (req, res) => {
    res.send("Bienvenido al panel de admin");
});
```
> [!info] Sobre req.user.rol
> Como puedes notar hemos consumido un dato del req de la petición que no es ni query, param, o body. Esto se debe a que en el mundo de los middleware es usual que un middleware pueda agregar información nueva al req, y que el siguiente middleware lo utilice como quiera.
> En este caso en concreto, podríamos decir que un middleware de autenticación se ejecuto antes que el del ejemplo, y ha buscado el rol del usuario en la DB, y luego lo metió  al req así:
> `req.user = userDB;`
## Alcance de un middleware
Un middleware no siempre se mete dentro de un endpoint, y funciona exclusivamente ahí, sino, que puede tener diferentes alcances dentro de nuestro proyecto.
#### Nivel global
Usualmente utilizado para crear algo llamado "middleware para manejo de errores", se utiliza antes de absolutamente todas las rutas:
```javascript
app.use(miMiddleware);
```
#### Nivel de rutas
Se utiliza usualmente para las rutas que requieren de autenticación, y autorización. Es una forma de englobar varias rutas que tengan algo en común:
```javascript
// Middleware para todas las rutas que empiezan con /admin
app.use('/admin/*', verificarAdmin);

// Middleware para múltiples rutas específicas
app.use(['/perfil', '/configuracion'], middlewareSeguridad);

// Middleware por extensión de archivo
app.use('*.json', middlewareParaJSON);
```
> [!warning] CUIDADO
> Lo mejor es siempre utilizar la primera y segunda forma, ya que son más sencillas de comprender, y no elevan la complejidad del proyecto.
#### Nivel de petición
Este es algo peligroso, ya que se aplica a todos los endpoints que tenga una petición en concreto.
```javascript
// Solo para POST y PUT
app.use((req, res, next) => {
  if (['POST', 'PUT'].includes(req.method)) {
    console.log('Middleware para métodos de escritura');
  }
  next();
});
```
#### Nivel de endpoint
Es el que vimos en el ejemplo de un principio, es el más sencillo de usar:
```javascript
app.get("/admin", autorization(["admin"]), (req, res) => {
	// operaciones
})
```
