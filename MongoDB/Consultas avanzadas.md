En [[MongoDB]] además de las consultas tradicionales, también tenemos consultas avanzadas, que permiten un poco más de flexibilidad en cuanto a encontrar contenido.
Para todas las siguientes consultas nos vamos a valer de **.find**, así que es muy importante repasar el contenido de [[Operaciones CRUD]].
## consultas con expresiones regulares
Las expresiones regulares son muy útiles para analizar el contenido de un texto, y aún más si lo usamos para buscar cosas, veamos como se pueden aplicar. Para estás consultas nos vamos a valer del operador **$regex**. Ahora, tomemos el siguiente ejemplo de colección:
```json
[
	{nombre: "laptop"},
	{nombre: "solapa"},
	{nombre: "tablet"},
	{nombre: "sopa"},
	{nombre: "lapicero"}
]
```
#### consulta por coincidencia al principio
Es la consulta más básica, simplemente buscamos que las primeras letras de una palabra, coincidan con los caracteres de busqueda.
```json
db.productos.find({
  nombre: { $regex: /^lap/ }
});

// Obtenemos:
[
  {
    "nombre": "laptop"
  },
  {
    "nombre": "lapicero"
  }
]
```
#### consulta por coincidencia al final
Es lo mismo que el anterior, solo que ahora se busca que los últimos caracteres de una palabra, coincidan con la expresión regular de busqueda.
```json
db.productos.find(
	nombre: { $regex: /pa$/ }
)

// Obtenemos:
[
  {
    "nombre": "solapa"
  },
  {
    "nombre": "sopa"
  }
]
```
#### consulta por coincidencia general
Ahora, no importa donde estén ubicados los caracteres de busqueda, lo importante es que la palabra lo contenga, y ya.
```json
db.productos.find({
  nombre: { $regex: /ap/ }
});

// Obtenemos:
[
  {
    "nombre": "laptop"
  },
  {
    "nombre": "solapa"
  },
  {
    "nombre": "lapicero"
  }
]
```
> [!tip] Sobre las mayúsculas
> Supongamos que las palabras contienen letras en mayúsculas, o nuestra busqueda las contiene, en este caso no se va poder encontrar nada porque las búsquedas de este tipo son sensibles a las mayúsculas, para desactivar eso:
> 
> `{json}nombre: { $regex: /OL/, $options: "i" }`
## Consultas anidadas, y arreglos
Ahora, vamos a ver como consultar dentro de estructuras complejas de JSON, es bastante sencillo en realidad. Supongamos la siguiente colección:
```json
[
  {
    "nombre": "Laptop Gamer Z",
    "especificaciones": [
      { "componente": "RAM", "valor": 16 },
      { "componente": "CPU", "valor": 8 },
      { "componente": "GPU", "valor": 12 }
    ],
    "caracteristicas": {
      "color": "negro",
      "altura": 60,
      "chasis": "gamer"
    }
  },
  {
    "nombre": "Laptop Gamer X",
    "especificaciones": [
      { "componente": "RAM", "valor": 24 },
      { "componente": "CPU", "valor": 6 },
      { "componente": "GPU", "valor": 8 }
    ],
    "caracteristicas": {
      "color": "blanco",
      "altura": 50,
      "chasis": "oficina"
    }
  }
]
```
#### consulta de datos anidados
Para esto vamos a considerar la "dot notation" o la misma forma en como exploramos objetos dentro de JavaScript. Es realmente sencillo.
```json
db.productos.find(
	{ "caracteristicas.color": "negro" }
)
```
En este caso, nos trae el documento que tenga un documento anidado llamado "características" que contenga un campo "color" con el valor de "negro", sería **"Laptop Gamer Z"**.
#### consulta de datos en un arreglo
Ahora, este es un poco más complejo, pero debemos de hacer uso del operador **$elemMatch**.
```json
db.productos.find(
	{ especificaciones: { $elemMatch: { componente: "RAM", valor: 16 } } }
)
```
¿Por qué rayos es así? Porque básicamente una colección puede tener documentos que tengan arrays con valores muy parecidos, así que la única forma de ser especifico, es haciendo coincidir varios valores al mismo tiempo.
## Consulta por tamaño, y tipo
Ahora veamos como podemos consultar documentos por su tipo de dato, o por el tamaño del array. Esto definitivamente es algo inusual, pero claro, la opción existe.
#### consulta por tamaño
Para este tipo de consultas, vamos a usar el operador **$size**, que nos permite definir un tamaño de elementos en un array como parametro de busqueda.
```json
db.productos.find(
	{ especificaciones: { $size: 3 } }
)
```
Entonces, esto nos va traer todos los documentos que tenga un campo "especificaciones", que tenga un array de 3 elementos.
#### consulta por tipo
Para esta consulta se va utilizar el operador **$type**, este nos permite definir un tipo de dato como un parametro de busqueda.
```json
db.productos.find(
	{ precio: { $type: "string" } }
)
```
Entonces nos trae cualquier documento que tenga un campo "precio" que tenga el valor en strings. 