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
#### Documento
Un documento es un conjunto de campos que están limitados entre llaves **{ ... }**, por ejemplo:
```json
{
	"nombre": "loan",
	"edad": 28,
	"carrera": "ingeniería de sistemas",
	"dirección": {
		"calle": 1,
		"barrio": "el primero"
	}
}
```
Todo lo anterior es un documento, y como podemos notar, dentro de este hay otro documento anidado.
#### Colección
Podría decirse que son todos los documentos que están limitados por un **[ ... ]**, y están metidos dentro del mismo archivo:
```json
[
	{
		"nombre": "Loan",
		"edad": 26,
		"carrera": "ingeniería de sistemas",
		"dirección":
		{
			"Calle": 1,
			"barrio": "primero"
		}
	},
	{
		"nombre": "Luis",
		"edad": 22,
		"carrera": "ingeniería de sistemas",
		"dirección":
		{
			"Calle": 3,
			"barrio": "tercero"
		}
	}
]
```
Se recomienda que por archivo, no se tenga más de un **[ ... ]**, en ese caso, mejor se maneja una lógica que permita separar las cosas.
# Ejemplo final
Veamos como se ve más o menos una colección en MongoDB:
```json
{
	"usuario": "loan",
	"post": [
		{
			"titulo": "mi primer post!",
			"contenido": "blablabla",
			"comentarios": [
				{
					"usuario": "Lisa",
					"comentario": "Joer que buen post"
				},
				{
					"usuario": "Luis",
					"comentario": "Muy bien!!"
				}
			]
		},
		{
			"titulo": "mi segundo post!",
			"contenido": "blablabla",
			"comentarios": [
				{
					"usuario": "Lisa",
					"comentario": "saludame la proxima vez"
				},
				{
					"usuario": "Luis",
					"comentario": "nada nuevo bro wtf"
				}
			]
		}
	]
}
```
# Instalación local
Vamos a ver como se instala de forma local, aunque tenga en cuenta que la mejor manera de usar MongoDB es usando MongoDB Atlas, ya que no hay que configurar muchas cosas, y ya todo tiene las mejores practicas de seguridad posibles.
#### Preparar dependencias, llave GPG, y repositorio
```bash
sudo apt-get update && sudo apt-get install -y gnupg curl
curl -fsSL https://pgp.mongodb.com/server-7.0.asc | sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg --dearmor
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```
#### Actualizar, e instalar MongoDB
```bash
sudo apt-get update && sudo apt-get install -y mongodb-org
```
#### Habilitar, e iniciar el servicio
```bash
sudo systemctl enable --now mongod
sudo systemctl status mongod
```
Luego de esto, presionas la "q", para podamos seguir con la verificación.
#### Verificación
```bash
mongosh --eval "db.adminCommand({ ping: 1 })"
```
Si aparece: **{ ok: 1 }**, bravo, lo instalaste correctamente.