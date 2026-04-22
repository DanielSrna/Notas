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
  .where('age').lt(30) // Los documentos deben tener <30 en agre
  .where('balance').gt(1000) // Los documentos deben tener >1000 en balance
  .where('status').equals('active') // los documentos deben status = active
  .skip(10) // nos saltamos los 10 primeros documentos
  .limit(10) // Solo 10 documentos
  .sort('-balanace') // organiza de mayor a menor en cuanto a dinero
  .exec(); // Lanza la consulta
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
#### Paginación
Como vimos tenemos **skip** y **limit**, dos herramientas poderosas que pueden hacer magia junta, se suelen usar así:
```javascript
const pagina = 2;
const porPagina = 10;

const resultados = await User.find()
  .skip((pagina - 1) * porPagina) // Te saltas los de la página anterior
  .limit(porPagina)     // Limitas la cantidad actual
```
# Actualización
Actualizar también es sencillo, vamos a guiarnos bajo la lógica de:
- **Criterio:** es el valor por el cual vamos a encontrar uno, o varios documentos.
- **Nuevo valor**: es el nuevo valor que vamos a actualizar.
Ahora veamos como funciona:
**Model.updateOne({criterio},{nuevo contenido}):** Busca el primer documento que coincida, y le cambia el valor definido en el campo.
```javascript
Model.updateOne({'60c72b2f9f1b9e001f3f4c6b'}, {nombre: "ana"});
```
**Model.updateMany({criterio},{nuevo contenido}):** Busca todos los documentos que coincidan, y les cambia el valor definido en el campo.
```javascript
Model.updateMany({barrio: "Alameda"},{estrato: 3});
```
**Model.findOneAndUpdate({criterio}, {nuevo contenido}, {new}):** Busca el primer documento que coincida, lo actualiza, y lo devuelve como si fuera una lectura.
