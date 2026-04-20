Dentro de MongoDB, o bueno, dentro de cualquier base de datos, lo que queremos hacer es algo llamado CRUD, que se define como:
- **C**reate: crear nuevos elementos en la base de datos.
- **R**ead: leer los elementos en la base de datos.
- **U**pdate: actualizar los elementos de la base de datos.
- **D**elete: eliminar los elementos de la base de datos.
Esas 4 operaciones son los pilares fundamentales para administrar cualquier base de datos, y MongoDB no es la excepción. 
# C-reate, o creación de documentos
En MongoDB no se le llama creación al hecho de meter un nuevo documento, sino, **"insertar"**, es importante hacer esa distinción. 
#### Insertar un solo documento
Para insertar un solo documento, debemos de considerar que hay que tener 2 cosas listas:
1. El nombre de la colección.
2. El documento estructurado en JSON para insertar.
Por ejemplo:
```json
db.<nombreColeccion>.insertOne({
	nombre: "ejemplo",
	fecha: "hoy"
})
```
