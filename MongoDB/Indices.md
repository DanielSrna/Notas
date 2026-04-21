Hasta ahora, hemos visto que manejar una base de datos en [[MongoDB]] es bastante sencillo, ¿No? Pues, eso es solo porque nuestros ejemplos son diminutos, una base de datos real puede contener cientos de miles de documentos, y realizar consultas ahí puede convertirse en un infierno.
Los índices son una forma de hacer las cosas mucho más rápidas, ya que es una manera de crear un "mapa de consultas" que hace mucho más rápido buscar documento utilizando un campo en especifico.
Para crear un índice, los documentos deben de cumplir con dos reglas:
1. Todos los documentos de la colección deben de tener un campo en común.
2. El campo de todos esos documentos debe ser único de preferencia.
#### Indice no único
Para crear un índice no único vamos a seguir la siguiente sintaxis:
```json
db.colección.createIndex(
	{
		campo: 1
	}
)
```
#### Indice único
Para crear un índice único usamos la siguiente sintaxis:
```json
db.usuarios.createIndex(
	{ 
		email: 1 
	}, 
	{ 
		unique: true 
	}
)
```
## Administración de índices
Ahora veamos como visualizar, y eliminar un índice, es bastante sencillo también.
#### Ver índices
```json
db.colección.getIndexes();
```
#### Eliminar un índice
```json
db.colección.dropIndex("nombre_índice");
```
> [!warning] Aclaración
> Obviamente vamos a usar los índices para hacer consultas utilizando un campo en especifico, no se debe crear un índice si no vamos a usar ese campo para luego consultar los documentos.

