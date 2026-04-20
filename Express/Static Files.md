Los static files son archivos que [[Express]] puede mandar al cliente, sin necesidad de procesarlos, ejecutarlos, y sin lógica en ellos. Básicamente, se entregan archivos que realmente no tienen un carácter valioso en cuanto a seguridad, pero pueden ser importantes para el funcionamiento general del cliente.
# Carpeta Public
La carpeta public es una carpeta que le dice a Express: "Todo lo que está aquí es seguro, y puede compartirse sin problemas", es como un excepción contraría al archivo .env.
# ¿Cómo se usa?
Es bastante simple, se tiene que hacer uso de "path" para poder indicar a Express en donde va estar alojada la carpeta:
```javascript
const path = require('path');

// 'public' es el nombre de la carpeta en tu proyecto
app.use(express.static(path.join(__dirname, 'public')));

```
# ¿Ahora qué?
Bueno, puedes almacenar ahí prácticamente todos los recursos de la interfaz de la web. Básicamente la regla que debes respetar para poner ahí es:
"Se tiene que colocar en public, todo aquel material que compartan en común todos los usuarios, o sea, todo lo que sea publico."
# Arquitectura
Para almacenar la información deberías de seguir un patrón parecido a este:
```json
mi-proyecto/
  ├── app.js
  └── public/
        ├── css/
        │    └── estilos.css
        └── img/
             └── logo.png

```
# ¿Cómo se consume?
Me imagino que debes estar imaginando que se consume por medio de una URL como si fuera una petición normal, y estas en lo cierto, PERO, debes considerar que la carpeta "public" realmente no existe para el front end, así que por ejemplo, para acceder al logo que definimos en el ejemplo anterior:
```json
localhost:3000/img/logo.png
```
