Ahora, vamos a entender a grandes rasgos lo que vinimos a estudiar, y es principalmente, el desarrollo backend.

## API
Una API es un programa que nos permite recibir peticiones del cliente, procesarlas, y en respuesta a esta petición, realizar consultas a la base de datos, o interactuar con el cliente dándole permisos.
## Modelo cliente-servidor
Se trata de la forma en como vamos a crear nuestras APIs, y es directamente, como su nombre indica, como un intermediario entre el cliente, y el servidor. Pero, considere primero que:

1. El cliente es la aplicación frontend, en donde el usuario va interactuar, y enviar las peticiones a nuestra API.
2. El servidor es en donde se aloja nuestra API, y es básicamente una construcción de diferentes cosas, que incluye:
	-  Un base de datos.
	- La API.
	- Un bucket donde almacenar cosas.
	- Seguridad.

Entonces, cuando el frontend envía una petición a nuestra API, nosotros la vamos a manejar, y si es correcta, vamos a hacer diferentes cosas como agregar nuevos datos a la base de datos, enviarlos, modificarlos, darle acceso al usuario a cierto contenido, etc.

![[Diagram1.svg]]


> [!warning] NO LO CONFUNDAS
> Es importante considerar que una API no es técnicamente el servidor, sino, que el servidor es lo que contiene la API, y otras adiciones que lo hacen funcionar como un servidor.

## Base de datos
Aquí vamos a almacenar la información que nos proporcione el frontend, o el cliente. Está información puede ser además de almacenada, modificada, eliminada, o reemplazada, pero todo eso, se hace por medio de un lenguaje de programación especializado para una base de datos.

Por ejemplo:

 ![[Diagram2.svg]]

> [!info] ¿Entonces?
> Básicamente, [[Node.js]] juega el papel de intermediario en este caso, ya que es quien lidera como se manejan las interacciones entre el cliente, y el servidor.

