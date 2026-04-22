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
- **minLength, maxLength:** obliga a que un campo de texto no supere un número de caracteres, o sea menor a lo especificado.
- **match:** es posiblemente la regla más poderosa, y permite validar usando regex.
- **enum:** sirve para que el valor de un string solo sea valido si está dentro de una lista ya definida de palabras.
- **unique:** para definir como único un campo dentro del documento, esto a su vez crea un índice con ese campo en la colección.
- **required:** para hacer el campo obligatorio.
- **default:** si el campo viene en blanco, pues se pone un valor por defecto.
- **trim:** para eliminar espacios en blanco al principio y al final.
- **lowercase:** para poner el valor del campo en minúsculas.
#### Tipos de datos
Los tipos de datos que se pueden definir son:
- **String:** Para texto.
- **Number:** Para números enteros o decimales.
- **Date:** Para fechas y horas.
- **Boolean:** Para valores `true` o `false`.
- **Array:** Para almacenar listas de valores.
- **ObjectId:** Se usa comúnmente para referenciar documentos de otras colecciones.
- **Mixed:** Para datos de cualquier tipo. Se usa con precaución, ya que deshace parte de la rigidez de los schemas.
# Subdocumentos
Son documentos anidados sin más, se pueden usar así:
```javascript
//Subdocumento
const DireccionSchema = new mongoose.Schema({
  calle: {
	  type: string
  },
  ciudad: {
	  type: string
  },
  codigoPostal: {
	  type: number
  }
});

//Documento principal
const UsersSchema = new mongoose.Schema({
	nombre: {
		type: string
	},
	edad: {
		type: number
	},
	direccion: {
		type: DireccionSchema // <-- aquí está
	},   
	email: {
		type: string
	} 
})
```
# Ejemplo final
Veamos un ejemplo, no te espantes por el tamaño, si lo lees es sencillo en realidad:
```json
const mongoose = require("mongoose")

const DireccionSchema = new mongoose.Schema({
    calle: {
        type: String,      // Debe ser tipo String
        required: true,   // Es obligatorio
        trim: true,       // Eliminar espacios en blanco
        minLength: 2,     // Longitud mínima
        maxLength: 100,   // Longitud máxima
        lowercase: true   // Convertir a minúsculas
    },
    ciudad: {
        type: String,      // Debe ser tipo String
        required: true,   // Es obligatorio
        trim: true,       // Eliminar espacios en blanco
        minLength: 2,     // Longitud mínima
        maxLength: 100,   // Longitud máxima
        lowercase: true   // Convertir a minúsculas
    },
    pais: {
        type: String,      // Debe ser tipo String
        required: true,   // Es obligatorio
        trim: true,       // Eliminar espacios en blanco
        minLength: 2,     // Longitud mínima
        maxLength: 100,   // Longitud máxima
        lowercase: true   // Convertir a minúsculas
    }
})

const UsersSchema = new mongoose.Schema({
	nombre: {
		type: String,      // Debe ser tipo String
		required: true,   // Es obligatorio
        trim: true,       // Eliminar espacios en blanco
        minLength: 2,     // Longitud mínima
        maxLength: 100,   // Longitud máxima
        lowercase: true   // Convertir a minúsculas
	},
	edad: {
		type: Number,    // Debe ser tipo Number
		required: true,   // Es obligatorio
		min: 18           // Edad mínima
	},
	email: {
		type: String,      // Debe ser tipo String
		required: true,   // Es obligatorio
		trim: true,       // Eliminar espacios en blanco
        match: /.+\@.+\..+/,  // Debe tener formato de correo electrónico
		minLength: 5,     // Longitud mínima
		maxLength: 100,   // Longitud máxima
		lowercase: true   // Convertir a minúsculas
	},
	telefono: {
		type: String,      // Debe ser tipo String
		required: true,   // Es obligatorio
		trim: true,       // Eliminar espacios en blanco
		match: /^\d{7,14}$/,  // Debe ser un número de 7 a 14 dígitos
	},
    direccion: {
        type: DireccionSchema, // Referencia al esquema de dirección
        required: true         // Es obligatorio
    },
    fechaRegistro: {
        type: Date,       // Debe ser tipo Date
        default: Date.now  // Valor por defecto: fecha y hora actuales
    }
})
```
Finalmente d