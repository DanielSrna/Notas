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
## Operadores de consulta
Cuando hablamos de una operación de consulta, nos referimos a que estamos intentando traer documentos que cumplan con los parámetros de busqueda, la estructura es:
```json
db.nombre_colección.find({
	nombreCampo: {operador: valor}
})
```
#### Operador "igual que" ($eq)
El operador "igual que", busca documentos cuyo campo operado, tenga un valor igual que el valor del operador, por ejemplo:
```json
// Encuentra productos con precio exactamente 1200 
db.productos.find({
	precio: { $eq: 1200 } 
});
```
En este caso, trae todos los documentos cuyo campo "precio" sea igual a 1200.
#### Operador "mayor que" ($gt)
El operador "mayor que", busca documentos cuyo campo operado, tenga un valor mayor que el valor del operador, por ejemplo:
```json
db.productos.find({
	precio: {$gt: 1200}
})
```
En este caso, trae todos los documentos cuyo campo "precio" es mayor a 1200.
#### Consulta "mayor, o igual que" ($gte)
El operador "mayor, o igual que", busca documentos cuyo campo operado, tenga un valor mayor, o igual que el valor del operador, por ejemplo:
```json
db.productos.find({
	precio: {$gte: 1200}
})
```
En este caso, trae documentos cuyo campo "precio", tenga un valor mayor, o igual a 1200.
#### Consulta "menor que" ($lt)
El operador "menor que", busca documentos cuyo campo operado, tenga un valor menor que el valor del operador, por ejemplo:
```json
db.productos.find({
	precio: {$lt: 900}
})
```
En este caso, trae documento cuyo campo "precio", tenga un valor menor a 900.
#### Consulta "menor, o igual que" ($lte)
El operador "menor, o igual que", busca documento cuyo campo operado, tenga un valor menor, o igual que el valor del operador, por ejemplo:
```json
db.productos.find({
	precio: {$lte: 900}
})
```
En este caso, trae documentos cuyo campo "precio", tenga un valor menor o igual a 900.
#### Operador "distinto a" ($ne)
El operador "distinto a", busca documento cuyo campo operado, tenga un valor distinto al valor del operador, por ejemplo:
```json
db.productos.find({
	precio: {$ne: 900}
})
```
En este caso, trae documentos cuyo campo "precio", tenga un valor distinto a 900.
#### Operador "dentro de" ($in)
El operador "dentro de", busca documentos cuyo campo operado, tenga un valor que este dentro de un rango de valores definidos en el operador, por ejemplo:
```json
db.productos.find({
	precio: {$in: [800, 900]}
})
```
En este caso, trae documentos cuyo campo "precio", tenga un valor que este dentro de 800, y 900, incluyéndolos. 
#### Operador "no dentro de" ($nin)
El operador "no dentro de", busca documentos cuyo campo operado, tenga un valor distinto dentro del rango de valores definidos en el operador, por ejemplo:
```json
db.productos.find({
	precio: {$in: [800, 900]}
})
```
En este caso, trae documento cuyo campo "precio", tenga un valor distinto al rango de valores de entre 800, y 900, incluyéndolos.
## Combinación de operadores
Podemos hacer que los operadores se combinen para hacer una busqueda aún más especifica. Para ello vamos a usar la siguiente estructura:
```json
db.nombre_colección.find({
	$combinación: [
		{campo: {$consulta: valor}},
		{campo: {$consulta: valor}}
	]
})
```
#### Combinación AND ($and)
En esta combinación, todos los operadores deben encontrar algo, o sea, todo debe ser true, porque de otra forma, no se muestra nada. Es la combinación más estricta.
```json
db.productos.find({
	$and :[
		{precio: {$gte: 500}},
		{precio: {$lte: 1000}}
	]
})
```
En este caso, buscamos todos los productos cuyos precios sean más de 500, pero menores que 1000. Si alguna de las dos operaciones no encuentra algo, falla la consulta.
#### Combinación OR ($or)
En esta combinación, al menos uno de todos los operadores debe encontrar algo, porque de otra forma, no se muestra nada. Es la combinación más flexible, por ejemplo:
```json
db.productos.find({
	$or: [
		{precio: {$lte: 200}},
		{categoria: "Electrodomésticos"}
	]
})
```
En este caso, buscamos todos los productos cuyos valores "precio", o "categoría", cumplan con los valores de las operaciones. Al menos uno de ellos, por ejemplo, si no hay producto de categoría "electrodomésticos", pero se encontró un producto que cuesta menos de 200, pues se muestra.
## Proyección de campos
Cuando hacemos una consulta, y encontramos un documento, este nos suele mostrar TODOS LOS CAMPOS, pero a veces nosotros no queremos TODOS LOS CAMPOS, así que incluimos en la consulta que es lo que queremos ver, la estructura es así:
```
db.nombre_colección.find({
	Operaciones de consulta
}, {clave: 1, clave: 1, clave: 1})
```
Esencialmente, le vamos a decir que campos si queremos que traiga, marcándolos con 1. Por ejemplo:
```json
db.productos.find({
	$and: [
		{precio: {$gte: 500}},
		{precio: {$lte: 1000}}
	]
}, {nombre: 1, precio: 1, _id: 0})
```
Esto nos devuelve la lista de productos que cumplan con los operadores, pero solamente sus nombres y precios, nada más. Se pone "id: 0", para señalar que no lo queremos.
# U-pdate, o actualización de documentos
Ahora, vamos a ver como podremos actualizar los documentos, es esencialmente sencillo.
#### Actualizar un documento (updateOne)
Para actualizar un documento, vamos a usar **updateOne** y el operador **$set**:
```json
db.productos.updateOne(
	{ nombre: "Laptop Pro X" },
	{ $set: {precio: 1000} }
)
```
Para reemplazar varios campos:
```json
db.productos.updateOne(
	{ nombre: "Laptop Pro X" },
	$set: {
		precio: 1200,
		disponible: true
	}
)
```
#### Actualizar varios documentos (updateMany)
Es esencialmente lo mismo, pero tenga en cuenta que ahora el operador de busqueda, debe coincidir con varios documentos, no solo con uno.
```json
db.productos.updateMany(
	{ categoria: "Electrodomésticos" },
	{ $set: { disponible: false } }
)
```
#### Reemplazar todo el documento (replaceOne)
Los operadores anteriores solo servían para cambiar el valor de algunos campos, y el resto de campos del documento quedaban intactos. En este caso, cuando modifiquemos un campo con **replaceOne**, y no especifiquemos valores para el resto, simplemente se eliminan.
```json
db.productos.replaceOne(
	{ nombre: "Televisor" },
	{
		precio: 1200,
		disponible: false,
		nombre: "TV"
	}
)
```
En este caso, el documento "Televisor" paso a llamarse "TV", y todos sus campos excepto "precio", "disponible", y "nombre" fueron eliminados.
## Operadores de actualización
Ya hemos estado usando $set, pero no es el único operador, hay otros que también hacen cosas curiosas.
#### $set
Este operador permite actualizar, o crear un nuevo campo dentro de un documento.
#### $inc
Este operador permite aumentar, o disminuir el valor de un campo dentro de un comento. Todo depende de si el nuevo valor es positivo, o negativo.
#### $push
Si tenemos un campo que es un array de elementos, y queremos incluir uno nuevo, usamos $push.
#### $pull
Si tenemos un campo que es un array de elementos, y queremos eliminar uno de ellos, usamos $pull.
#### $unset
Si queremos eliminar uno, o varios campos de un documento, usamos $unset.
> [!important] Sobre los operadores
> Estos operadores se usan todos con **updateOne**, o con **updateMany** si la operación se quiere hacer con varios documentos. Son idénticos al uso de **$set**, solo que su comportamiento sobre los campos es distinto.

