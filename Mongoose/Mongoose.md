Mongoose es la herramienta más fiable para trabajar con bases de datos [[MongoDB]] dentro de Node.js, ya que permite crear criterios muy sólidos, y reglas. Veamos sus beneficios:
1. Se usa algo llamado "Modelos", que son básicamente un esquema de como deben ser los documentos dentro de cada colección, haciendo que se deban respetar reglas, y no dejando pasar cuanto JSON se vaya a meter.
2. Permite realizar operaciones antes, durante, y después de una operación principal, haciendo las cosas mucho más sencillas para limpiar datos, o modificar cosas.
3. Las relaciones entre las colecciones ahora son mucho más tangibles, ya que se identifican directamente dentro de los modelos de las colecciones.
# Uso
Para usar está herramienta se deben de hacer una serie de cosas primero.
#### Instalación
Se instala como un paquete de NPM:
```bash
npm install mongoose
```
#### Ubicación
La configuración principal de la base de datos, la vamos a realizar dentro de la carpeta "config", algo así:
![[Pasted image 20260422153142.png]]
#### Levantamiento de la base de datos
Es bastante sencillo, prácticamente debes copiar y pegar:
```javascript
//Instanciamos Mongoose para usarlo, y traemos la URL
const mongoose = require("mongoose")
const mongoURL = process.env.MONGODB_URL

//Función para realizar la conexión
const connectDb = async () => {
    await mongoose.connect(mongoURL)
}

//La exportamos
module.exports = connectDb
```
#### Inicio de la base de datos
Una vez tenemos el iniciador listo, hay que iniciarlo, para eso en el archivo final del proyecto "server.js", colocamos:
```javascript
const express = require("express")
const app = express()
const connectDb = require("./config/db")
require("dotenv").config()
const PORT = process.env.PORT

//Conectamos la base de datos, y entonces iniciamos el servidor.
connectDb()
    .then(() => {
        console.log("MongoDB connected")
        app.listen(PORT, () => {
            console.log(`Server is running on port ${PORT}`)
        })
    })
    .catch((error) => {
        console.error("Error connecting to the database:", error)
    })
```
#### ¿Cómo conectamos a MongoDB Atlas?
Para ellos configuramos el archivo .env, y le agregamos:
```javascript
MONGODB_URL=mongodb+srv://<nombre>:<password>@cluster0.rnhn7.mongodb.net/<aqui va la base de datos>?retryWrites=true&w=majority&appName=<nombre del cluster>
```