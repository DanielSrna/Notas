En [[Express]] podemos realizar algunas configuraciones globales que pueden cambiar bastante el comportamiento de Express, o crear nuestras propias variables globales que pueden cambiar el comportamiento también del proyecto.
## Configuraciones de Express
Hay varias configuraciones para Express, y prácticamente todas tienen la misma estructura:
```javascript
// Importamos, e instanciamos Express
const express = require ('Express');
const app = express();

// Estructura de la configuración de un setting
app.set("nombre de la configuración", valor);
```
> [!warning] CUIDADO
> Está configuración siempre va arriba de las rutas, debería ser lo primero que vaya en tu archivo app.js

|        Setting         | Descripción                                                                                                                        | Uso                                                                                             |
|:----------------------:| ---------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
|          env           | Define el entorno, y que tan callado, o escandaloso es Express.                                                                    | Sus valores pueden ser "development", o "production".                                           |
|      x-powered-by      | Básicamente esconde el hecho de que el proyecto está hecho con Express. Es por seguridad.                                          | Siempre "false" para que nadie sepa como hiciste tus cosas.                                     |
|      trust proxy       | Sirve para obtener la IP real del cliente por si usas servicios como Heroku.                                                       | Es "true", o "false".                                                                           |
|      json spaces       | Para darle un formato más bonito al JSON, aunque no afecta en nada como funciona la aplicación.                                    | Ponerle de valor "2" si estás desarrollando, sino, mejor desactivalo.                           |
|      view engine       | Es para definir el motor de plantillas de la aplicación, por si haces una landing page o algo así sencillito.                      | Aquí debes ver cual usas, el más usado es "EJS".                                                |
| case sensitive routing | Por ejemplo, si haces una ruta llamada "/admin", y otra "/Admin", si está cosa está en "true", los va tratar como rutar distintas. | Pues, depende bastante si vas a hacer rutas con el mismo nombre, pero para mi es mala practica. |
## Settings personalizados
Esto puede llegar a ser muy útil, ya que básicamente vamos a poder crear una configuración global, y a medida que construyamos las funcionalidades de nuestra aplicaciones, podemos ir haciéndolas sensibles a estas configuraciones, así podemos poner ir cambiando el comportamiento según las necesidades del servidor. Es de hecho bastante sencillo:
```javascript
app.set('max_items_por_pagina', 50);
app.set('api_version', 'v1');
app.set('nombre_proyecto', 'proyecto_de_prueba');
app.set('reintentos_DB', 5);
```
Ahora nuestra aplicación en cada configuración en donde su comportamiento cambie por esos valores, se va ver afectada. También puedes jugar con el nivel de seguridad de la aplicación.
## Arquitectura
Ahora, ¿Dónde rayos meto todas esas configuraciones? Supongamos que tienes un montón de esas cosas, lo mejor es no meterlo todo en el archivo final "server.js", sino, crear un archivo app.js:
```javascript
const express = require("express");
const app = express();

// Aquí pones las configuraciones que quieras.
// Aquí puedes llamar los servicios que usan eventos.
// Middlewares
// Rutas

module.exports = app;
```
Ahora en tu archivo server.js:
```javascript
const app = require("./app.js") // Traes ya todo configurado
const port = process.env.PORT || 3000;

app.liter(port, () => {
	console.log(`Servidor corriendo en el puerto ${port}`);
	console.log(`Entorno: ${app.get("env")}`);
})
```