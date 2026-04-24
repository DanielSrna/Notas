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
- param: son los parámetros de la URL.
- cookie: es el valor de las cookie.
- header: aquí vienen los encabezados de la solicitud, estos son metadatos.
```javascript
const { body, query, param, cookie, header } = require("express-validator");
```
# Validators
Son todas las reglas que podemos implementar para cada validador, son realmente sencillas:
- **notEmpty():** permite validar que el valor del campo no este vació.
- **isLength({ min, max }):** permite validar que el valor del campo tiene un mínimo, y un máximo de caracteres.
- **isEmail():** permite validar que el valor del campo tiene un formato de email.
- **isNumeric():** permite validar que el valor del campo es numérico.
- isAlpha(): permite validar que el valor del campo sea solo letras.
- **isURL():** permite validar que el valor del campo tiene un formato de URL.
- **isUUID():** permite validar que el valor del campo tiene un formato de UUID.
- **isStrongPassword({ reglas }):** permite validar reglas para crear contraseñas.
- **isIn([ valor1, valor2, valorn ]):** permite validar que el valor del campo está en uno de los valores del array.
- **matches(regex):** permite validar por medio de expresiones regulares, es muy potente.
Por ejemplo:
```javascript
[
	body('nombre')
	.notEmpty()
	.withMessage('El nombre es obligatorio.'),
	body('password')
	.isLength({ min: 6 })
	.withMessage('La contraseña debe tener al menos 6 caracteres.'),
	body('email')
	.isEmail()
	.withMessage('El formato del email no es válido.'),
	body('edad')
	.isNumeric()
	.withMessage('La edad debe ser un número.'),
	body('ciudad')
	.isAlpha()
	.withMessage('La ciudad debe contener solo letras.'),
	 body('sitioWeb')
	 .isURL()
	 .withMessage('La URL no es válida.'),
	 param('id')
	.isUUID()
	.withMessage('El ID proporcionado no es válido.'),
	body('rol')
	.isIn(['admin', 'editor', 'lector'])
	.withMessage('El rol seleccionado no es válido.'),
	body('password')
	.isStrongPassword({ minLength: 8, minLowercase: 1 })
	.withMessage('La contraseña no es lo suficientemente segura.'),
	body('codigo')
	.matches(/^[A-Z]{3}[0-9]{4}$/)
	.withMessage('El código no cumple con el formato requerido.')
]
```
# Custom, y sanitización
Ahora vamos a aprender sobre como crear validaciones COMPLEJAS, y como sanitizar valores.
#### Custom
Con custom vamos a poder crear validaciones que puedan:
- Validar por medio del uso de la base de datos.
- Validar que dos datos de un mismo campo sean iguales. Por ejemplo, confirmar una contraseña dos veces.
- Validar por medio de lógica profunda. (poco usual)
La estructura es:
```javascript
[
	body("email")
		.isEmail()
		.withMessage("Debe ser un email valido")
		.custom(async valor => {
			const resultado = await User.find({ email: valor })
			if (resultado) { throw new Error("El email ya existe") }
			return true
		})
]
```
#### Sanitización
Cuando nos llegan los valores, es usual que el usuario sin querer deje ir un punto, o un espacio en donde no debe. Nosotros tenemos el trabajo de regular todas esas pequeñas fallas para que la información almacenada sea lo más fiable posible:
- trim(): eliminar los espacios al principio, y final.
- escpae(): convierte los caracteres HTML como <, y >, a sus entidades correspondientes ($lt, $gt), para evitar un ataque XSS.
- normalizeEmail(): normaliza el email, convirtiéndolo a minúsculas, y eliminando signos irrelevantes, como un punto al final.
Se usan así:
```javascript
[
	body("username")
		.trim()
		.escape()
	body("email")
		.isEmail()
		.normalizeEmail()
]
```