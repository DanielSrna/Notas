Este manejador se utiliza colocandolo justo al final de todas las rutas en el archivo final del proyecto, pero obviamente justo antes del levantamiento del server, y la base de datos.
```javascript
// MANEJADOR GLOBAL DE ERRORES (SENSIBLE AL ENTORNO)
app.use((error, req, res, next) => {
    
    // 1. EXTRAER EL STATUS: Si el error no tiene status, asumimos 500.
    const statusCode = error.status || 500;

    // 2. LOG INTERNO: Imprimimos el error. 
    // Si es un error de validación, incluimos los detalles de express-validator.
    console.error("🚨 LOG DE ERROR:", {
        message: error.message,
        status: statusCode,
        details: error.errors || 'N/A' 
    });

    // 3. CONDICIONAL DE ENTORNO
    if (process.env.NODE_ENV === 'development') {
        
        // --- RESPUESTA EN DESARROLLO ---
        return res.status(statusCode).json({
            status: statusCode,
            message: error.message,
            errors: error.errors, // Mostramos qué campos fallaron en desarrollo
            stack: error.stack
        });

    } else {
        
        // --- RESPUESTA EN PRODUCCIÓN ---
        return res.status(statusCode).json({
            status: statusCode,
            message: statusCode === 500 
                ? "Algo salió mal en nuestros servidores" 
                : error.message,
            // En producción, solo enviamos los errores de validación si existen (status 400)
            errors: statusCode === 400 ? error.errors : undefined
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