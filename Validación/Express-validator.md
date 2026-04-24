Se trata de uno de los paquetes más importantes para Express, y es que básicamente es la medida de seguridad más grande que podríamos implementar en nuestra API. Lo que hace es agarrar los elementos que vienen en el body, query, o param, y los analiza según las reglas que nosotros le indiquemos, para validar que los formatos sean seguros, y correctos.
# Instalación y uso
Instalarlo es sencillo:
```
npm install express-validator
```
La estructura de uso es sencilla:
```javascript
const { body } = require("express-validator");

exports.userValidator = [
	body.("nombre")
		.notEmpty().withMessage("El nombre es obligatorio")
		.isString().withMessage("El nombre debe ser texto")
	body.("edad")
		.optional()
		.isInt({ min: 1 }).withMessage("No puede ser negativo")
]
```
Ahora dentro de la ruta:
```javascript
const userValidator = require("../validators/userValidator")
const { validationResult } = require("express-validator");

app.post("/register", userValidator, (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
        return res.status(400).json({ errors: errors.array() });
    }
})
```
# Checkers
Son los contenedores en donde viene la información, en el ejemplo anterior usamos body, pero hay más:
- body: es el cuerpo de la petición, donde viene la mayoría de la información.
- query: son los valores de consulta que vienen en la URL.
- param: son los parametros de la URL.
- cookie: es el valor de las cookie.
- header: aquí vienen los encabezados de la solicitud, estos son metadatos.
```javascript
const { body, query, param, cookie, header } = require("express-validator");
```
# Validators
Son todas las reglas que podemos implementar para cada validador, son realmente sencillas:
- **notEmpty():** permite validar que el campo no este vació.
- **isLength({ min, max }):** permite validar que el campo tiene un mínimo, y un máximo de caracteres.
- **isEmail():** permite validar que el campo tiene un formato de email.
- **isNumeric():** permite validar que el campo es numérico.
- **isURL():** permite validar que el campo tiene un formato de URL.
- **isUUID**