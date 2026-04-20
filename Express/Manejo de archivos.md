En [[Express]] no solo vamos a estar manejando puro JSON, sino, que a veces se hace necesario poder manejar archivos que son enviados por el usuario, pero obviamente, deben ser procesados de alguna forma para que no nos dañe el servidor, o comprometa su comportamiento.
# Multer
Multer nos permite hacer dos cosas esenciales con los archivos que nos envia el cliente:
1. Analizar el archivo.
2. Procesar el archivo.
Esto es vital para poder mantener el estado correcto del servidor. Su instalación es sencilla:
```bash
npm install multer
```
## ¿Cómo analiza, y procesa archivos?
Lo que hace Multer con los archivos, es básicamente colocarlos en un stage temporal del servidor, antes de que se vaya a la base de datos, o cualquier otro lugar, y empezar a trabajar con el. Una vez el archivo esta en el stage, Multer crea un objeto llamado "file" en donde se almacena toda la información del archivo.
## El objeto file
Dentro del objeto file podemos encontrar casi que todo lo relacionado con el archivo:
``` javascript
const file = {
	fieldname: "el nombre del campo en el formulario que contiene el archivo.",
	originalname: "el nombre original del archivo en el disco del usuario.",
	encoding: "el tipo de codificación del archivo (por ejemplo 7bit).",
	mimetype: "el tipo MIME del archivo (por ejemplo, image/png).",
	size: "el tamaño del archivo en bytes.",
	destination: "la carpeta donde se guarda el archivo (solo si usas diskStorage).",
	filename: "el nombre que Multer le asignó al archivo guardado en el servidor (si usas diskStorage).",
	path: "la ruta completa del archivo en el servidor (solo con diskStorage).",
	buffer: "el contenido del archivo en un Buffer de Node.js (solo si usas memoryStorage)."
}
```
Podemos usar toda esa información para crear validaciones de archivos, y empezar a corroborar que el archivo enviado, es lo que se espera.
# Uso de Multer
Para poder empezar a crear Multer, obviamente debemos instanciarlo como cualquier otro paquete:
```javascript
import multer from "multer";
```
Ahora debemos tener 3 cosas listas para poder usarlo:
- **Storage:** se trata de en donde vamos a guardar los archivos de esa ruta. Aquí podemos optar por guardarlo dentro del servidor, o usar un bucket.
- **fileFilter:** aquí va ocurrir toda la magia, ya que en este punto vamos a validar la información del archivo para poder darle el visto bueno, y que pase a ser guardado.
- **limits:** en limits podremos definir cuanto es el máximo que puede pesar el archivo, o de ser una imagen, que dimensiones puede tener.
## Storage
Para poder definir en donde vamos a almacenar los archivos procesados por Multer, debemos seguir está estructura:
```javascript
const storage = multer.diskStorage({
	destination: (req, file, cb) => {
		//Aquí incluimos las configuraciones del destino del archivo	
	},
	filename: (req, file, cb) => {
		//Aquí configuramos como queremos que se llame el archivo
	}
})
```
Ahora veamos una configuración básica:
```javascript
import path from 'path';
import uuid from 'uuid';
import multer from "multer";

const storage = multer.diskStorage({
    destination: (req, file, cb) => {
        if (file.fieldname === 'avatar') {
            cb(null, 'uploads/avatars/');
        } else if (file.fieldname === 'wallpaper') {
            cb(null, 'uploads/wallpapers/');
        } else {
            cb(new Error('Invalid field name'), null);
        }
    },
    filename: (req, file, cb) => {
        const ext = path.extname(file.originalname);
        const uniqueName = `${Date.now()}-${uuid()}${ext}`;
        cb(null, uniqueName);
    }
});
```
> [!info] Sobre el nombre, y el almacenamiento
> Como puedes notar hemos usado **diskStorage** en donde podremos almacenar información dentro del mismo servidor. De usar un bucket (lo recomendado) debe usarse otra nomenclatura, e incluir otro paquete para manejarlo.
> Por otro lado, como puede notar, hemos usado **uuid**, y **date** para crear un nombre único para el archivo, esto nos garantiza que los nombres de los archivos nunca sean iguales.
## fileFilter
Ahora, para construir esto debemos de tener muy presentes los datos del objeto "file", ya que los vamos a usar para crear casos de validación. Para crear el fileFilter, vamos a seguir está estructura:
```javascript
const fileFilter = (req, file, cb) => {
	//Validaciones que vayamos a programar
}
```
Se recomienda hacer uso de la estructura "if/else", ya que esencialmente vamos a comprobar algo, y si está bien pasa con **cb**, y si esta mal, pasa como error. Por ejemplo:
```javascript
const fileFilter = (req, file, cb) => {
	// Reglas:
    const camposPermitidos = ["avatar", "wallpaper"];
    const tipoArchivosPermitidos = ["image/png", "image/jpeg"];

    // Primer test:
    if (!camposPermitidos.includes(file.fieldname)) {
        return cb(new Error(`El campo '${file.fieldname}' no está permitido`), false);
    }

    // Segundo test:
    if (!tipoArchivosPermitidos.includes(file.mimetype)) {
        return cb(new Error(`El formato ${file.mimetype} no es válido`), false);
    }

    // Si los tests pasan, se sigue adelante:
    cb(null, true);
};
```
> [!info] Sobre el uso de cb(caso, true/false)
> Cuando usamos cb() lo hacemos para dos casos:
> 1. Para devolver un error, en este caso se envia así: `cb(error, false);`
> 2. Para guardar el archivo si todos los tests pasan: `cb(null, true);`
## limits
El limitador es lo más sencillo de los tres, por ejemplo:
```javascript
const limits = {
    fileSize: 5 * 1024 * 1024 // 5MB
};
```
## Instancia, y uso
Ya que finalmente tenemos los 3 reunidos, podemos instanciar así de fácil:
```javascript
const upload = multer({
    storage: storage,
    fileFilter: fileFilter,
    limits: limits
});
```
Para usarlo, se debe incluir en una ruta tal que así:
```javascript
app.post('/upload-avatar', upload.single('avatar'), (req, res) => {
    res.send('File uploaded successfully');
}); //Para subir un solo archivo

app.post('/upload-multiple', upload.fields([
    { name: 'avatar', maxCount: 1 },
    { name: 'wallpaper', maxCount: 1 }
]), (req, res) => {
    res.send('Files uploaded successfully');
}); //Para varios archivos
```
> [!warning] IMPORTANTE
> Es importante que se use **.single**, o **.fields** para señalar si la ruta recibe uno, o varios archivos. También, de ser **.fields** seguir adecuadamente la sintaxis.
