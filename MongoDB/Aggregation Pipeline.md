Está herramienta de [[MongoDB]] es realmente poderosa, y nos permite realizar análisis de datos bastante complejos. Se comporta tal que así:
1. **Filtrado:** agarra una serie de documentos que tengan algo relacionado, tradicionalmente es el campo "fecha".
2. **Agrupación**: se separan en grupos, por ejemplo, si le decimos en la etapa de filtrado que tome los elementos de entre 2025, hasta 2026, sería 12 grupos por cada mes.
3. **Procesamiento:** a cada grupo se le hace una operación en concreto, ya sea para saber resultados netos, o promedios.
4. **Ordenamiento:** se ordenan los resultados del menor al mayor, o viceversa. También en orden cronologico si se trata de fechas.
5. Se presentan los resultados.
Por ejemplo:
- Si quisiéramos saber el promedio de ventas de productos a lo largo de un año.
- Si quisiéramos saber cuanto compran nuestros clientes cada mes en promedio.
- Si quisiéramos saber de que país vienen más de nuestros productos.
## Sentencia aggregate([]) 
Está sentencia es la que vamos a usar en lugar de **.find** o cualquier otra. La estructura es básicamente así:
```json
db.colección.aggregate([
	{
		// Etapa de match, o filtrado.
	},
	{
		// Etapa de agrupación, y procesamiento.
	},
	{
		// Etapa de presentación.
	},
	{
		// Etapa de ordenamiento.
	}
])
```
## Etapas de un aggregation pipeline
Ahora veamos las cuatro etapas en orden, en realidad es bastante sencillo.
#### Etapa de filtrado ($match)
En esta etapa lo que queremos hacer, es agarrar los documentos de una colección, y seleccionar los que nos interesa analizar, para ello vamos a utilizar un campo en común que tenga cada uno. Pero, es importante decir que "campo en común", no quiere decir que tengan el mismo valor, sino, una clave idéntica.
Por ejemplo:
*Si tenemos una serie de documentos que muestran una cantidad de transacciones, a nosotros nos interesa filtrarlas por fechas, por ejemplo, se pueden extraer de una fecha especifica a otra, como de un año a otro, o hasta de un mes a otro.*
```json
db.productos.aggregate([
	{
		$match: {
			fecha: {
				$gte: new ISODate("2024-01-01T00:00:00Z"), 
				$lt: new ISODate("2025-01-01T00:00:00Z")
			}
		}
	},
	{ // Etapa de agrupación, y procesamiento. },
	{ // Etapa de presentación. },
	{ // Etapa de ordenamiento. }
])
```
En este caso, nos traemos todos los documentos cuya fecha coincida con los parámetros de busqueda, que en este caso, son todas las compras entre el primero de enero del 2024 incluyéndolo, hasta el primero de enero del 2025, sin incluirlo.
#### Etapa de agrupación, y procesamiento
En está etapa vamos a decidir como agrupar los documentos que **$match** nos ha ofrecido, y como nos ha ofrecido un rango de fechas, pues, podemos usar meses por ejemplo.
Si tenemos unos 30 documentos, o más, por así decirlo, se empiezan a separar en 12 grupos distintos, cada documento va en su respectivo grupo.
```json
db.productos.aggregate([
	{ // Etapa de filtrado. },
	{
		$group: {
			_id: { month: { $month: "$fecha" } }
		}
	},
	{ // Etapa de presentación. },
	{ // Etapa de ordenamiento. }
])
```
> [!info] ¿Qué rayos es eso?
> Te preguntaras, ¿Qué rayos es esa estructura? O sea, está costa de aquí:
`{json}_id: { month: { $month: "$fecha" } }`
Es sencillo, lo que pasa, es que en esta etapa de agrupación, y procesamiento, vamos a crear variables que vamos a llenar con información. Entonces, ese `{json}month: { $month: "$fecha" }` es una forma se agregar de una vez el número del mes a una variable llamada "month", porque podríamos simplemente hacer la agrupación con `{json}_id: { $month: "$fecha" }`, pero entonces luego, ¿Cómo rayos vamos a saber que significa el número de "_id"?

Bien, ya podemos pasar al procesamiento, es bastante sencillo:
```javascript
db.productos.aggregate([
	{ // Etapa de filtrado. },
	{
		$group: {
			_id: { month: { $month: "$fecha" } },
			ingresos_totales: { $sum: { $multiply: ["$cantidad", "$precio_unitario"] } }, 
			cantidad_productos_vendidos: { $sum: { "$cantidad" } }
		}
	},
	{ // Etapa de presentación. },
	{ // Etapa de ordenamiento. }
])
```
Bine, aquí hicimos varias cosas que vale la pena señalar:
- Hemos creado más variables. Ahora no solo la variable month contiene un valor, sino, otras dos variables más.
- Hemos usado operadores matemáticos, para operar campos específicos de cada grupo.
- Como podemos notar, para llamar a los campos, lo hemos hecho con un "$".
- El orden de las operaciones está directamente asociado por el nivel de anidamiento. Si una operación está dentro de otra, se empieza a resolver la que está más adentro.
Ahora, veamos que operadores podemos usar aquí:
- **`$avg`**: Calcula el promedio.
- **`$min` / `$max`**: Encuentra el valor más bajo o más alto.
- **`$push`**: Crea un **array** con todos los valores de un campo.
- **`$first` / `$last`**: Toma el primer o último valor que entra al grupo.
- **`$subtract`**: Resta dos números.
- **`$divide`**: Para sacar porcentajes o ratios.
- **`$round`**: Para que los precios no te salgan con mil decimales.
#### Etapa de presentación
Está etapa es sencilla, simplemente vamos a definir que queremos que se muestre, y que no queremos que se muestre. Cuando fijamos que queremos que algo se muestre, automáticamente el resto de campos desaparecen, así que en ese caso debemos definir que es todo lo que queremos mostrar.
```json
db.productos.aggregate([
	{
		$project: {
			_id: 0, //no nos interesa ver el ID.
			mes: "$_id.month", // Si nos interesa ver el mes
			ingresos_totales: 1, //1 significa "incluido"
			cantidad_productos_vendidos: 1
		}
	}
])
```
#### Etapa de ordenamiento
Aquí vamos a definir como queremos que se ordene esto. Si seguimos nuestro ejemplo, podemos hacer que se ordene por un orden ascendente, o descendente según el numero del mes, u ordenarlos por quien ha tenido más ingresos totales, o por cual mes ha vendido más productos.
```json
db.productos.aggregate([
	{
		$sort: {
			ingresos_totales: -1
		}
	}
])
```
## Ejemplo final
Ahora veamos un ejemplo final, está es la colección:
```json
[
  { "producto": "Laptop Gamer",         "cantidad": 1,  "precio_unitario": 1200, "fecha": ISODate("2024-01-15T10:00:00Z") },
  { "producto": "Mouse Inalámbrico",    "cantidad": 5,  "precio_unitario": 25,   "fecha": ISODate("2024-01-20T11:30:00Z") },
  { "producto": "Teclado Mecánico",     "cantidad": 2,  "precio_unitario": 80,   "fecha": ISODate("2024-02-05T14:45:00Z") },
  { "producto": "Laptop Ultrabook",     "cantidad": 1,  "precio_unitario": 1500, "fecha": ISODate("2024-02-10T09:00:00Z") },
  { "producto": "Webcam 4K",            "cantidad": 3,  "precio_unitario": 100,  "fecha": ISODate("2024-03-25T16:20:00Z") },
  { "producto": "Monitor 27''",         "cantidad": 2,  "precio_unitario": 300,  "fecha": ISODate("2024-04-05T10:15:00Z") },
  { "producto": "Disco SSD 1TB",        "cantidad": 4,  "precio_unitario": 130,  "fecha": ISODate("2024-05-12T13:45:00Z") },
  { "producto": "Tablet Android",       "cantidad": 1,  "precio_unitario": 400,  "fecha": ISODate("2024-06-01T15:00:00Z") },
  { "producto": "Router WiFi 6",        "cantidad": 3,  "precio_unitario": 90,   "fecha": ISODate("2024-06-18T11:10:00Z") },
  { "producto": "Auriculares Bluetooth","cantidad": 6,  "precio_unitario": 60,   "fecha": ISODate("2024-07-10T12:30:00Z") },
  { "producto": "Silla Ergonómica",     "cantidad": 2,  "precio_unitario": 250,  "fecha": ISODate("2024-08-22T09:00:00Z") },
  { "producto": "Hub USB-C",            "cantidad": 5,  "precio_unitario": 40,   "fecha": ISODate("2024-09-15T13:15:00Z") },
  { "producto": "Cámara de Seguridad",  "cantidad": 3,  "precio_unitario": 150,  "fecha": ISODate("2024-10-05T08:50:00Z") },
  { "producto": "Impresora Láser",      "cantidad": 1,  "precio_unitario": 300,  "fecha": ISODate("2024-11-25T17:20:00Z") },
  { "producto": "Disco Duro 2TB",       "cantidad": 2,  "precio_unitario": 110,  "fecha": ISODate("2024-12-10T16:40:00Z") },
  { "producto": "Laptop Gamer",         "cantidad": 1,  "precio_unitario": 1250, "fecha": ISODate("2025-01-12T10:00:00Z") },
  { "producto": "Teclado Mecánico",     "cantidad": 3,  "precio_unitario": 85,   "fecha": ISODate("2025-01-20T12:00:00Z") },
  { "producto": "Monitor 27''",         "cantidad": 1,  "precio_unitario": 310,  "fecha": ISODate("2025-02-05T11:30:00Z") },
  { "producto": "Tablet Android",       "cantidad": 2,  "precio_unitario": 420,  "fecha": ISODate("2025-02-15T14:10:00Z") },
  { "producto": "Auriculares Bluetooth","cantidad": 4,  "precio_unitario": 65,   "fecha": ISODate("2025-03-08T09:45:00Z") },
  { "producto": "Silla Ergonómica",     "cantidad": 1,  "precio_unitario": 260,  "fecha": ISODate("2025-04-01T13:05:00Z") },
  { "producto": "Laptop Ultrabook",     "cantidad": 1,  "precio_unitario": 1550, "fecha": ISODate("2025-04-22T15:25:00Z") },
  { "producto": "Hub USB-C",            "cantidad": 7,  "precio_unitario": 45,   "fecha": ISODate("2025-05-10T16:55:00Z") },
  { "producto": "Disco SSD 1TB",        "cantidad": 2,  "precio_unitario": 140,  "fecha": ISODate("2025-06-03T10:10:00Z") },
  { "producto": "Router WiFi 6",        "cantidad": 4,  "precio_unitario": 95,   "fecha": ISODate("2025-07-01T11:40:00Z") }
]
```
Y este es el aggregation pipeline que hemos construido:
```json
db.productos.aggregate([
  {
    $match: {
      fecha: {
        $gte: new ISODate("2024-01-01T00:00:00Z"),
        $lt: new ISODate("2025-01-01T00:00:00Z"),
      },
    },
  },
  {
    $group: {
      _id: {
        month: { $month: "$fecha" },
      },
      total_ventas: { $sum: { $multiply: ["$cantidad", "$precio_unitario"] } },
      total_productos: { $sum: "$cantidad" },
    },
  },
  {
    $project: {
      mes: "$_id.month",
      total_ventas: 1,
      total_productos: 1,
      _id: 0,
    },
  },
  {
    $sort: {
      total_ventas: -1,
    },
  },
]);
```
Nos devuelve:
```json
[
  {
    "total_ventas": 1660,
    "total_productos": 3,
    "mes": 2
  },
  {
    "total_ventas": 1325,
    "total_productos": 6,
    "mes": 1
  },
  {
    "total_ventas": 670,
    "total_productos": 4,
    "mes": 6
  },
  {
    "total_ventas": 600,
    "total_productos": 2,
    "mes": 4
  },
  {
    "total_ventas": 520,
    "total_productos": 4,
    "mes": 5
  },
  {
    "total_ventas": 500,
    "total_productos": 2,
    "mes": 8
  },
  {
    "total_ventas": 450,
    "total_productos": 3,
    "mes": 10
  },
  {
    "total_ventas": 360,
    "total_productos": 6,
    "mes": 7
  },
  {
    "total_ventas": 300,
    "total_productos": 1,
    "mes": 11
  },
  {
    "total_ventas": 300,
    "total_productos": 3,
    "mes": 3
  },
  {
    "total_ventas": 220,
    "total_productos": 2,
    "mes": 12
  },
  {
    "total_ventas": 200,
    "total_productos": 5,
    "mes": 9
  }
]
```
## Etapas extra (limitación, y saltos)
Ahora, como podemos notar, nos dio todos los documentos, pero, ¿Qué pasa si consultamos una colección que tiene miles de documentos, y agrupamos cientos de ellos? Simplemente la respuesta va ser GIGANTE, e inmanejable. 
#### Limitación
Podemos limitar la cantidad de elementos que nos muestren, para ello agregamos una quinta etapa:
```json
db.ventas.aggregate([
	{//Buscamos todas las ventas hechas en 2024
		$match: {
			fecha: {
        $gte: ISODate("2024-01-01T00:00:00Z"),
        $lt: ISODate("2025-01-01T00:00:00Z"),
			},
		},
	},
	
	{//Ordenamos los precios de menor a mayor
		$sort: {
			precio_unitario: 1-
		},
	},
	{//Mostramos solo el nombre, y el precio del producto
		$project: {
			_id: 0,
			producto: 1,
			precio_unitario: 1,
		}
	},
	{//Limitamos el resultado a 5
		$limit: 5
	},
])
```
#### Salto
Ahora, ya nos muestra solo 5 resultados, excelente, pero, ¿Cómo vemos el resto? Pues, saltando esos 5 con un skip:
```json
db.ventas.aggregate([
	{//Buscamos todas las ventas hechas en 2024
		$match: {
			fecha: {
        $gte: ISODate("2024-01-01T00:00:00Z"),
        $lt: ISODate("2025-01-01T00:00:00Z"),
			},
		},
	},
	
	{//Ordenamos los precios de menor a mayor
		$sort: {
			precio_unitario: 1-
		},
	},
	{//Mostramos solo el nombre, y el precio del producto
		$project: {
			_id: 0,
			producto: 1,
			precio_unitario: 1,
		}
	},
	{//Saltamos los primeros 5 documentos
	$skip: 5 
	},
	{//Limitamos el resultado a 5
		$limit: 5
	},
])
```
> [!info] Sobre saltos, y limites
> Si manejan esos saltos, y limites de forma dinámica con las peticiones del cliente, pueden hacer una paginación tremenda. 

