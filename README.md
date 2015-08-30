# Authentication

## Autenticación con Token

El usuario se autentica a la app, con usuario/password, o a través de un proveedor como puede ser Twitter, Facebook o Google por ejemplo

A partir de entonces, cada petición HTTP que haga el usuario va acompañada de un Token en la cabecera. Este Token no es más que una firma cifrada que permite a nuestro API identificar al usuario

Pero este Token no se almacena en el servidor, si no en el lado del cliente (por ejemplo en localStorage o sessionStorage) y el API es el que se encarga de descrifrar ese Token y redirigir el flujo de la aplicación en un sentido u otro

Como los tokens son almacenados en el lado del cliente, no hay información de estado y la aplicación se vuelve totalmente escalable. Podemos usar el mismo API para diferentes apliaciones (Web, Mobile, Android, iOS, ...) solo debemos preocuparnos de enviar los datos en formato JSON y generar y descrifrar tokens en la autenticación y posteriores peticiones HTTP a través de un middleware

También añade **más seguridad**. Al no utilizar cookies para almacenar la información del usuario, podemos evitar ataques CSRF (Cross-Site Request Forgery) que manipulen la sesión que se envía al backend. Por supuesto podemos hacer que el token expire después de un tiempo lo que le añade una capa extra de seguridad

### JSON Web Tokens

El estándar para este tipo de autenticación es utilizar JSON Web Tokens (JWT). Al igual que los APIs, el formato JSON es agnóstico del lenguaje, y podemos utilizar el que queramos

El formato de un JWT está compuesto por 3 strings separados por un punto **.** algo así como:

```sh
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI1NGE4Y2U2MThlOTFiMGIxMzY2NWUyZjkiLCJpYXQiOiIxNDI0MTgwNDg0IiwiZXhwIjoiMTQyNTM5MDE0MiJ9.yk4nouUteW54F1HbWtgg1wJxeDjqDA_8AhUPyjE5K0U  
```

**Cada String significa una cosa**

+ **Header**: Define el tipo, en este caso un JWT y la codificación utilizada. Comunmente es el algoritmo HMAC SHA256, El contenido sin codificar es el siguiente:

```sh
{
    "typ": "JWT",
    "alg": "HS256
}
```

+ **Payload**: EL Payload está compuesto por los llamados JWT Claims donde irán colocados la atributos que definen nuestro token. Exiten varios que puedes consultar aquí, los más comunes a utilizar son: 
sub: Identifica el sujeto del token, por ejemplo un identificador de usuario.
iat: Identifica la fecha de creación del token, válido para si queremos ponerle una fecha de caducidad. En formato de tiempo UNIX
exp: Identifica a la fecha de expiración del token. Podemos calcularla a partir del iat. También en formato de tiempo UNIX

```sh
{
    "sub": "54a8ce618e91b0b13665e2f9",
    "iat": "1424180484",
    "exp": "1425390142"
}
```

También podemos añadirle más campos, incluso personalizados, como pueden ser el rol del usuario, entre otros

```sh
{
    "sub": "54a8ce618e91b0b13665e2f9",
    "iat": "1424180484",
    "exp": "1425390142",
    "admin": true,
    "role": 1
}
```

+ **Signature**: La firma esstá formada por los anteriores componentes (Header y Payload) cifrados en Base64 con una clave secreta (almacenada en nuestro backend). Así sirve de Hash para comprobar que todo está bien

```sh
HMACSHA256(  
    base64UrlEncode(header) + "." + 
    base64UrlEncode(payload), secret
)
```

Esto se puede comprobar en [JWT.io] 

[JWT.io]: http://jwt.io/
