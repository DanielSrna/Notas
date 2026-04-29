Cuando enviamos tokens al cliente, es usual que simplemente los mandemos como una respuesta de tipo `send` pero esto no es del todo correcto, ya que en ese caso, el cliente tendría que guardar el token en `localStorage`, y ese lugar es bastante inseguro. Para evitar lo anterior, vamos a enviar el refresh token, por medio de las cookies, y también vamos a recibirlo por ahí.

Para poder hacer uso de las cookies desde el backend, vamos a tener que instalar:

```jsx
npm install cookie-parser
```

Luego en el archivo principal:

```jsx
app.use(cookieParser());
```

Listo, ya vamos a poder administrar cookies.

# Enviando el refresh token

Una vez instalado, ya vamos a poder enviar cookies, veamos un ejemplo, realmente es sencillo:

```jsx
res.cookie("refreshToken", refreshToken, {
  httpOnly: true,    // ✅ No accesible por JS (protección XSS)
  secure: true,      // ✅ Solo HTTPS en producción
  sameSite: "strict" // ✅ Previene CSRF
});
```

Como podemos ver, los parametros de la respuesta son:

- `“nombreDeLaCookie”`: en este caso es “refreshToken”.
- `valorDeLaCookie`: en este caso es el refresh token.
- `{opciones de la cookie}`: aquí vamos a configurar:
    - `httpOnly`: solo se permite manipular la cookie si es para enviarla en una petición HTTP.
    - `secure`: solo se permite mover la cookie por medio de HTTPS.
    - `sameSite`: este puede chocar con CORS, así que cuidado, ya que solo permite usar la cookie, si tanto el dominio del front, y el back coinciden.

# Leer una cookie

Leer una cookie es muy sencillo, solo la vamos a extraer así de la petición:

```jsx
const token = req.cookies.refreshToken
```

Listo, es importante validar entonces la cookie.