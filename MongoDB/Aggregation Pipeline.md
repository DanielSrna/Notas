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
		// Etapa de ordenamiento.
	},
	{
		// Etapa de presentación.
	}
])
```
## Etapas de un aggregation pipeline
Ahora veamos las cuatro etapas en orden, en realidad es bastante sencillo.
#### Etapa de filtrado ($match)
En esta etapa lo que queremos hacer, es agarrar los documentos de una colección, y seleccionar los que nos interesa analizar, para ello vamos a utilizar un campo en común que tenga cada uno. Pero, es importante decir que "campo en común", no quiere decir que tengan el mismo valor, sino, una clave identica. 