Helmet es un middleware extremadamente fácil de implementar, cuyo objetivo es crear una capa extra de seguridad en nuestra API. 
# Instalación y uso
Se instala mediante NPM:
```
npm install helmet
```
Ahora lo instanciamos, y lo ponemos en el archivo final del proyecto:
```javascript
const helmet = require("helmet")
app.use(helmet)

/*Rutas*/
```