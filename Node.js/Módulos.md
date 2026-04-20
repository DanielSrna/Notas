Los módulos son fundamentales dentro de [[Node.js]], ya que se trata de la forma en como vamos a poder aprovechar absolutamente todas las herramientas disponibles de este entorno de ejecución.
## Módulos de JavaScript
Es importante primero, saber como se manejan los módulos dentro de JavaScript, para que nosotros mismos podamos crear nuestras propias herramientas dentro de nuestra API.
##### Exportación
La fase de exportación es imprescindible, ya que con esto vamos a poder sacar de nuestro código, las herramientas que necesitamos para usarlas en otro lugar. Se hace así:

```javascript
export const sumar = (a, b) => a + b;
export const PI = 3.1416;
```
##### Importación
Ahora, ya que tenemos nuestros valores exportados, debemos traerlos de alguna manera a donde los vamos a usar, para ello los importamos así:

```javascript
import { sumar, PI } from "./math.js";
```

Ahora, si quisiéramos importar todo lo exportable que este en un archivo, y nos ahorramos tener que importar nombre por nombre, y tener cosas gigantes, le damos un alias así:

```javascript
import * as MathUtils from "./math.js";
```

Entonces lo usamos así:

```javascript
console.log(MathUtils.sumar(5, 4)); //9
//En caso de importarlos individualmente:
console.log(sumar(4,2)); //6
```
## Módulos nativos de Node.js
Node.js trae su propio conjunto de herramientas que permiten una interacción tanto con el sistema, como con sus archivos. Estás herramientas están alojadas como módulos, y son en efecto, bastante útiles.
##### Módulo process
Esté módulo trae un puñado de herramientas para saber la información esencial de en donde se está ejecutando el servidor:

**Conocer el entorno en donde se ejecuta el servidor:**
```javascript
console.log(process.env);
```

**Conocer la ruta en donde se ejecuta el servidor:**
```javascript
console.log(process.argv);
```

**Conocer el uso de memoria del servidor:**
```javascript
console.log(process.memoryUsage());
```

##### Módulo os
Este módulo nos permite saber información sobre el sistema en donde se ejecuta nuestro servidor.

> [!warning] Para poder usar este módulo, se debe de importar primero:
> `{javascript}const os = require("os");`

**Conocer el sistema operativo:**
```javascript
console.log(os.type());
```

**Conocer la carpeta principal del sistema:**
```javascript
console.log(os.homedir());
```

**Conocer cuanto tiempo lleva encendido el sistema:**
```javascript
console.log(os.uptime());
```

**Conocer la información del usuario del sistema:**
```javascript
console.log(os.userInfor());
```
## Módulo fs
Este módulo es bastante importante, y es por el cual vamos a poder interactuar con los archivos del sistema.

> [!warning] Para poder usar este módulo, se debe de importar primero:
> `{javascript}const fs = require("fs");`

**Leer un archivo:**
```javascript
fs.readFile("ruta al elemento a leer", "utf-8", función que se ejecuta al final)
```

**Cambiar el nombre de un archivo:**
```javascript
fs.rename("ruta del archivo", "nuevo nombre", función para confirmar);
```

**Agregar contenido al final de un archivo, o crear un archivo nuevo con contenido:**
```javascript
fs.appendFile("ruta del archivo, o donde quiera crear el nuevo archivo", "contenido", función para confirmar);
```

**Reemplazar el contenido de un archivo, o crear un archivo nuevo con contenido:**
```javascript
fs.writeFile("ruta del archivo, o donde quiera crear el nuevo archivo", "contenido", función para confirmar);
```

**Eliminar un archivo:**
```javascript
fs.unlink("ruta del archivo", "contenido", función para confirmar);
```

