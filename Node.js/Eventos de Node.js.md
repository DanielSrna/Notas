En [[Node.js]] tenemos una de las mejores herramientas del mundo, y son los eventos. Retomando nuestro conocimiento sobre Endpoints, podríamos decir que en:
1. En un endpoint se maneja prácticamente todo.
2. Podríamos importar servicios al archivo del endpoint, y pasarles información para que la manejen, y hacer un poco más pequeño el endpoint.
3. Si un solo endpoint hace muchas cosas, podrías estar frito, ya que el usuario tendría que esperar a que termine un montón de cosas para recibir la respuesta del endpoint.
4. Si el endpoint es muy largo, se va convertir en un infierno para ti.
Bueno como podemos notar todo parece ser un infierno por donde se mire, pero hay buenas noticias. Node.js trajo los eventos para solucionar todo eso, y son super sencillos de utilizar.
## ¿Exactamente donde debería usar un evento?
Supongamos lo siguiente:
"He creado un endpoint en donde el usuario puede realizar una compra. Al pagar con éxito, y que Stripe me devuelva True, agrego la compra a la colección "envios pendientes", y le voy a enviar un JSON al cliente diciendo "Compra con éxito, muchas gracias", y listo"
Bueno, como podemos notar, la lógica es muy cruda, y esencialmente no necesitamos usar un evento aquí, pero, ¿Qué pasaría si además de lo anterior, debemos hacer lo siguiente?
"Luego de que Stripe devuelva True, tengo que generar una factura electronica en PDF, enviar un correo de confirmación de la compra, enviar otro correo con la factura electronica, agregar el producto no solo a la colección de pendientes, sino, a la colección de "Compras realizadas" del usuario"
Como podemos notar, ahora si el endpoint parece un infierno de cosas, y es justo en esas tareas extras en donde entra en juego los eventos.
## Estructura de un evento en Node.js
La estructura es simple, debemos de accionar el evento de alguna forma, y es precisamente cuando la ruta sea usada por una petición del cliente. Pero primero, debemos considerar que los eventos se necesitan comunicar entre archivos, o sea:
1. Si nuestra ruta manda un email como acción extra, debemos de considerar sacar el servicio de email de ahí, y meterlo en un servicio a parte. En la ruta va estar el emisor del evento.
2. En el servicio a parte, va estar el escuchador.
Entonces:
- El emisor del evento está dentro del endpoint.
- El escuchador del evento, está en el archivo del servicio que debe hacer las acciones.
Para conectar todo esto, debemos de hacer algo llamado "bus", o un archivo central de eventos que podamos exportar a donde queramos usar eventos, para ello:
```javascript
// eventBus.js
const EventEmitter = require('events');

// Creamos una clase personalizada que hereda de EventEmitter
class EventBus extends EventEmitter {}

// Instanciamos un único bus (Singleton) para toda la aplicación
const eventBus = new EventBus();

module.exports = eventBus;
```

Ahora, donde queramos usar un evento, simplemente lo importamos:
```javascript
// student.controller.js
const eventBus = require('./eventBus');
const Student = require('../models/Student'); // Tu modelo de Mongoose

const registerStudent = async (req, res) => {
    try {
        // 1. Guardamos en la base de datos
        const newStudent = await Student.create(req.body);
        
        // 2. Emitimos el evento, pasando los datos del estudiante como                "payload"
        eventBus.emit('student:registered', newStudent);
        
        // 3. Respondemos rápido
        return res.status(201).json({ message: 'Estudiante registrado con           éxito' });
    } catch (error) {
        return res.status(500).json({ error: 'Error en el registro' });
    }
};
```

Como podemos notar, cuando hemos emitido el evento, también pasamos información útil para el servicio que va captar el evento, y realizar acciones:
```javascript
// notification.subscriber.js
const eventBus = require('./eventBus');
const emailService = require('./emailService');

// Escuchamos el evento
eventBus.on('student:registered', async (studentData) => {
    try {
        console.log(`Iniciando tareas en segundo plano para:                        ${studentData.nombre}`);
        // Tarea pesada 1: Generar PDF
        // Tarea pesada 2: Enviar correo
        await emailService.sendWelcomeEmail(studentData.email);
        console.log('Correo de bienvenida enviado en segundo plano.');
    } catch (error) {
        console.error('Error al enviar correo en segundo plano:', error);
    }
});
```
## Uso de servicios
Como podemos notar, los servicios ahora se separan por completo del proyecto, y solo se conectan vía un evento, pero este evento no es oficialmente una "conexión", eso quiere decir que para Node.js, el servicio es un archivo extra del proyecto que no se hace nada, para evitar esto:
```javascript
// app.js (o tu archivo principal)
const express = require('express');
const app = express();

// 🚀 El toque secreto: Importar los suscriptores para que cobren vida
require('./services/notification.subscriber'); 
require('./services/audit.subscriber'); 

// Aquí abajo ya siguen tus middlewares, rutas y configuración de Mongoose...
app.use(express.json());
```
