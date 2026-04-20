Para emplear este módulo, vamos a la carpeta "Middlewares", y creamos un archivo llamado:
```bash
gcsUploader.js
```
Instalamos con NPM:
```bash
npm install multer file-type sharp @google-cloud/storage
```
Ahora copiamos, y pegamos en el archivo, el siguiente código:
```javascript
import multer from 'multer';
import { fileTypeFromBuffer } from 'file-type';
import { Storage } from '@google-cloud/storage';
import sharp from 'sharp';

// ==========================================
// 1. Configuración de Google Cloud Storage
// ==========================================
const storage = new Storage({
  projectId: process.env.GCLOUD_PROJECT_ID,
  keyFilename: process.env.GCLOUD_KEY_FILE,
});
const bucket = storage.bucket(process.env.GCLOUD_BUCKET_NAME);

// ==========================================
// 2. Configuración de Multer (En Memoria)
// ==========================================
export const uploadLocal = multer({
  storage: multer.memoryStorage(),
  limits: { fileSize: 10 * 1024 * 1024 }, // 10MB
});

// ==========================================
// 3. Middleware de Procesamiento y Subida a GCS
// ==========================================
export const uploadToGCS = ({ 
  allowedMimeTypes = [], 
  width, 
  height, 
  fit = 'cover',
  forceWebp = true // Activado por defecto para optimizar imágenes
} = {}) => {
  return async (req, res, next) => {
    if (!req.file) {
      return res.status(400).json({ error: 'No se proporcionó ningún archivo.' });
    }

    try {
      // Validación real del formato
      const fileInfo = await fileTypeFromBuffer(req.file.buffer);

      if (!fileInfo || (allowedMimeTypes.length > 0 && !allowedMimeTypes.includes(fileInfo.mime))) {
        return res.status(415).json({ error: 'Tipo de archivo no permitido o corrupto.' });
      }

      let finalBuffer = req.file.buffer;
      let finalExtension = fileInfo.ext;
      let finalMime = fileInfo.mime;

      // ==========================================
      // a. Procesamiento con Sharp (Solo imágenes)
      // ==========================================
      if (fileInfo.mime.startsWith('image/')) {
        let pipeline = sharp(req.file.buffer);

        // Si se pasaron dimensiones, redimensionamos
        if (width || height) {
          pipeline = pipeline.resize({
            width: width ? parseInt(width) : null,
            height: height ? parseInt(height) : null,
            fit: fit
          });
        }

        // Si forzamos WebP, aplicamos conversión y compresión
        if (forceWebp) {
          pipeline = pipeline.webp({ quality: 80 }); 
          finalExtension = 'webp';
          finalMime = 'image/webp';
        }

        // Ejecutamos el pipeline de Sharp
        finalBuffer = await pipeline.toBuffer();
      }

      // ==========================================
      // b. Subida a Google Cloud Storage
      // ==========================================
      const uniqueName = `${Date.now()}-${Math.round(Math.random() * 1e9)}.${finalExtension}`;
      const blob = bucket.file(uniqueName);
      
      const blobStream = blob.createWriteStream({
        resumable: false,
        contentType: finalMime,
      });

      blobStream.on('error', (err) => {
        console.error('Error al subir a GCS:', err);
        next(err);
      });

      blobStream.on('finish', () => {
        // Construimos la URL pública y la adjuntamos al request
        req.file.gcsUrl = `https://storage.googleapis.com/${bucket.name}/${blob.name}`;
        req.file.verifiedType = { ext: finalExtension, mime: finalMime };
        
        next(); // Pasamos al controlador final
      });

      // Iniciamos la transmisión del buffer hacia la nube
      blobStream.end(finalBuffer);

    } catch (error) {
      console.error('Error procesando el archivo:', error);
      next(error);
    }
  };
};
```
Ahora, para usarlo, vamos a las rutas, y lo empleamos así:
```javascript
import { Router } from 'express';
import { uploadLocal, uploadToGCS } from '../middlewares/gcsUploader.js';

const router = Router();

// Ejemplo: Subida de fotos de perfil (Redimensionadas a 500x500 y metadata eliminada)
router.post(
  '/upload-profile',
  uploadLocal.single('image'),
  uploadToGCS({
    allowedMimeTypes: ['image/jpeg', 'image/png'],
    width: 500,
    height: 500,
    fit: 'cover' // Recorta para llenar el cuadrado
  }),
  (req, res) => {
    res.json({ url: req.file.gcsUrl });
  }
);

// Ejemplo: Subida de banners (Ancho fijo de 1200px, alto proporcional)
router.post(
  '/upload-banner',
  uploadLocal.single('banner'),
  uploadToGCS({
    allowedMimeTypes: ['image/webp', 'image/jpeg'],
    width: 1200
    // Al no pasar height, Sharp mantiene la relación de aspecto
  }),
  (req, res) => {
    res.json({ url: req.file.gcsUrl });
  }
);

// Ejemplo: Subir un reporte o documento (No usa Sharp, solo valida y sube)
router.post(
  '/upload-report',
  uploadLocal.single('documento'),
  uploadToGCS({
    allowedMimeTypes: ['application/pdf'] 
    // Al no pasar width ni height, el PDF pasa directo sin errores
  }),
  (req, res) => {
    res.json({ url: req.file.gcsUrl });
  }
);
```
# Valores
Este módulo requiere que configures como parámetro del middleware los valores de:
- **allowedMimeTypes:** aquí indicamos que tipo de archivos van a ser recibidos.
- **width, y height:** aquí indicamos en que tamaño vamos a redimensionar la imagen.
- **fit:** aquí indicamos como queremos rellenar el nuevo tamaño.
Los valores de las variables de entorno son:
```javascript
GCLOUD_PROJECT_ID
GCLOUD_KEY_FILE
GCLOUD_BUCKET_NAME
```
Las vamos a sacar de **Google Storage.** 
> [!important] ¡Recuerda!
> Es importante que guardes el path de donde quedo guardado el archivo del Google Storage, dentro de la base de datos, en la colección, o registro correspondiente.
> 
> También, recuerda que puedes agregarle unos buenos logs con winston para mostrar en la terminal si algo relacionado con el análisis, procesamiento, o subida del archivo presento una falla, y de qué tipo.
