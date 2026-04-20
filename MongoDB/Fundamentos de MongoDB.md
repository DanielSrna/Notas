MongoDB es una base de datos NoSQL de tipo documental, que escala horizontalmente. Te preguntaras, ¿Qué rayos es escalar horizontalmente, y por qué es mejor que hacerlo verticalmente?
Es muy sencillo, si la base de datos escala horizontalmente, puedes dividirla en varios computadores, mientras que si escala verticalmente, no puedes dividirla, y el computador en donde se aloja debe mejorarse cada vez más hasta tener un demonio monolítico. 
# Conceptos básicos
Hay varias cosas que debemos de considerar antes de hundirnos de cabeza en MongoDB, lo primordial es entender que MongoDB no almacena imágenes, vídeos, o archivos en general, sino, algo llamado **JSON**, se trata básicamente de una estructura de texto super compatible con casi todos los lenguajes de programación.
```json
{
	"Elemento": "valor",
	"otro_elemento": "otro valor",
	"elemento_numerico": 28
}
```
Básicamente, así funcionan los **JSON**, aunque también podemos hacer otras cosas aún más locas.
#### Anidamiento en JSON
Como su nombre indica, podemos meter un JSON dentro de otro:
```json
{
	"nombre": "daniel",
	"medidas": {
		"peso": 97,
		"alto": 183,
		"calzado": 43,
		"camiseta": "XL"
	}
}
```
#### Listas en JSON
Si tuviéramos que meter varios JSON dentro de un JSON, pero **todos están relacionados por algo**, usamos una lista:
```json
{
	"nombre": "loan",
	"peliculas_favoritas": [
		{
			"nombre": "el padrino",
			"año": 1972
		},
		{
			"nombre": "el mago de Oz",
			"año": 1979
		},
		{
			"nombre": "ciudadano kane",
			"año": 1941
		}
	]
}
```
Bien, eso es básicamente todo lo que se puede hacer en un JSON, porque pues, se supone que es una estructura que solo transmite información escrita, y ya.
# Estructura de MongoDB
Ya vimos que MongoDB guarda y envia únicamente elementos de tipo JSON, pero, ¿Cómo rayos se estructuran?
#### Clave, valor, y campo
Son las unidades mínimas de MongoDB, por ejemplo:
```json
{
	"nombre": "loan"
}
```
En este caso:
- **Clave:** "nombre:"
- **Valor:** "loan"
- **Campo:** "nombre": "loan"
Sencillo, ¿No?