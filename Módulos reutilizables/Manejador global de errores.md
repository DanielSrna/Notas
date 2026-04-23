Este manejador se utiliza colocandolo justo al final de todas las rutas en el archivo final del proyecto, pero obviamente justo antes del levantamiento del server, y la base de datos.
```javascript
// MANEJADOR GLOBAL DE ERRORES (SENSIBLE AL ENTORNO)
app.use((error, req, res, next) => {
    
    // 1. EXTRAER EL STATUS: Si el error no tiene status, asumimos 500.
    const statusCode = error.status || 500;

    // 2. LOG INTERNO: Siempre imprimimos el error en nuestra consola
    // para que nosotros (los devs) podamos verlo, sin importar el entorno.
    console.error(" LOG DE ERROR:", error);

    // 3. CONDICIONAL DE ENTORNO: 
    // Comprobamos la variable 'NODE_ENV' (estándar en la industria).
    if (process.env.NODE_ENV === 'development') {
        
        // --- RESPUESTA EN DESARROLLO ---
        // Enviamos todo: status, mensaje y el 'stack' (la ruta del error).
        return res.status(statusCode).json({
            status: statusCode,
            message: error.message,
            stack: error.stack // Esto te dice exactamente en qué línea falló
        });

    } else {
        
        // --- RESPUESTA EN PRODUCCIÓN ---
        // Enviamos un mensaje limpio y profesional. No revelamos el 'stack'.
        return res.status(statusCode).json({
            status: statusCode,
            message: statusCode === 500 
                ? "Algo salió mal en nuestros servidores" 
                : error.message
        });
    }
});
```
También recomiendo usar un error para las rutas no encontradas:
```javascript
app.use((req, res, next) => {
    next(createError(404, "Not Found"));
});
```