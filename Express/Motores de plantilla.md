Los motores de plantilla son herramientas que nos ofrece NPM para [[Express]], en donde podremos renderizar una pagina web sencilla, y alimentarla con información de la base de datos, o del mismo cliente.
# ¿Cuál usar?
Bueno, resulta que hay varias, pero por ahora vamos a aprender **EJS** ya que es como la más usada y documentada.
# Instalación y configuración
Instalar EJS es sencillo ya que es un paquete de NPM:
```bash
npm install ejs
```
Ahora, para configurarlo, debemos de importarlo obviamente, como cualquier otro paquete, y luego le decimos a Express que lo use, y en donde encontrar su carpeta:
```javascript
require("ejs");
app.set("view engine", "ejs");
app.set("views", path.join(__dirname, "views"));
```
# Uso
Para usar es tan sencillo como dirigirnos a la carpeta "views" y crear un archivo, o varios archivos con extensión ".ejs".
![[Pasted image 20260417175240.png]]
Ahora lo metemos en la ruta que va renderizar esa vista:
```javascript
router.get("/", (req, res) => {
	//Recuerda siempre armar esta ruta relativa a la carpeta views
  res.render("./home/index");
});
```
Ahora, cuando entremos a esa ruta, vamos a poder ver renderizada la página alojada en ese path.
# Estructura de un proyecto con EJS
EJS es básicamente un lenguaje de programación que mezcla HTML, y elemento dinamicos que se puede alimentar con información de la API, por ejemplo:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Experimentación con EJS</title>
</head>
<body>
    <h1><%= title %></h1>
</body>
</html>
```
Ese elemento extraño `<%= title %>` es básicamente el elemento dinámico de EJS que podemos alimentar desde la API:
```javascript
router.get("/", (req, res) => {
  res.render("./home/index", { 
    title: "Home" 
  })
});
```
Entonces ahora cuando el usuario haga una petición a esa ruta, se va renderizar la página, y el titulo va ser "Home". Esto se debe a que title es como una variable que se llena en la API.
# Sintaxis
La sintaxis gira en torno a esos elementos extraños: `<%=...%>`, lo que hay dentro de ellos no hace parte de la sintaxis en si mismo, sino, que es el nombre de la variable que viene también de la API. Estas cosas se denominan como **Salidas** ya que sirven como salidas de datos que vienen de la API hacía el cliente.
#### Salida con escape <%=...%>
Este tipo de salida es la más segura, ya que no permite al usuario interactuar con estás variables. Es más, es tan segura y útil, que vamos a pasar de largo la salida insegura, olvídate de ella.
#### Salida de scripting
Aquí se nos permite jugar un poco con las posibles salidas de información de EJS, y nos da un plus de dinamismo.
**Salida condicional:** está salida nos permite condicionar texto, por ejemplo, si ponemos que en la condición dice que si el usuario es Admin, el damos una bienvenida apropiada, sino, una generica. (Con bienvenida me refiere al texto de salida, como "Hola Admin", no a una pantalla distinta, o algo más complejo)
```html
<% if (condición) { %>
	//Código de la condición true
<% } else { %>
	//Código de la condición false
<% } %>
```
**Bucles**: Sirve para cuando tenemos muchos elementos iguales, y los queremos renderizar todos, aunque es poco usual hacer uso de esto.
```html
<% for (let i = 0; i < items.length; i++) { %>
   //Código
<% } %>
```
#### Comentarios <%# … %>
¿Por qué habrías de hacer comentarios dinamicos para el cliente que solo puede verlos con la herramienta de inspeccionar? Quien sabe, pero ahí está la posibilidad.
# Partials
Supongamos que nuestra web es especialmente sencilla, y en todas sus vistas, se comparte algunos elementos iguales de la interfaz. Para no escribir en todas las vistas lo mismo, usamos partials, que permite ir a archivos prefabricados para llenar las vistas. Lo primero es crear una carpeta "partials":
![[Pasted image 20260417180621.png]]
En footer:
```html
<footer>
    <p>&copy; 2023 Mi Aplicación. Todos los derechos reservados.</p>
</footer>
```
en header:
```html
<header>
    <h1>
        <%- pageTitle %>
    </h1>
</header>
```
Ahora en cualquier vista:
```html
<%- include('../partials/header', { pageTitle: 'Perfil de Usuario' }); %>

<main>
    <h2>Información del Usuario</h2>
    <p>Nombre: <%= name %></p>
    <p>Edad: <%= age %></p>
</main>

<%- include('../partials/footer'); %>
```
# Opciones de compilado
Además de la información que se adjunta para las salidas de nuestras plantillas, también se pueden incluir opciones para cambiar un poco el comportamiento de la plantilla renderizada, se escriben así:
```javascript
router.get('/', (req, res) => {
    res.render("./users/users", {
        name: "John Doe",
        age: 30,
    }, 
    {
		    //Aquí van las opciones
    });
});
```
Las opciones disponibles son:
- `cache: boolean`: si es true, la plantilla se guarda en caché para mejorar el rendimiento de cargado de páginas ya cargadas. (Solo para producción)
- `debug: boolean`: si es true, la función generada por la plantilla en EJS, es mostrada en la consola del servidor, así podremos ver si hay algo malo con la plantilla.
- `rmWhitespace: boolean`: elimina todos los espacios en blanco “seguros” de eliminarse. Esto ayuda a compactar el HTML.
- `async: boolean`: permite que la plantilla se renderice en una función asíncrona. Esto sirve si la información que se inserta en la plantilla es un poco pesada.

> [!warning] IMPORTANTE
> Recuerda que EJS, o cualquier otro motor de plantilla, no debería de renderizar cosas complejas, sino, un front end sencillo que pueda interactuar con la API rápidamente. Créeme, si sientes que es sencillo de usar, y podrías escalar todo esto en una app super grande, puedes hacerlo, pero es mucho más sencillo con herramientas front end.
