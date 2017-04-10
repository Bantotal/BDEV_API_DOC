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

Bienvenido a ¡**Bantotal Developers**! Aquí encontrarás toda la documentación necesaria para utilizar nuestra API y conectarte a Bantotal. De esta forma tu producto podrá ser utilizado por más de 70 bancos en 15 países.

Te brindamos recursos tales como: autenticación, clientes, cuentas, pagos, movimientos, entre otros. 

Identifica los que necesitas y comienza ahora 😃

# Autenticación

La autenticación es la llave 🔑 que te permitirá trabajar con nuestra API. 

Todos los recursos de nuestra plataforma son privados. Esto quiere decir que por ejemplo, si deseas que tu aplicación acceda a los datos de un cliente, el cliente deberá darle permisos.
Para autenticar utilizamos el conocido protocolo [oAuth 2.0](https://oauth.net/2/).

El protocolo se divide en **dos grandes partes**:

1. Obtener un *access_token* para un cliente (mediante la aprobación del cliente).
2. Accesso a los recursos mediante el *access_token*.

Antes de comenzar a detallar cada una de las partes, es **importante** mencionar que una vez que tu aplicación este registrada en B[Developers], tendra asignado un *client_id* y un *client_secret*.

`client_id: 123456789`

`client_secret: shhhhhhhh`

<aside class="notice">
Para registrar tu aplicación ingresá en [Bantotal Developers Admin](https://developers.bantotal.com/admin) 
</aside>

## Obtener el access token

El *access_token* o token de cliente es el tipo de token más común. Se necesita cada vez que la aplicación solicita a la API, que lea o modifique datos del Banco en nombre del cliente. Los *access_token* se obtienen mediante un cuadro de diálogo de inicio de sesión y requieren que el cliente conceda permiso 👍 a la aplicación.

Aunque cada plataforma genera *access_token* mediante API's distintas, todas siguen una estrategia básica para obtenerlo:

1. La aplicación solicita al cliente que se autentique en el banco, y le de permisos de acceso a sus datos.
2. El cliente se autentica y le otorga los permisos a la aplicación.
3. La aplicación recibe el código de autorización.
3. La aplicación intercambia el código de autorización por el *access_token*. 

### Paso 1

En este paso, la aplicación que desarrolles, deberá **redireccionar** al login del Banco para que los usuarios puedan autenticarse y posteriormente autorizar tu aplicación. Simplemente debes efectuar un redireccionamiento al URL:

> Remplaza *123456789* por tu client_id, y *https://www.youawesomeapp.com* por la url de tu aplicación

`https://developers.bantotal.com/auth/dialog/authorize?client_id=123456789&response_type=code&redirect_uri=https://www.youawesomeapp.com/`

### Parametros

Parametro | Descripción
--------- | -----------
client_id | Es el client_id de tu aplicación.
response_type | code – Indica que la operación deseada es obtener un código de autenticación que le permitirá a tu aplicación interactuar con el Banco.
redirect_uri | URL – Es el URL de tu aplicación.

Una vez que redirecciones al cliente al URL, ya puedes dejar de preocuparte, nosotros nos encargamos de la autenticación 😉

### Paso 2

Una vez que el cliente complete el proceso de autenticación en la página del Banco, será redireccionado a la página de autorización de tu aplicación. Allí se le presentarán todos los permisos solicitados junto a la descripción de tu aplicación.

De esto tampoco debes preocuparte 😊

### Paso 3

Otorgados los permisos, el cliente será redireccionado al Redirect URI (el que nos pasaste en el URL anterior) con el código de autorización, de la siguiente forma:

`https://www.youawesomeapp.com/?code=SERVER_GENERATED_AUTHORIZATION_CODE`

### Paso 4

> Aquí un ejemplo:

```shell
curl -X POST 
  -H "Content-Type: application/x-www-form-urlencoded" 
  -H "Authorization: Basic YWJjMTIzOnNzaC1zZWNyZXQ=" 
  -d 'client=blablabla&code=SERVER_GENERATED_AUTHORIZATION_CODE&redi
  rect_uri=https://www.youawesomeapp.com/&grant_type=authorization_code'
  "https://developers.bantotal.com/auth/oauth/token"
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

`https://developers.bantotal.com/auth/oauth/token`

Como verás en el ejemplo de la derecha, uno de los parametros pasados en el Header es Authorization. **En este comando esperamos que nos pases tu client_id y tu client_secret en Base64**. De esta manera: client_id:client_secret. 

[Aquí](https://es.wikipedia.org/wiki/Autenticaci%C3%B3n_de_acceso_b%C3%A1sica) 👈 puedes ver como generar el string de HTTP Basic authentication.

Parametro | Descripción
--------- | -----------
client | Es el client_id de tu aplicación.
code | El código de autorización obtenido en el paso anterior.
redirect_uri | URL – Es el URL de tu aplicación.
grant_type | authorization_code – Indica que la operación deseada es intercambiar el “code” por un access_token.

¡Listo! Como verás en la respuesta obtendrás el *access_token* para realizar llamadas a nuestra API, y así obtener acceso a los datos privados del cliente 🚆

## Acceso a los recursos

```shell
# El access_token obtenido en el paso anterior, 
se pasa en Authorization: Bearer access_token
curl -X GET 
  -H "Authorization: Bearer hUEM8g7EK4u8hxFYpIw1YPlkjjrY" 
  "https://developers.bantotal.com/api/v1/client"
```
> El GET te retornará la siguiente estructura JSON:

```json
{
  "uid": "FGH546H34KLO98OP",
  "firstName": "Camila",
  "secondName": "Lucia",
  "surname": "Irachet",
  "secondSurname": "Bonifacino",
  "documentNumber": "123456789",
  "documentType": "C.I.",
  "documentCountry": "Uruguay",
  "address": "18 de Julio 2523, Montevideo, Uruguay",
  "email": "cboni@mail.com",
  "phone": "123456789"
}
```

Ahora que ya tienes el `access_token`, podrás acceder a los recursos.

Veamos un ejemplo.

<aside class="notice">
Para el ejemplo utilizamos la consulta de cliente, pero fue solamente un ejemplo. A partir de ahora, cada recurso que quieras consumir, será de la misma manera, enviandole el access_token del cliente en el Header.
</aside>

# Clientes

En esta sección, encontrarás todos los recursos asociados a los datos de los clientes. Por ejemplo, sus datos personales registrados en el banco.

## Obtener datos

```shell
curl -X GET 
  -H "Authorization: Bearer hUEM8g7EK4u8hxFYpIw1YPlkjjrY" 
  "https://developers.bantotal.com/api/v1/client"
```

> El GET te retornará la siguiente estructura JSON:

```json
{
  "uid": "FGH546H34KLO98OP",
  "firstName": "Camila",
  "secondName": "Lucia",
  "surname": "Irachet",
  "secondSurname": "Bonifacino",
  "documentNumber": "123456789",
  "documentType": "C.I.",
  "documentCountry": "Uruguay",
  "address": "18 de Julio 2523, Montevideo, Uruguay",
  "email": "cboni@mail.com",
  "phone": "123456789"
}
```

Este endpoint retorna los datos del cliente autenticado.

### HTTP Request

`GET https://developers.bantotal.com/api/v1/client`

# Cuentas

En esta sección, encontrarás todos los recursos asociados a las cuentas de los clientes. Por ejemplo, consulta de sus cuentas, alta de cuentas, consulta movimientos, etc.

## Obtener cuentas

```shell
curl -X GET 
  -H "Authorization: Bearer hUEM8g7EK4u8hxFYpIw1YPlkjjrY" 
  "https://developers.bantotal.com/api/v1/accounts"
```

> El GET te retornará la siguiente estructura JSON:

```json 
[
  {
   "uid": "1",
   "productType": "CC",
   "currency": "USD",
   "balance": "1200",
   "description": "Cuenta corriente 1",
  },
  {
   "uid": "2",
   "productType": "CC",
   "currency": "UYU",
   "balance": "25800",
   "description": "Cuenta corriente 2",
  },
  {
   "uid": "3",
   "productType": "CA",
   "currency": "USD",
   "balance": "100",
   "description": "Caja de ahorro 1",
  },
  {
   "uid": "4",
   "productType": "CA",
   "currency": "UYU",
   "balance": "78500",
   "description": "Caja de ahorro 2",
  }
]
```

Este endpoint retorna las cuentas del cliente autenticado.

### HTTP Request

`GET https://developers.bantotal.com/api/v1/accounts`

## Obtener una cuenta

```shell
curl -X GET 
  -H "Authorization: Bearer hUEM8g7EK4u8hxFYpIw1YPlkjjrY" 
  "https://developers.bantotal.com/api/v1/accounts/<UID>"
```

> El GET te retornará la siguiente estructura JSON:

```json 
{
  "uid": "1",
  "productType": "CC",
  "currency": "USD",
  "balance": "1200",
  "description": "Cuenta corriente 1",
}
```

Este endpoint retorna la información de una única cuenta, la cual es filtrada mediante la URL.

### HTTP Request

`GET https://developers.bantotal.com/api/v1/accounts/<UID>`

Parametro | Descripción
--------- | -----------
UID | Identificador de la cuenta.

## Obtener movimientos de una cuenta

```shell
curl -X GET 
  -H "Authorization: Bearer hUEM8g7EK4u8hxFYpIw1YPlkjjrY" 
  "https://developers.bantotal.com/api/v1/accounts/<UID>/movements"
```

> El GET te retornará la siguiente estructura JSON:

```json 
[
  {
    "uid": "AA45GH5TM789DS34",
    transactionId: "ASD56F54G890KKL",
    "signature": "a4e29a77cc84c393da1da7d06beb0ec0d12ae58314e26b137f913f4f85e25e2f",
    "type": "Debit",
    "date": "2016-11-18T17:59:11.858Z",
    "valueDate": "2016-11-18T17:59:11.858Z",
    "balance": "14060"
    "currency": "USD",
    "ammount": "500",
    "reference": "Impuestos",
    "reason": "Pago de sueldo",
    "chanel": "Cajero automático"
  }
]
```

Este endpoint retorna los movimientos de una única cuenta, la cual es filtrada mediante la URL.

### HTTP Request

`GET https://developers.bantotal.com/api/v1/accounts/<UID>/movements`

Parametro | Descripción
--------- | -----------
ID | Identificador de la cuenta.

## Crear cuenta de ahorro

```shell
curl -X POST 
  -H "Authorization: Bearer hUEM8g7EK4u8hxFYpIw1YPlkjjrY"  
  -H "Content-Type: application/x-www-form-urlencoded" 
  -d 'currency=USD&description=Chanchita' 
  "https://www.api.btdevelopers.com/v0/savingAccount"
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

`POST https://www.api.btdevelopers.com/v0/savingAccount`

Parametro | Descripción
--------- | -----------
currency | Moneda de la cuenta (valores válidos `USD` - `$`).
description | Un nombre para la cuenta.

# Transferencias

En esta sección, encontrarás todos los recursos asociados a las transferencias entre cuentas. Por ejemplo, entre cuentas, a terceros, internacionales, etc.

## Entre cuentas

```shell
curl -X POST 
  -H "Authorization: Bearer hUEM8g7EK4u8hxFYpIw1YPlkjjrY"  
  -H "Content-Type: application/x-www-form-urlencoded" 
  -d 'debitAccount=581cfacc24ad4706583ae7bb&creditAccount=581cfacc24ad4706583ae6aa&currency=USD&ammount=200&reference=Almuerzo' 
  "https://www.api.btdevelopers.com/v0/transferMyAccounts"
```

> El POST te retornará la siguiente estructura JSON:

```json 
{
  "signature": "34652e1d575bb6c5daf3884e70baf13839a62b4c1a218fd14f487c073bcb7d82",
  "date": "2016-11-18T17:59:25.823Z",
  "reference": "Almuerzo",
  "ammount": 200,
  "currency": "USD",
  "creditAccountBalance": 21700,
  "creditAccount": "581cfacc24ad4706583ae6aa",
  "debitAccountBalance": 21300,
  "debitAccount": "581cfacc24ad4706583ae7bb",
  "_id": "582f417d2a8a4810301dbf0c"
}
```

Este endpoint da de alta una cuenta de ahorro al cliente.

### HTTP Request

`POST https://www.api.btdevelopers.com/v0/transferMyAccounts`

Parametro | Descripción
--------- | -----------
debitAccount | Identificador de cuenta débito
creditAccount | Identificador de cuenta cédito
currency | Moneda de la cuenta (valores válidos `USD` - `$`)
ammount | Importe de la transferencia
reference | Texto de referencia 