---
title: API Reference

language_tabs:
  - shell

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introducción

Bienvenido a Bantotal Developers! Aquí encontrarás toda la documentación necesaria para utilizar nuestra API y conectarte a Bantotal. De esta forma tu producto podrá ser utilizado por más de 50 bancos en 15 países.

Te brindamos endpoints tales como: autenticación, clientes, cuentas, pagos, movimientos, entre otros. Identifica los que necesitas y comienza ahora.

# Autenticación

La autenticación es la llave que te permitirá trabajar con nuestra API. 
<aside class="notice">
Cómo estamos en una versión beta, comunicate con nosotros para recibir una llave y ser uno de los primeros developers en probar la API.
</aside>

Todos los recursos de nuestra plataforma son privados. Esto quiere decir que por ejemplo, si deseas que tu aplicación acceda a los datos de un cliente, el cliente deberá darle permisos.
Para autenticar utilizamos el conocido protocolo [oAuth 2.0](https://oauth.net/2/).

El protocolo se divide en **dos grandes partes**:

1. Obtener un access token para un cliente(mediante la aprobación del cliente).
2. Accesso a los recursos mediante el access token.

Antes de comenzar a detallar cada una de las partes, es **importante** mencionar que una vez que tu aplicación este registrada en B[DEV], tendra asignado un *client_id* y un *client_secret*.

`client_id: blablabla`

`client_secret: shhhhhhhh`

## Obtener el access token

El *access_token* o token de cliente es el tipo de token más común. Se necesita cada vez que la aplicación solicita a la API, que lea o modifique datos del Banco en nombre del cliente. Los tokens de acceso de cliente se obtienen mediante un cuadro de diálogo de inicio de sesión y requieren que el cliente conceda permiso a la aplicación.

Aunque cada plataforma genera tokens de acceso mediante API distintas, todas siguen una estrategia básica para obtener un token de cliente:

1. La aplicación solicita al cliente que se autentique en el banco, y le de permisos de acceso a sus datos.
2. El cliente se autentica y le otorga los permisos a la aplicación.
3. La aplicación recibe el *access_token*. 

### Paso 1

En este paso, la aplicación que desarrolles, deberá **redireccionar** al login del Banco para que los usuarios puedan autenticarse y posteriormente autorizar tu aplicación. Simplemente debes efectuar un redireccionamiento al URL:

> Remplaza client_id por tu client_id, y redirect_uri por la url de tu aplicación

`https://www.auth.btdevelopers.com/dialog/authorize?client_id=blablabla&response_type=code&redirect_uri=https://www.youawesomeapp.com`

### Parametros

Parametro | Descripción
--------- | -----------
client_id | Es el client_id de tu aplicación.
response_type | code – Indica que la operación deseada es obtener un código de autenticación que le permitirá a tu aplicación interactuar con el Banco
redirect_uri | URL – Es el URL de tu aplicación

## Acceso a los recursos

> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

