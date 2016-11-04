# Errors

<aside class="notice">Todos los endpoints manejan los errores de la misma manera</aside>

La API utiliza los siguientes códigos:

Error Code | Descripción
---------- | -------
400 | Bad Request -- Algúno de los parametros es incorrecto
401 | Unauthorized -- El access_token es inválido
404 | Not Found -- La URL que solicitaste no existe
500 | Internal Server Error -- Upps, tuvimos un error (también tenemos bugs).
503 | Service Unavailable -- Nuestro servidor esta temporalmente fuera de servicio.