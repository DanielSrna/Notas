Está es una funcionalidad de Helmet que nos permite que el navegador no almacene caché de una ruta en especifico. Consideremos que ya hemos instalado Helmet:
```javascript
const express = require('express');
const helmet = require('helmet');
const app = express();

// ... Otros middlewares ...

app.get('/dashboard', helmet.noCache(), (req, res) => {
  // Esta respuesta no será guardada en la caché del navegador.
  res.send('Contenido de la cuenta personal');
});
```