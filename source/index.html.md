---
title: API Reference

language_tabs:
  - shell

toc_footers:
  - v0.1

includes:
  - errors

search: true
---

# Introducción

Bienvenido a ¡**Bantotal Developers**! Aquí encontrarás toda la documentación necesaria para utilizar nuestra API y conectarte a Bantotal. De esta forma tu producto podrá ser utilizado por más de 50 bancos en 15 países.

Te brindamos recursos tales como: autenticación, clientes, cuentas, pagos, movimientos, entre otros. 

Identifica los que necesitas y comienza ahora ;)

# Autenticación

La autenticación es la llave que te permitirá trabajar con nuestra API. 

Todos los recursos de nuestra plataforma son privados. Esto quiere decir que por ejemplo, si deseas que tu aplicación acceda a los datos de un cliente, el cliente deberá darle permisos.
Para autenticar utilizamos el conocido protocolo [oAuth 2.0](https://oauth.net/2/).

El protocolo se divide en **dos grandes partes**:

1. Obtener un *access_token* para un cliente (mediante la aprobación del cliente).
2. Accesso a los recursos mediante el *access_token*.

Antes de comenzar a detallar cada una de las partes, es **importante** mencionar que una vez que tu aplicación este registrada en B[Developers], tendra asignado un *client_id* y un *client_secret*.

`client_id: blablabla`

`client_secret: shhhhhhhh`

<aside class="notice">
Cómo estamos en una versión beta, comunicate con nosotros para recibir un client_id y un client_secret. Se uno de los primeros developers en probar la API.
</aside>

## Obtener el access token

El *access_token* o token de cliente es el tipo de token más común. Se necesita cada vez que la aplicación solicita a la API, que lea o modifique datos del Banco en nombre del cliente. Los *access_token* se obtienen mediante un cuadro de diálogo de inicio de sesión y requieren que el cliente conceda permiso a la aplicación.

Aunque cada plataforma genera *access_token* mediante API distintas, todas siguen una estrategia básica para obtenerlo:

1. La aplicación solicita al cliente que se autentique en el banco, y le de permisos de acceso a sus datos.
2. El cliente se autentica y le otorga los permisos a la aplicación.
3. La aplicación recibe el código de autorización.
3. La aplicación intercambia el código de autorización por el *access_token*. 

### Paso 1

En este paso, la aplicación que desarrolles, deberá **redireccionar** al login del Banco para que los usuarios puedan autenticarse y posteriormente autorizar tu aplicación. Simplemente debes efectuar un redireccionamiento al URL:

> Remplaza *blablabla* por tu client_id, y *https://www.youawesomeapp.com* por la url de tu aplicación

`http://www.auth.btdevelopers.com:8090/dialog/authorize?client_id=blablabla&response_type=code&redirect_uri=https://www.youawesomeapp.com/`

<aside class="warning">
Cómo veras, no utilizamos https. Esto es porque este es un ambiente “arena” y es la versión beta.
En la próxima versión, y en las versiones reales disponibles en los clientes, por supuesto que será https.
Otro detalle, es el puerto en la URL, esto tambien lo corregiremos.
</aside>

### Parametros

Parametro | Descripción
--------- | -----------
client_id | Es el client_id de tu aplicación.
response_type | code – Indica que la operación deseada es obtener un código de autenticación que le permitirá a tu aplicación interactuar con el Banco.
redirect_uri | URL – Es el URL de tu aplicación.

Una vez que redirecciones al cliente al URL, ya puedes dejar de preocuparte, nosotros nos encargamos de la autenticación :)

### Paso 2

Una vez que el cliente complete el proceso de autenticación en la pagina del Banco, será redireccionado a la página de autorización de tu aplicación. Allí se le presentarán todos los permisos solicitados junto a la descripción de tu aplicación.

De esto tampoco debes preocuparte :D

### Paso 3

Otorgados los permisos, el cliente será redireccionado al Redirect URI (el que nos pasaste en el URL anterior) con el código de autorización, de la siguiente forma:

`http://www.youawesomeapp.com/?code=SERVER_GENERATED_AUTHORIZATION_CODE`

### Paso 4

> Aquí un ejemplo:

```shell
curl -X POST 
  -H "Content-Type: application/x-www-form-urlencoded" 
  -H "Authorization: Basic YWJjMTIzOnNzaC1zZWNyZXQ=" 
  -d 'client=blablabla&code=SERVER_GENERATED_AUTHORIZATION_CODE&redi
  rect_uri=https://www.youawesomeapp.com/&grant_type=authorization_code'
  "http://www.auth.btdevelopers.com:8090/oauth/token"
```
> El POST te retornará la siguiente estructura JSON:

```json
{
  "access_token": "MOY62IRjcTaYpCfh4nAJmXnV",
  "token_type": "Bearer"
}
```

Solo resta el último paso. Aquí deberas intercambiar el `code` obtenido por el 
`access_token`.

Para ello deberás realizar el siguiente POST:

`http://www.auth.btdevelopers.com:8090/oauth/token`

Como verás en el ejemplo de la derecha, uno de los parametros pasados en el Header es Authorization. **En este comando esperamos que nos pases tu client_id y tu client_secret "hasheados"**. [Aquí](https://es.wikipedia.org/wiki/Autenticaci%C3%B3n_de_acceso_b%C3%A1sica) puedes ver como generar el hash.

Parametro | Descripción
--------- | -----------
client | Es el client_id de tu aplicación.
code | El código de autorización obtenido en el paso anterior.
redirect_uri | URL – Es el URL de tu aplicación.
grant_type | authorization_code – Indica que la operación deseada es intercambiar el “code” por un access_token.

¡Listo! Como verás en la respuesta obtendrás el *access_token* para realizar llamadas a nuestra API, y así obtener acceso a los datos privados del cliente.

## Acceso a los recursos

```shell
# El access_token obtenido en el paso anterior, 
se pasa en Authorization: Bearer access_token
curl -X GET 
  -H "Authorization: Bearer hUEM8g7EK4u8hxFYpIw1YPlkjjrY" 
  "http://www.api.btdevelopers.com:8080/v0/client"
```
> El GET te retornará la siguiente estructura JSON:

```json
{
  "_id": "5818fcd862b916439c226657",
  "phone": "094500500",
  "address": "Av. Italia 456",
  "email": "mvarela@btd.com",
  "surname": "Varela",
  "name": "Martín",
  "document": "48039165",
  "documentType": {
    "_id": "5818fcd662b916439c226653",
    "name": "Cedula de Identidad"
  },
  "country": {
    "_id": "5818fcd662b916439c226654",
    "name": "Uruguay"
  }
}
```

Ahora que ya tienes el `access_token`, podrás acceder a los recursos.

Veamos un ejemplo.

<aside class="notice">
Para el ejemplo utilizamos la consulta de cliente, pero fue solamente un ejemplo. A partir de ahora, cada recurso que quieras consumir, será de la misma manera, enviandole el access_token del cliente en el Header.
</aside>

# Clientes

En esta sección, encontrarás todos los recursos asociados a los datos de los clientes. Por ejemplo, sus datos personales registrados en el banco, o el alta de un nuevo cliente.

## Obtener datos

```shell
curl -X GET 
  -H "Authorization: Bearer hUEM8g7EK4u8hxFYpIw1YPlkjjrY" 
  "http://www.api.btdevelopers.com:8080/v0/client"
```

> El GET te retornará la siguiente estructura JSON:

```json
{
  "_id": "5818fcd862b916439c226657",
  "phone": "094500500",
  "address": "Av. Italia 456",
  "email": "mvarela@btd.com",
  "surname": "Varela",
  "name": "Martín",
  "document": "48039165",
  "documentType": {
    "_id": "5818fcd662b916439c226653",
    "name": "Cedula de Identidad"
  },
  "country": {
    "_id": "5818fcd662b916439c226654",
    "name": "Uruguay"
  }
}
```

Este endpoint retorna los datos del cliente autenticado.

### HTTP Request

`GET http://www.api.btdevelopers.com:8080/v0/client`

# Cuentas

En esta sección, encontrarás todos los recursos asociados a las cuentas de los clientes. Por ejemplo, consulta de sus cuentas, alta de cuentas, consulta movimientos, etc.

## Obtener cuentas

```shell
curl -X GET 
  -H "Authorization: Bearer hUEM8g7EK4u8hxFYpIw1YPlkjjrY" 
  "http://www.api.btdevelopers.com:8080/v0/accounts"
```

> El GET te retornará la siguiente estructura JSON:

```json 
[
  {
   "_id": "5818fcd862b916439c226659",
   "description": "Cuenta de ahorro",
   "balance": "6570",
   "currency": "USD",
   "productType": "CA"
  },
  {
   "_id": "5818fcd862b916439c22665a",
   "description": "Cuenta de sueldo",
   "balance": "70500",
   "currency": "$",
   "productType": "CA"
  }
]
```

Este endpoint retorna las cuentas del cliente autenticado.

### HTTP Request

`GET http://www.api.btdevelopers.com:8080/v0/accounts`

## Obtener una cuenta

```shell
curl -X GET 
  -H "Authorization: Bearer hUEM8g7EK4u8hxFYpIw1YPlkjjrY" 
  "http://www.api.btdevelopers.com:8080/v0/accounts/<ID>"
```

> El GET te retornará la siguiente estructura JSON:

```json 
{
 "_id": "5818fcd862b916439c226659",
 "description": "Cuenta de ahorro",
 "balance": "6570",
 "currency": "USD",
 "productType": "CA"
}
```

Este endpoint retorna la información de una única cuenta, la cual es filtrada mediante la URL.

### HTTP Request

`GET http://www.api.btdevelopers.com:8080/v0/accounts/<ID>`

Parametro | Descripción
--------- | -----------
ID | Identificador de la cuenta.

## Crear cuenta de ahorro

```shell
curl -X POST 
  -H "Authorization: Bearer hUEM8g7EK4u8hxFYpIw1YPlkjjrY"  
  -H "Content-Type: application/x-www-form-urlencoded" 
  -d 'currency=USD&description=Chanchita' 
  "http://www.api.btdevelopers.com:8080/v0/savingAccount"
```

> El POST te retornará la siguiente estructura JSON:

```json 
{
  "description": "Chanchita",
  "balance": "0",
  "currency": "USD",
  "productType": "CA",
  "client": "5818fcd862b916439c226657",
  "_id": "581cd3b7caf8ac0e2d1747c3"
}
```

Este endpoint da de alta una cuenta de ahorro al cliente.

### HTTP Request

`POST http://www.api.btdevelopers.com:8080/v0/savingAccount`

Parametro | Descripción
--------- | -----------
currency | Moneda de la cuenta (valores válidos `USD` - `$`).
description | Un nombre para la cuenta.