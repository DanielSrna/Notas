Las transacciones en [[Mongoose]] como vimos en el curso de MongoDB, es una forma de poder garantizar que varias operaciones CRUD se puedan hacer de forma segura, mediante el concepto de ACID.
Es técnicamente sencillo, o bueno, por lo general puedes seguir la siguiente estructura:
```javascript
// A. PREPARACIÓN: Se crea el objeto de sesión para rastrear la operación.
const session = await mongoose.startSession();

try {
    // B. APERTURA: Se marca el punto de inicio. A partir de aquí, 
    // todo es "temporal" hasta que se confirme.
    session.startTransaction();

    // C. EJECUCIÓN: Operaciones CRUD. 
    // Es CRUCIAL pasar el objeto { session } como segundo argumento; 
    // si lo olvidas, esa operación se ejecutará fuera de la transacción.
    await Modelo.create([datos], { session });
    await OtroModelo.create([datos], { session });

    // D. CONSOLIDACIÓN: Se le ordena a MongoDB aplicar todos los cambios 
    // de forma atómica y permanente en el disco.
    await session.commitTransaction();

} catch (error) {
    // E. REVERSIÓN: Si algo falló (un error de validación, conexión, etc.), 
    // este comando borra cualquier rastro de lo que se intentó hacer en el 'try'.
    await session.abortTransaction();

} finally {
    // F. LIMPIEZA: Se destruye la sesión para liberar memoria y 
    // recursos del servidor de base de datos.
    session.endSession();
}
```