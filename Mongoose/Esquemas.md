El chiste principal de Mongoose, es que permite crear esquemas, que se tratan esencialmente de una serie de reglas que le ponemos a las colecciones, para que los documentos alojados en ella sigan todos las mismas reglas, y podamos encontrar completa regularidad en las estructuras de estos.
Sin los esquemas, almacenar información en Mongoose sería un caos, podría almacenar un JSON hell, y al lado almacenar una dirección, etc.
# ¿Qué se debe considerar al iniciar un esquema?
Bueno, lo que debemos considerar, aunque suene algo abstracto, son todos los campos que necesitamos del documento, por ejemplo:
*Si nuestra colección es de usuario, ¿Qué información útil para nuestro proyecto deberíamos requerir? ¿Email, contraseña, nickname, edad, comentarios, etc?*
Depende más bien de hasta donde quieres llevar las funcionalidades de tu proyecto.
# Estructura de un esquema
Es MUY SENCILLO, literalmente un esquema puede comenzar así:
```javascript
const mongoose = require("mongoose")
const nombreSchema = new mongoose.Schema({
	//Aquí van nuestras reglas para el esquema
})
```
# Reglas
Esto es lo único tedioso, aunque ya te digo que son fáciles de recordar, y varias reglas se copian, y pegan para algunos campos que son simplemente texto, o números sin más.
- **Type:** obliga a que el campo deba cumplir con un tipo de dato en especifico.
- **min, max:** obliga a que un campo número no sea menor, o mayor a cantidades especificas.
- 