La carpeta controller, o el concepto de controlador en si mismo, dentro de [[Express]], representa el primer paso de la separación de la lógica de una ruta, siendo este, el lugar en donde se almacena toda la lógica detrás de la ruta en especifico.
# Sintaxis
Es bastante sencillo, esencialmente, vamos a crear funciones que van a almacenar toda la lógica de la ruta con la siguiente estructura:
```javascript
/*
* @desc controlador que saluda al usuario
* @route GET /datos
* @access Public
*/
exports.saludar = (req, res) => { 
	res.json({ 
	message: "Bienvenido a la ruta de datos", 
	status: "success" 
	}); 
};
```
Como podemos notar, hemos hecho un comentario informativo sobre la naturaleza de ese controlador. Ahora, veamos como se consume:
```javascript
const express = require("express");
const router = express.Router();
const controller = require("./datos.controller.js");

router.route("/")
	.get(controller.saludar())

module.exports = router;
```
> [!info] Sobre su estructura
> Es importante llamar al archivo que contiene el controlador con una extensión ".controller", así es más fácil de diferenciar dentro de la carpeta de la ruta.
