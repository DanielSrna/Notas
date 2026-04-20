Dentro del desarrollo backend, casi ningún operación se ejecuta con datos locales, sino, que casi siempre todo gira en torno a peticiones, o esperar a que otro servicio que no hace técnicamente parte de nuestro proyecto, responda. Así que para no romper todo porque estábamos esperando algo y se tardo mucho, o de plano respondió con un error, se usa la asíncrona. 

## Async y Await
Estás son básicamente las palabras mágicas que vamos a escuchar en todos lados mientras trabajos en [[Node.js]], puesto que es la forma más fácil y elegante de poder manejar promesas, su estructura es:

```javascript
async function ejecutarPromesa () {
	try {
		const valor = await miPromesa()
		console.log(`Promesa resuelta con éxito`)
	} catch (error) { 
		console.log (error) 
	}
}

// Ejecutamos:

ejecutarPromesa();
```

Es realmente sencillo, simplemente creamos una función de espera de forma asincrona, y en ella ejecutamos la promesa, y aguardamos su resultado. Usamos Try/catch para realizar operaciones según la promesa se resuelva, o capturar el error.