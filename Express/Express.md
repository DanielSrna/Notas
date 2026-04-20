Se trata del framework más famoso de [[Node.js]], este trae una caja de herramientas muy grande que nos permite realizar el desarrollo de la API muchísimo más fácil.
## Instalación
Se instala como cualquier paquete de NPM:
```bash
npm install express
```
## Uso
Para poder usarlo, es importante importarlo e instanciarlo, para luego poder usar la instancia, en este caso es la instancia `{javascript}app`:
```javascript
const express import "express"; //importamos
const app = express(); //instanciamos
```

> [!warning] CUIDADO
> Está forma de importar Express, es una forma novedosa, pero que requiere ir al archivo Package.json, y agregar la linea: `{javascript}"type": "module",`:
```javascript
{
  "name": "tu-app",
  "version": "1.0.0",
  "type": "module",   // <- Justo aquí
  "dependencies": {
    "express": "^4.18.2"
  }
}
```
## Levantamiento del servidor
Para levantar nuestro servidor, ya no necesitamos para la parafernalia de Node.js, sino, simplemente incluir al final de nuestro documento principal:
```javascript
const PUERTO = process.env.PORT || 3000;
app.listen(PUERTO, () => {
	console.log(`El servidor está en linea...`);
});
```

> [!info] Consideraciones
> Cuando hablamos de archivo principal, nos referimos a que la estructura de trabajo en Express, tiende a ser extremadamente modular, separando casi todo en diferentes archivos y carpetas, pero al final, todo culmina en un único archivo llamado "app.js". Justo aquí se debe levantar el servidor también. Esto lo veremos más tarde también.

