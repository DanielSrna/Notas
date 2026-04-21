Muchas veces cuando realizamos una petición a la base de datos, está petición puede estar cargada de muchas consultas que hacen varias cosas al mismo tiempo, y esto es completamente riesgoso.
Por ejemplo si en nuestra API tenemos una ruta que crea un usuario nuevo, y tienes que ir a meter al usuario en diferentes colecciones, o eliminarlo de otras, cambiar estados, etc. Imagina que a mitad de todas esas peticiones parpadee el router, y se joda toda, queda todo el proceso a medias, y el usuario queda "medio" registrado.
Otro ejemplo aún más jodido, imagina una transacción de dinero:
1. Se te saca dinero de la cuenta.
2. Se va el internet.
3. No llega el pago.
4. Se jodió tu dinero.
## ACID
Para evitar lo anterior, existe el concepto de ACID que es un conjunto de ideas de desarrollo que se deben aplicar a las peticiones de una base de datos, vamos a ver de que se trata.
#### Atomicidad
Nos dice que si alguno de los pasos de la transacción ha fallado, todo se cancela, y se devuelve todo a como estaba antes. Es como una bomba nuclear, la cual explota y evapora todo, o no explota y todo está bien.
#### Consistencia
Esto nos dice que en ningún momento de la transacción se deben de romper las reglas de la base de datos. O sea, si las cosas empiezan bien, deben terminar bien.
#### Aislamiento
Esto nos dice que todas las operaciones en la base de datos que se hacen en una transacción, ocurren en un espacio imaginario de la base de datos, y solo cuando todo está correcto, se sueltan todos los cambios al mismo tiempo.
#### Durabilidad
Aquí nos explican que una vez realizada una transacción, no hay vuelta atrás, los cambios ya fueron hechos, y todo se ve reflejado. Una transacción es cancelable solo mientras ocurre.
## Estructura de una transacción
Este es un ejemplo para mi mismo, puesto que yo voy a usar Mongoose, pero pues, el comportamiento es general, e intrínseco a la idea de una transacción para cualquier otro entorno como el driver oficial de [[MongoDB]].
```javascript
// 1. EL CANAL (Abrir la sesión)
const session = await mongoose.startSession();

try {
    // 2. EL CRONÓMETRO (Iniciar el registro de cambios)
    session.startTransaction();

    // 3. LA ACCIÓN (Operaciones vinculadas a la sesión)
    // Importante: Cada operación DEBE llevar la session al final
    await Modelo.operacion([datos], { session });
    await OtroModelo.operacion([datos], { session });

    // 4. EL SELLO (Guardar todo definitivamente)
    await session.commitTransaction();
    console.log("¡Todo salió perfecto!");

} catch (error) {
    // 5. EL RECORDE (Borrar lo que se alcanzó a hacer)
    await session.abortTransaction();
    console.error("Algo falló, no se guardó nada.");

} finally {
    // 6. EL CIERRE (Liberar la memoria)
    session.endSession();
}
```