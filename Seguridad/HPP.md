Este paquete nos permite limpiar las URL, y evitar que el usuario envíe una URL con querys, o parámetros repetidos miles de veces, o algo así.
# Instalación, y uso
Se instala con NPM:
```javascript
npm install hpp
```
Ahora se instancia de la siguiente forma, no de otra:
```javascript
const hpp = require('hpp');

//Middleware que parsea las peticiones
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Agrega el middleware de hpp
app.use(hpp());

// Rutas
```