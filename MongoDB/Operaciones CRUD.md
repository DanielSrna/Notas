Dentro de MongoDB, o bueno, dentro de cualquier base de datos, lo que queremos hacer es algo llamado CRUD, que se define como:
- **C**reate: crear nuevos elementos en la base de datos.
- **R**ead: leer los elementos en la base de datos.
- **U**pdate: actualizar los elementos de la base de datos.
- **D**elete: eliminar los elementos de la base de datos.
Esas 4 operaciones son los pilares fundamentales para administrar cualquier base de datos, y MongoDB no es la excepción. 
# C-reate, o creación de documentos
En MongoDB no se le llama creación al hecho de meter un nuevo documento, sino, **"insertar"**, es importante hacer esa distinción. 
Antes de insertar uno, o varios documentos, es importante considerar:
1. El nombre de la colección.
2. El documento, o documentos estructurados en JSON para insertar.
#### Insertar un solo documento
```json
db.<nombreColeccion>.insertOne({
	nombre: "ejemplo",
	fecha: "hoy"
})
```
#### Insertar varios documentos
En este caso, se hace uso de un array, y se separan los documentos dentro del mismo.
```json
db.<nombreColeccion>.insertMany([
	{
		nombre: "ejemplo1",
		fecha: "mañana"
	},
	{
		nombre: "ejemplo2",
		fecha: "hoy"
	},
	{
		nombre: "ejemplo3",
		fecha: "ayer"
	}
])
```
#### ¿Qué pasa con los ID?
Bueno, en este caso MongoDB crea por si mismo el ID, pero es importante tener siempre un campo único en nuestros documentos, para facilitar su búsqueda, ya sea un email, o identificación.
# R-ead, o lectura de documentos
La lectura de documentos es posiblemente la operación que más puede complicarse dentro de MongoDB, pero esencialmente gira en torno a un concepto de **"busqueda por filtros"**.
## Lectura básica
Bien, antes que nada, veamos como se hace una lectura general, y una especifica.
**Lectura de toda la colección:**
```json
db.productos.find({})
```
**Lectura especifica de uno, o varios documentos:**
```json
db.productos.find({
	categoria: "Electrodomésticos"
})
```
#### Operadores de consulta
Cuando hablamos de una operación de consulta, nos referimos a que estamos intentando traer documentos que cumplan con los parámetros de busqueda, la estructura es:
```json
db.nombre_colección.find({
	nombreCampo: {operador: valor}
})
```
