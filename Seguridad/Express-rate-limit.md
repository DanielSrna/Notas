Este paquete de NPM nos va permitir crear un limitador de intentos de usar nuestras rutas por parte del usuario. Es sencillo de usar.
# Instalación y uso
Se insta por NPM:
```
npm install express-rate-limit
```
Ahora, se configura en su archivo propio:
```javascript
const rateLimit = require('express-rate-limit');

// Configuración del limitador
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutos en milisegundos
  max: 100, // Límite de 100 peticiones por IP en el periodo de 15 minutos
  message: "Demasiadas peticiones desde esta IP, por favor intente de nuevo después de 15 minutos",
  standardHeaders: true, // Devuelve información del límite en los headers de respuesta
  legacyHeaders: false, // Deshabilita los headers de límite antiguos
});

module.exports = limiter
```
Finalmente, tenemos dos opciones. La primera es aplicar el limitador a todas las rutas, y para eso lo importamos en el archivo final del proyecto:
```javascript
const limiter = require("./services/limiter.js")

//Podemos aplicarlo a todas las peticiones:
app.use(limiter)

/*Rutas*/
```
La otra es aplicarlo a una sola ruta:
```javascript
const limiter = require("./services/limiter.js")

router.post("/api/login", limiter, (req, res) => {
	/*Lógica*/
})
```