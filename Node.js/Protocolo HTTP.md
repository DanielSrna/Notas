Ya vimos a grandes rasgos, como [[Node.js]] hace funcionar el backend por medio de la API, y los endpoinst, ahora es momento de estudiar como funciona exactamente las peticiones que realiza el cliente hacía la API.
Está comunicación se hace por medio de un estándar predefinido, que usualmente tiene una estructura así:
```javascript
GET /usuarios HTTP/1.1  # Tipo de petición
Host: www.ejemplo.com  # Ruta del endpoint
User-Agent: Chrome  # Qué navegador usas
Content-Type: application/json  # Tipo de datos enviados
Authorization: Bearer token123  # Credenciales de acceso
```

## GET
Una petición GET tiene como objetivo realizar una lectura de datos, esto quiere decir que el cliente espera que la API le devuelva información a cambio de su petición. Por ejemplo, para recibir información sobre usuarios:
```javascript
GET /usuarios HTTP/1.1
```
## POST
Una petición POST tiene como objetivo que la API registre en la base de datos nueva información que el cliente envió por medio de la petición. Por ejemplo, un nuevo usuario:
```javascript
POST /usuarios HTTP/1.1
{
	"nombre": "daniel",
	edad: 27
}
```
## PUT
La petición tipo PUT tiene como objetivo que la API reemplace información ya existente, por una nueva que se envia por medio de la petición. Por ejemplo, actualizar la edad:
```javascript
PUT /usuarios/12412123 HTTP/1.1
{
	"nombre": "daniel",
	edad: 27
}
```
## DELETE
La petición DELETE tiene como objetivo hacer que la API elimine algo de la base de datos. Por ejemplo, el usuario que hemos creado, y modificado:
```javascript
PUT /usuarios/12412123 HTTP/1.1
```

> [!info] Sobre GET, PUT y DELETE
> Como pudiste notar, estás peticiones realizan en algunos casos, un trabajo de precisión, eso quiere decir, que se debe leer, modificar, o eliminar un dato en especifico, aquí entra en juego los identificadores, para poder saber que leer, modificar, o eliminar. Estos identificadores simplemente se pasan a la API, y ella los busca para hacer la tarea.

