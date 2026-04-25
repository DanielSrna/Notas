En este caso tenemos 2 módulos en uno que logran realizar la tarea de encriptar, o comparar contraseñas de forma segura en nuestro proyecto. Lo primero es instala **bcrypt.js:**
```bash
npm install bcryptjs
```
Ahora nos dirigimos al archivo en donde tenemos el esquema, o molde principal de Mongoose para los usuarios.
## Encriptación
Para encriptar vamos a utilizar una función **PRE**, es un [[Middleware de Mongoose]] que permite realizar acciones antes de que se registre el usuario, y en nuestro caso, es encriptar la contraseña antes de que sea guardada en la base de datos.
```javascript
registroSchema.pre("save", async function (next) {
  // Solo hashear si la contraseña ha sido modificada (o es nueva)
  if (!this.isModified("password")) return next();

  // Hasheamos, considerar que el 10 puede ser una variable de entorno
  this.password = await bcrypt.hash(this.password, 10);
  next();
});
```
Perfecto, eso es todo, no se necesita más para encriptar la contraseña.
> [!info] SALTS
> El número de salts (en este caso 10), indica cuantas veces se va encriptar la misma contraseña para una mayor seguridad. Recomiendo usar una variable de entorno.
## Comparación
Ahora para el proceso de login, debemos de comparar la contraseña que nos ofrece el usuario, y 