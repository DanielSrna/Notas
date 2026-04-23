Este paquete nos permite prescindir por completo de try/catch, pasando de esto:
```javascript
app.post("/", async (req, res) => {
	try {
		const resultado = await User.create(req.body);
		res.status(200).json({ message: "ingresado con éxito" });
	} catch (error) {
		res.status(400).send("No se pudo ingresar")
	}
})
```
A esto:
```javascript
app.post("/", async (req, res) => {
	const resultado = await User.create(req.body);
	res.status(200).json({ message: "ingresado con éxito" });
})
```