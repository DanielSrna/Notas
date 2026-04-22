Dentro de Mongoose, las operaciones CRUD son extremadamente más sencillas que dentro de MongoDB, empecemos de una vez.
# Escritura
Para crear documentos en Mongoose, usamos la siguiente estructura:
```javascript
// Para crear un solo documento
User.create({nombre: "daniel", edad: 27})

// Para crear varios al mismo tiempo
User.create([
	{nombre: "daniel", edad: 27},
	{nombre: "loan", edad: 27}
])
```
# Lectura
La lectura solo se hace mediante **.find**, y ya, aunque se agregan nuevos valores que pueden facilitar las cosas:
```json
const jovenesConDinero = await User.find()
  .where('age').lt(30)
  .where('balance').gt(1000)
  .where('status').equals('active')
  .exec();
```
La estructura de **where** es sencilla:
```json
where("campo").operador(valor)
```
Ahí es en donde vamos a poner los criterio de busqueda, los operadores son:
**Comparaciones básicas**
- `.equals(valor)`: Igualdad (aunque si no pones nada, `where('campo', valor)` ya lo asume).
- `.gt(10)` / `.gte(10)`: Mayor que / Mayor o igual que.
- `.lt(10)` / `.lte(10)`: Menor que / Menor o igual que.
- `.ne(valor)`: No es igual a (Not Equal).

**Para Arrays y Strings**
- `.in([v1, v2])`: El valor del campo debe estar en esa lista.
- `.nin([v1, v2])`: El valor **no** debe estar en esa lista.
- `.all([v1, v2])`: El array del documento debe contener **todos** esos elementos.
- `.regex(/patrón/)`: Para buscar por expresiones regulares.

**Lógica y existencia**
- `.exists(true/false)`: Busca documentos donde el campo existe (o no).
- `.mod([divisor, resto])`: Para operaciones aritméticas de módulo.