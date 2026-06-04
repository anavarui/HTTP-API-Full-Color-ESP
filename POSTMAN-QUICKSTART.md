# Guía rápida de Postman
Solo te llevará unos minutos y ya podrás empezar a testear la API. Vamos paso a paso.

## 1. Instalar Postman

Descarge Postman de su página web oficial [postman.com](https://www.postman.com/downloads/) e instale el software.

Inicie sesión para poder importar la colección.

![](images/Pasted%20image%2020260519205100.png)

## 2. Importar la collección

Pulsa en `Import`.

![](images/Pasted%20image%2020260519210042.png)

Arrastra el archivo .json dentro.

![](images/Pasted%20image%2020260519210050.png)

## 3. Configurar variables de entorno

Una vez importada la colección, ahora vamos a `Create Enviroment` y pon un nombre al nuevo ambiente.

![](images/Pasted%20image%2020260519210058.png)

Creamos las siguientes variables:

`ip` - la IP del dispositivo (puedes verla con HDPlayer)

`deviceid` - la ID del dispositivo (puedes verla con HDPlayer)

`sdkKey` - clave pública, ofrecida durante el registro

`sdkSecret` - clave privada, ofrecida durante el registro

![](images/Pasted%20image%2020260519210109.png)

Ya podemos probar los ejemplos. Abrimos cualquiera, por ejemplo "Get device status" pulsamos SEND.

![](images/Pasted%20image%2020260519210121.png)

>NOTA: Los ejemplos de programas están diseñados para una pantalla de 128 x 64 px. Hay que modificar manualmente los valores `width` y `height` que encontrarás en el cuerpo de la petición.

---

[<< Volver al README](README.md)