[[Express]] tiene una forma muy elegante para manejar las rutas de una API, y se consigue mediante el manejo de una estructura predefinida. Además, hay que destacar la estructura de archivos que se debe manejar.
## Como implementar una ruta
La sintaxis es sencilla:
```javascript
app.método("path", (req, res) => {});
```
En donde:
- app: es la instancia de Express para empezar a manejar la ruta.
- método: es el método GET, POST, PUT, o DELETE.
- "path": es el enlace que activa la ruta, por ejemplo: "/estudiantes/hombres".
- arrow function: es lo que se quiere hacer cuando se active esa ruta.
- res, y res: son las dos formas de manejar la petición, y respuesta de la ruta. 
## Recepción, y respuesta
Dentro de una ruta lo más fundamental es saber manejar la petición junto con su contenido, y finalmente responder.
### REQ
Con este parámetro vamos a poder obtener toda la información que pueda acompañar a la ruta. Se descompone en 3 elementos principales:
##### Query
Los Query son elementalmente variables que se añaden al path que lleva a nuestra ruta:
```javascript
https:://ejemplo.com/ruta?clave1=valor1&clave2=valor2
```
Para consumir esas variables:
```javascript
app.post("/ruta", (req, res) => {
	const {clave1, clave2} = req.query
})
```
> [!info] Sobre su función
>Los valores de una Query son valores que nosotros ya debemos saber, porque principalmente se usa para seleccionar alguna opción. Por ejemplo:
>
>*Si tuviéramos una ruta dinámica que  puede leer, y eliminar un valor, podríamos programar que cuando el Query "clave1" tenga como valor "1" sea búsqueda, o si su valor es "2" es eliminación.*
##### Parámetros
Como su nombre indica, funciona para realizar búsquedas dinámicas dentro de la base de datos utilizando parámetros de búsqueda, esta viene así:
```javascript
https:://ejemplo.com/usuarios/parametro
```
Para consumirla:
```javascript
// Ruta con parámetro de ruta ":id"
app.get('/usuarios/:id', (req, res) => {
    // Accedemos al parámetro con req.params
    const idUsuario = req.params.id;
    res.send(`El ID del usuario es: ${idUsuario}`);
});
```
> [!info] Sobre su función
> En este caso no es necesario saberse el nombre del parámetro, solo su ubicación.
>  
> Cabe mencionar que tanto los parámetros, como los Query, son muy útiles juntos, ya que podemos realizar búsquedas de información con filtros, u otras opciones.
##### Body
El body es básicamente el elemento principal de REQ, ya que aquí es donde está la información principal de la petición. Es importante mencionar, que el body en el cliente, se debe de llenar con un formulario en donde a cada valor que trae el body se le ponga un nombre definido. Así se consume:
```javascript
app.post("/usuarios/ingresar", (req, res) => {
		const nombreUsuario = req.body.nombre
		const emailUsuario = req.body.email
		const resultado = collection.insertOne({nombreUsuario, emailUsuario})
		res.status(200).send("Usuario", resultado, "ingresado con éxito")
})
```
En este caso esperamos al menos dos valores llamados así:
```javascript
{
	nombre: "Daniel",
	email: "monokronia@gmail.com"
}
```
> [!info] Sobre su función
> Como podemos notar el body es parecido a un objeto de JavaScript, en donde podemos descomponerlo según sus atributos, y utilizar la información que viene almacenada en ellos.
>
>Este formato es llamado JSON.
### RES
Cuando ya hemos leído toda la información de la petición, la hemos manejado, y estamos listos para responder, es cuando finalmente va terminar el ciclo de vida de la ruta. Es importante mencionar que dentro de la ruta, la respuesta se debe manejar así:
```javascript
try {
	res.status(200).send("Respuesta éxitosa!")
} catch (e) {
	res.status(500).send("Hubo un error!", e)
}
```
Por ejemplo:
```javascript
app.get('/usuarios/:id', (req, res) => {
	try {
	    const idUsuario = req.params.id;
	    res.status(200).send(`El ID del usuario es: ${idUsuario}`);
	} catch (e) {
		res.status(500).send("Hubo un error!", e)
	}
});
```
> [!info] Sobre su función
> Es importante siempre usar Try, y Catch, ya que la respuesta es siempre el final definitivo de la ruta. 
>  
> Cabe mencionar que como es el final definitivo de una ruta, se debe poner siempre al final, todo lo que se ponga después ya no se ejecuta.
## Estructura básica de una ruta
Las rutas contienen toda la lógica que debe de manejarse dentro del endpoint, pero como se debe de estar considerando, esto es demasiado para meterlo en un solo lugar, por eso, se hace una separación modular para reducir la complejidad:
![[Diagram2.svg]]

> [!warning] CUIDADO
> No siempre toda la lógica de una ruta se almacena dentro del controller, ya que existen también los eventos, y estos se alojan más bien dentro de los servicios. Aunque si, es cierto que al final toda la lógica se transporta desde controller hasta el archivo router, pero eso no quiere decir que todo pase ahí.