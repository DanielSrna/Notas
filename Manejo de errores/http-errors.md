Es un paquete de NPM que nos ayuda a crear errores de una forma muy elegante. Vamos a instalarlo así:
```bash
npm install http-errors
```
Así escribiríamos un error tradicionalmente:
```javascript
send.status(401).json({ message: "No autorizado" })
```
Ahora es así:
```javascript
const createError = require('http-errors');

return next(createError(401, "No autorizado"));
```