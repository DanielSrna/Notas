JWT es una herramienta que nos va permitir hacer 2 cosas:
1. Mantener la sesión abierta del usuario.
2. Administrar el acceso a rutas por roles.
Primero que todo se instala así:
```bash
npm install jsonwebtoken
```
## Creación de tokens
Supongamos que el usuario hace login, lo que nosotros debemos hacer es:
```javascript
const jwt = require("jsonwebtoken");

// Creamos las contraseñas que necesita JWT para crear los tokens
const JWT_SECRET = process.env.JWT_SECRET || "mi_contraseña_secreta";
// El tiempo de vida del token
const JWT_EXPIRES_IN = process.env.JWT_EXPIRES_IN || "2h";
```
Ahora en la ruta:
```javascript
app.post("/login", (req, res, next) => {
	try {
		//**Lógica de autenticación**
	
    // Si todo es correcto, genera un token JWT
    const token = jwt.sign(
        { id: usuario._id, rol: usuario }, 
        JWT_SECRET, 
        { expiresIn: JWT_EXPIRES_IN }
    );

    // Devuelve una respuesta exitosa con el token
    res.status(200).json({
        message: "Inicio de sesión exitoso",
        token: token
    });
	} catch (e) {
		//Manejo de errores
	}
})
```