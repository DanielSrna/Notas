Vamos a ver como crear un logger bien bueno, lo primero es instalar Winston:
```bash
npm install winston
```
Ahora en la carpeta config creamos un archivo, y le metemos:
```javascript
const winston = require('winston');
const path = require('path');

// Detectar el entorno
const isProduction = process.env.NODE_ENV === 'production';

// Definición de niveles y colores nativos de Winston
const customLevels = {
  levels: {
    fracaso: 0,
    exito: 1,
    proceso: 2
  },
  colors: {
    fracaso: 'red',
    exito: 'green',
    proceso: 'cyan' // Lo más cercano al azul celeste nativo
  }
};

// 1. Indicarle a Winston que registre nuestros colores personalizados
winston.addColors(customLevels.colors);

const logger = winston.createLogger({
  levels: customLevels.levels,
  format: winston.format.combine(
    winston.format.timestamp({ format: 'YYYY-MM-DD HH:mm:ss' }),
    winston.format.errors({ stack: true }),
    winston.format.splat(),
    winston.format.json()
  ),
  transports: [
    // Transporte de Consola
    new winston.transports.Console({
      level: isProduction ? 'exito' : 'proceso',
      format: winston.format.combine(
        // 2. Aplicar la colorización de Winston
        winston.format.colorize({ all: true }), 
        winston.format.timestamp({ format: 'HH:mm:ss' }),
        winston.format.printf(({ level, message, timestamp }) => {
          // El nivel y el mensaje ya vendrán coloreados gracias a colorize()
          return `${timestamp} [${level}]: ${message}`;
        })
      )
    }),
    // Transporte de Archivo (Se mantiene igual)
    new winston.transports.File({
      filename: path.join(__dirname, '../logs/logs.log'),
      level: 'fracaso',
      maxFiles: 50,
      maxsize: 5242880,
      tailable: true
    })
  ]
});

module.exports = logger;
```
Para usarlo es tan sencillo como:
```javascript
import logger from './config/logger.js'; // Ajusta la ruta según tu carpeta

// Ejemplos de uso:

logger.proceso('Iniciando conexión con la base de datos...'); 
// Salida en Azul Celeste

logger.exito('Servidor corriendo en el puerto 3000'); 
// Salida en Verde Neón

logger.fracaso('Error al intentar autenticar al usuario'); 
// Salida en Rojo y se guarda en ../logs/logs.log
```