Se trata del manejador de paquetes de [[Node.js]], y tiene como finalidad, poder instalar en nuestro proyecto un sin fin de herramientas que hacen las cosas muchísimo más sencillas para nosotros.
## Conceptos claves
Ahora, veamos algunos conceptos claves a considerar para trabajar con NPM:
1. **Paquete:** es básicamente uno de los [[Módulos]] de los que hablamos antes, lleno de herramientas que podemos usar en nuestro proyecto.
2. **Dependencia:** son los paquetes que necesitan algunos paquetes para funcionar, y también se considera que los paquetes que usemos para nuestro proyecto, son esencialmente dependencias también.
## Funcionamiento
Ahora veamos como funciona, tenga en cuenta que todo se hacer desde la terminal:
**Iniciar NPM:**
```zsh
npm init 
```

**Inicio rápido:**
```bash
npm init -y
```

**Instalar un paquete:**
```bash
npm install nombreDelPaquete
```

**Desinstalar un paquete:**
```bash
npm uninstall nombreDelPaquete
```

**Actualizar un paquete:**
```bash
npm install nombreDelPaqueteYaInstalado
```

**Actualizar todos los paquetes:**
```bash
npm update
```

> [!warning] ¡CUIDADO!
> Cuando se actualiza un paquete, o todos dentro de NPM, es importante considerar que se puede convertir en un elemento incompatible con los otros paquetes, o con nuestro proyecto.

## Package-lock.json, y Package.json
Esto archivos sirven para revisar que hemos estado instalando dentro de NPM, y las versiones de cada paquete. Además, también funciona para que cuando alguien quiera integrarse a nuestro trabajo, solo tenga que descargar estos archivos, y escribir: `{bash}npm install` para tener todos los paquetes listos.