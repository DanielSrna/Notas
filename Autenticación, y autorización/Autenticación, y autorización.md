La autenticación es el proceso por el cual vamos a identificar a el usuario, para ello es importante:
1. Que el usuario se registre.
2. Que se confirme su registro.
3. Que haga login con sus credenciales.
Si el usuario cumple con todo lo anterior, ya debe estar autenticado. Aunque, ¿Exactamente que significa eso?
## ¿Por qué es necesario?
Autenticar un usuario en una plataforma se convierte necesario solo en la situación en donde debamos de almacenar sus datos dentro del almacenamiento persistente. Por ejemplo, con una red social, una plataforma de compras, una plataforma educativa, etc.
## ¿Cómo se logra?
Se logra de dos formas principales, y ambas son buenas practicas, de hecho muchas aplicaciones usan todas dos juntas.
#### JWT puro
Básicamente es un flujo en donde se prioriza el uso de tokens generados por la misma API:
1. El usuario hace login, y se le envia un AccesToken, y un RefreshToken. Estos tokens son generados por una clave secreta que vive dentro de nuestro archivo .env.
2. Cuando el usuario intente entrar a una ruta protegida, se valida su AccesToken, y si tiene los permisos, se le deja pasar. Si no los tiene se rechaza, y si está vencido se renueva con su RefreshToken.
#### Oauth2.0
Es casi igual que lo anterior, solo que:
1. El usuario hace login con un servidor de autenticación externo como Google.
2. Google nos dice por medio de un túnel de comunicación seguro, que ha sido un login exitoso, así que nosotros le damos un AccesToken, y un RefreshToken. 