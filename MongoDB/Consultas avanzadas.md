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
```
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