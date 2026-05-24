# Guía rápida de HDPlayer
Para que puedas aprender rápidamente las funciones principales que vas a necesitar, como detectar el dispositivo en línea.

## 1. Instalar HDPlayer

![](images/HDPlayer_128x128_32bit.png)

HDPlayer es el software propietario para Windows para gestionar y controlar la pantalla en red local. Lo usaremos inicialmente para hacer ajustes y detectar la pantalla.

Puedes descargar la **última versión de HDPlayer** y el **manual completo** [aquí](https://www.hdwell.com/Download/)

![](images/Pasted%20image%2020260519124432.png)
*Software HDPlayer*

## 2. Detectar pantalla y encontrar IP e ID de dispositivo con HDPlayer

Vaya a la lista de dispositivos LED haciendo clic en la esquina inferior:

![](images/Pasted%20image%2020260519205359.png)

En esta ventana podrá ver las direcciones IP de todas las pantallas en red local y sus respectivos ID de dispositivo.

NOTA: El SDK solo funcionará con las pantallas habilitadas para SDK. Estas pantallas contienen una D en el nombre: C16L-Dxx-xxxxx.

## 3. Asignar una IP fija

En HDPlayer vaya a Control > Información de dispositivo y en la nueva ventana selecciona la pestaña “Configuración de red”.

![](images/Pasted%20image%2020260519130044.png)

Desmarcar “Adquisición automática” y rellenar los campos con los valores de red deseados. Pulsa en "determinar" para enviar la configuración.

## 4. Creación de programas en HDPlayer
>Esta sección es meramente informativa. La API permite realizar las funciones básicas de HDPlayer.

En HDplayer entendemos la creación de un diseño de pantalla como una lista de programas. Cada programa contiene áreas con distintas funciones, área de texto, área de imagen, área de vídeo, y otras áreas con funciones especiales como fecha y hora. Cada área tiene una posición y tamaño configurable.


![](images/Pasted%20image%2020260519124514.png)
*Algunas de las distintas áreas disponibles*

La elaboración de una pantalla tiene una estructura anidada, donde primero tenemos una pantalla, por debajo tenemos la lista de programas, y cada programa tiene sus elementos o áreas.

![](images/Pasted%20image%2020260519124731.png)
*Estructura de una pantalla con 4 programas*

Una vez creados nuestros programas, estos se reproducirán en bucle.


![](images/Pasted%20image%2020260519124834.png)

Para transmitir el diseño que acabamos de crear, nos conectaremos a la pantalla. Esta debe estar conectada en la misma red local, o bien realizar una conexión directa por Wifi o con cable ethernet a nuestro ordenador. Veremos en la esquina inferior el nombre de nuestra pantalla en color azul en cuanto el programa detecte la pantalla. Si el texto aparece en rojo no hay conexión.

![](images/Pasted%20image%2020260519125002.png)
*Pantalla detectada*

Hacemos clic en Transmitir. La información se quedará guardada en la memoria interna de la pantalla.

![](images/Pasted%20image%2020260519125020.png)

---