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
## Autorización
Ahora cuando el usuario quiera hacer uso del token para autorizarse poder entrar a una de las rutas protegidas:
```javascript
const decoded = jwt.verify(token, process.env.JWT_SECRET);

const autorizar = (roles) => {
  return (req, res, next) => {
	  //Obtenemos el token
    const token = req.headers.authorization?.split(" ")[1];
    //Lo decodificamos
    const decoded = verificarToken(token);
    if (!decoded) return next(createError(401, "No autorizado"));
    //Si la decodificación sale bien, comparamos los roles
    if (roles.length && !roles.includes(decoded.role)) return next(createError(403, "Acceso denegado"));
    //Si el usuario tiene el rol permitido, lo dejamos pasar e interactuar
    //con la ruta
    req.user = decoded;
    next();
  }
}
```
# Rotación de tokens
La rotación de tokens es una practica muy útil ya que permite tener un plus de seguridad muy alto.
1. El usuario hace login, y se le entregan dos tokens: AccesToken, y RefreshToken.
2. Con AccesToken va explorar todas las rutas protegidas por permisos basados en rol.
3. Con RefreshToken se va actualizar su AccesToken por uno nuevo, ya que este se vence muy rápido. 
4. El punto 3 se logra ya que RefreshToken se guarda en la DB apenas es creado, y cada que el usuario se le acaba la sesión, se redirige a una ruta en donde se verifica su RefreshToken, y se le da un nuevo AccesToken.
Esto hace que todo sea muchísimo más seguro.