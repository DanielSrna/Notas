Este manejador se utiliza con el paquete **[[Express-validator]]**, y tiene como finalidad manejar los errores de validación.
```javascript
const { validationResult } = require('express-validator');

const validate = (req, res, next) => {
    const errors = validationResult(req);
    
    if (!errors.isEmpty()) {
        // Creamos un error estándar de JS
        const error = new Error("Error de validación de datos");
        
        // Adjuntamos el status que tu manejador busca (400 para errores de cliente)
        error.status = 400;
        
        // Adjuntamos el array de errores detallados de express-validator
        error.errors = errors.array(); 
        
        // Lo enviamos al manejador global
        return next(error);
    }
    
    next();
};

module.exports = validate;
```
# ¿Cómo se usa?
Es sencillo, debes ir al archivo de las rutas en donde vas a aplicar validación de campos, y entones:
``` javascript
const validate = require("../validate.js")

const middlewaresRegistro = [
    body('email').isEmail().withMessage('Email inválido'),
    body('password').isLength({ min: 6 }),
    validate // Tu interceptor de errores va aquí, al final del grupo
];

// La ruta queda súper limpia:
router.post('/registro', middlewaresRegistro, (req, res) => {
    // Tu lógica normal...
});
```