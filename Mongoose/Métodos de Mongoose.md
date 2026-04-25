Los métodos de Mongoose, son todas aquellas funciones que vamos a realizar con los datos de la base de datos. Ya sea cualquier tipo de CRUD, operaciones de login, verificación, JWT, etc. Todo aquello que interactue con la base de datos, debe de estar aquí.
# Creación
Los métodos van después de definir las reglas del esquema, pero antes de definir las operaciones **PRE**, o **POST**. Obviamente, también van antes de compilar el modelo, o exportarlo. Su estructura es:
```javascript
userSchema.methods.obtenerNombreCompleto = function() { 
	// 'this' apunta al documento específico de la DB 
	return `${this.nombre} ${this.apellido}`; 
};
```
# Tipos
Tenemos dos tipos de métodos, y ambos afectan a un documento, o todos los documentos de la colección.
#### Método de instancia
Para poder hacer uso de este método, primero debemos de tener un objeto instanciado del modelo. Por ejemplo, traer un usuario de la base de datos, y verificar si aún está suscrito.
```javascript
userSchema.methods.esSuscripcionActiva = function() {
  const hoy = new Date();
  // Supongamos que tienes un campo 'fechaExpiracion' en el schema
  return this.fechaExpiracion > hoy;
};
```
Para usarlo en la ruta:
```javascript
const user = require("../userSchema");

router.post('/verificar-acceso', async (req, res) => {
  try {
    const { email } = req.body;

    // 1. Usamos el método ESTÁTICO para buscar al usuario
    const usuario = await Usuario.buscarPorEmail(email);

    if (!usuario) {
      return res.status(404).json({ mensaje: "El correo no está registrado" });
    }

    // 2. Usamos el método de INSTANCIA sobre el usuario encontrado
    if (usuario.esSuscripcionActiva()) {
      res.json({
        mensaje: "Acceso concedido",
        data: "Contenido ultra secreto para suscriptores"
      });
    } else {
      res.status(403).json({
        mensaje: "Acceso denegado",
        detalle: "Tu suscripción ha vencido"
      });
    }

  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

```