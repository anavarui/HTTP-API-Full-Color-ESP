Documentación HTTP API para FULL COLOR SDK
---

> 💡 NOVEDAD: ***API gateway*** ya disponible para instalar en su servidor!


# Contenido
1. [Introducción](#1-introducción)
2. [Cómo empezar a testear la API](#2-cómo-empezar-a-testear-la-api)
3. [Descripción de la API](#3-descripción-de-la-api)
4. [Interfaz de dispositivo](#4-interfaz-de-dispositivo-apidevice)
    - 4.1. [Métodos de dispositivo](#41-métodos-de-dispositivo)
	- 4.2  [Lista de dispositivos en línea](#42-lista-de-dispositivos-en-línea-apidevicelist)
5. [Interfaz de programa](#5-interfaz-de-programa-apiprogram)
    - 5.1. [Estructura de los programas (JSON)](#51-estructura-de-los-programas-json)
    - 5.2. [Métodos de programa](#52-métodos-de-programa)
    - 5.3. [Control de reproducción de programa](#53-control-de-reproducción-de-programa)
    - 5.4. [Ítem de texto](#54-ítem-de-texto)
    - 5.5. [Ítem de imagen](#55-ítem-de-imagen)
    - 5.6. [Ítem de video](#56-ítem-de-video)
    - 5.7. [Ítem de reloj digital](#57-ítem-de-reloj-digital)
    - 5.8. [Item de reloj analógico](#58-ítem-de-reloj-analógico)
    - 5.9. [Ítem de área dinámica](#59-ítem-de-área-dinámica)
6. [Interfaz de archivo](#6-interfaz-de-archivo-apifile)
    - 6.1. [Cómo usar archivos subidos con la API](#61-cómo-usar-archivos-subidos-con-la-api)
    - 6.2. [Alternativa: subir archivos con RemoteServer.exe](#62-alternativa-subir-archivos-con-remoteserverexe)
7. [Interfaz de captura de pantalla](#7-interfaz-de-captura-de-pantalla-apiscreenshot)
8. [Apéndices](#8-apéndices)
    - 8.1. [Apéndice 1: Efectos](#81-apéndice-1-efectos)
    - 8.2. [Apéndice 2: Bordes](#82-apéndice-2-bordes)
    - 8.3. [Apéndice 3: Lista de fuentes disponibles](#83-apéndice-3-lista-de-fuentes-disponibles)
    - 8.4. [Apéndice 4: Lista de códigos de error SDK](#84-apéndice-4-lista-de-códigos-de-error-sdk)

Anexo 1. [Guía rápida de Postman](POSTMAN-QUICKSTART.md)

Anexo 2. [Guía rápida de HDPlayer](HDPLAYER-QUICKSTART.md)


# 1. Introducción

## 1.1 Resumen

Este manual detalla cómo usar la API para el desarrollo de integraciones informáticas con nuestras pantallas Full Color SDK.

Las pantallas Full Color SDK tienen habilitada tanto 1. la comunicación por protocolo SDK de código abierto (no especificado en este manual) como 2. por protocolo HTTP mediante la API que se detalla aquí.

Tenga en cuenta que únicamente los dispositivos con una 'D' en medio de la ID del dispositivo tienen el hardware necesario para el SDK. Por ej. C16L-Dxx-xxxxx.

Este manual explica en detalle las interfaces del sistema y se incluyen ejemplos de uso para Postman, con los que el personal técnico podrá entenderlas rápidamente y empezar a desarrollar.

Los lectores deben tener conocimientos básicos de:
1. Comprensión de los protocolos HTTPS/HTTP, etc.
2. Conceptos básicos de seguridad de la información.
3. Al menos un lenguaje de programación.

> NOTA: Es muy recomendable familiarizarse antes con las funciones disponibles en la pantalla usando el software propietario HDPlayer, explicado en el anexo [Guía rápida de HDPlayer](HDPLAYER-QUICKSTART.md).

## 1.2 Comunicación con los dispositivos

La comunicación se realiza mediante solicitudes HTTP al puerto 30080 del dispositivo. El cuerpo de la petición y la respuesta están codificados en JSON.

**Acceso directo a un dispositivo**

![](images/Esquema%201.png)


**Acceso indirecto mediante el API gateway (linux/windows/mac) ligado a varios dispositivos**
![](images/Esquema%202.png)
(Ya disponible. Contacte con nosotros)

## 1.3 Registrarse en la plataforma para obtener las claves sdkKey y sdkSecret

La plataforma de registro no está abierta para el público aún. Los clientes que deseen desarrollar con la API deben registrarse con nosotros para que les facilitemos la clave pública y privada, llamadas `sdkKey` y `sdkSecret` respectivamente. 

Los datos para el registro son:
- Un correo electrónico
- Nombre del desarrollador
- Nombre de la empresa

Esto debe hacerse idealmente antes o durante el proceso de pedido de la pantalla, y quedan inicializadas en el propio dispositivo durante la fabricación. Si ha adquirido una pantalla SDK pero no ha sido inicializada en su momento para la comunicación por API, contáctenos para ayudarle a activar la API.

Estas claves son necesarias para el proceso de firma de la petición HTTP.

# 2. Cómo empezar a testear la API

1. Conecte la pantalla a su red local.
2. Sigue los pasos de la [Guía rápida de Postman](POSTMAN-QUICKSTART.md) para importar la colección de ejemplos
3. Sigue los pasos de [Guía rápida de HDPlayer](HDPLAYER-QUICKSTART.md) para detectar la ID e IP del dispositivo
4. Introduce los valores de `sdkKey`, `sdkSecret`, `ip` y `deviceid` en Postman como dice la guía rápida
5. Prueba un ejemplo como "Get device information" para probar que funciona todo correctamente.

---

# 3. Descripción de la API

## 3.1 Vista general
La API opera sobre HTTP en el puerto 30080. Las peticiones deben estar codificadas en JSON, e incluyen un método (`method`) y sus parámetros (`data`) en el cuerpo de la solicitud (el parámetro `id` es opcional). La respuesta también se devuelve en formato JSON.

***Ejemplo de una llamada a la API para ajustar el brillo al 50%:***

Endpoint: `POST 192.168.1.13:30080/api/device/C16L-D24-00622`

Cuerpo (JSON):
```json
{
    "method": "setDeviceProperty",
    "data": {
        "luminance": "50"
    }
}
```
Respuesta:
```json
{
    "method": "setDeviceProperty",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-00622",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

## 3.2 Formas de especificar la ID
>En caso de una conexión directa con un dispositivo no es obligatorio especificar la ID, excepto en algunos casos (véase "Método `getAll`").

Para especificar el dispositivo que queremos controlar se pueden usar las siguientes formas:
1. No especificar ID - /api/device/ Indica que operamos en el dispositivo local
2. En la URL forma 1 - /api/device/C16L-D24-A0001,C16L-D24-A0002
3. En la URL forma 2 - /api/device/?id=C16L-D24-A0001,C16L-D24-A0002
4. En el JSON :
```json
{
	"id": "C16L-D24-A0001,C16L-D24-A0002",
    "method": "setDeviceProperty",
    "data": {
        "luminance": "50"
    }
}
```

## 3.3 Mecanismo de firma

El dispositivo viene de fábrica con las claves `sdkKey` y `sdkSecret` inicializadas. Si no fuera así, contáctenos.

Todas las llamadas a la interfaz de la API utilizan `sdkKey` y `sdkSecret` (la clave secreta no se transmite) para firmar, garantizando la integridad y legitimidad de los datos. 

Hay dos reglas de firma:

### 3.3.1 Regla 1 (General):

`sign = HMACMD5(body + sdkKey + date, sdkSecret)`


Donde:  
	`sign`: La firma calculada, agregado al encabezado de la solicitud HTTP  
	`HMACMD5`: Función criptográfica HMAC-MD5  
    `body`: Todo el contenido del cuerpo de la solicitud HTTP  
    `date`: Hora y fecha actual del cliente, campo del encabezado HTTP  
    `sdkKey`: ofrecido durante el registro, campo del encabezado HTTP  
    `sdkSecret`: ofrecido durante el registro (clave secreta, no se transmite)
    

### 3.3.2 Regla 2 (Usada solo para la interfaz de archivos):
Para la interfaz de archivos:

`sign = HMACMD5(sdkKey + date, sdkSecret)`


### 3.3.3 Ejemplo de un encabezado firmado

```
requestId: da7ddf89-c102-4fb4-95e7-a8f7a72e697e
sdkKey: xxxxxxxxxxxxxxxxxxxxx
date: Wed, 09 Aug 2023 07:27:44 GMT
sign: 371b45207ecc8ea993a1468caf7d8bec
Content-Type: application/json
Accept: */*
Host: sdk.huidu.cn
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Content-Length: 72
```

## 3.4 Interfaces de la API
Hay principalmente 4 interfaces:
 
| Interfaz         | Descripción                                   | 
| ---------------- | --------------------------------------------- |
| /api/device/     | Información y configuración del dispositivo   |
| /api/program/    | Control de los programas a visualizar         |
| /api/file/       | Transferencia de archivos                     |
| /api/screenshot/ | Capturas de pantalla                          |




---


# 4. Interfaz de dispositivo /api/device/

La interfaz de dispositivo se utiliza principalmente para operar propio dispositivo (ajustar brillo, apagar/encender...) como para obtener información del estado del dispositivo.

## 4.1 Métodos de dispositivo

### 4.1.1 getDeviceProperty
***Obtener las propiedades del dispositivo***

>Con este método puedes leer las propiedades como el brillo, la hora interna, la versión de firmware, etc.

**Endpoint:** `POST 127.0.0.1:30080/api/device/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**

```json
{
    "method": "getDeviceProperty",
    "data": []
}
```

**Respuesta — ejemplo:**

```json
{
    "method": "getDeviceProperty",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-00622",
            "message": "ok",
            "data": {
                "name": "BoxPlayer",
                "sync": "false",
                "screen.width": "128",
                "screen.height": "64",
                "screen.rotation": "0",
                "version.hardware": "HD-C16L-V1.X",
                "version.fpga": "16.12.0.0",
                "version.app": "7.11.18.0",
                "time": "2026-04-14 20:18:14",
                "time.timeZone": "Europe/Amsterdam;UTC+01:00;Amsterdam ,Berlin , Bern, Rome ,Stockholm , Vienna",
                "time.sync": "none",
                "time.ntp": "ntp.huidu.cn",
                "volume": "80",
                "volume.mode": "default",
                "luminance": "5",
                "luminance.mode": "default",
                "eth.dhcp": "true",
                "eth.ip": "192.168.1.93",
                "wifi.enabled": "true",
                "wifi.mode": "ap",
                "wifi.ap.ssid": "C16L-D24-00622",
                "wifi.ap.passwd": "88888888",
                "wifi.ap.channel": "5",
                "raw": /*XML EN BRUTO, OMITIDO POR EXCESO DE LONGITUD*/
            }
        }
    ]
}
```

**Atributos del dispositivo**

| Atributo           | Descripción                                                                                                   | Valores                                                                                                                                                                                        | Editable                                                           |
| ------------------ | ------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| `name`             | Nombre del dispositivo.<br>Se puede cambiar para identificar mejor el dispositivo                             | String<br>"BoxPlayer" por defecto                                                                                                                                                              | Sí                                                                 |
| `sync`             | Sincronización multipantalla                                                                                  | String<br>"false" por def.                                                                                                                                                                     | Editable, pero consulte el manual de HDPlayer                      |
| `screen.width`     | Ancho de la pantalla                                                                                          | String                                                                                                                                                                                         | Sí. No modificar                                                   |
| `screen.height`    | Alto de la pantalla                                                                                           | String                                                                                                                                                                                         | Sí. No modificar                                                   |
| `screen.rotation`  | Rotación de la pantalla                                                                                       | 0, 90, 180, 270                                                                                                                                                                                | No editable<br> Usar HDPlayer para cambiarlo                       |
| `version.hardware` | Versión del hardware                                                                                          |                                                                                                                                                                                                | No                                                                 |
| `version.fpga`     | Versión del FPGA                                                                                              |                                                                                                                                                                                                | No                                                                 |
| `version.app`      | Versión de firmware                                                                                           |                                                                                                                                                                                                | No                                                                 |
| `time`             | Hora/fecha del dispositivo                                                                                    |                                                                                                                                                                                                | Sí                                                                 |
| `time.timeZone`    | Zona horaria                                                                                                  |                                                                                                                                                                                                | Sí, pero recomendamos cambiarla desde HDPlayer                     |
| `time.sync`        | Modo de sincronización de la hora                                                                             | “none” - desactivado<br>“ntp” - servidor ntp                                                                                                                                                   | Sí                                                                 |
| `time.ntp`         | URL del servidor ntp                                                                                          | String                                                                                                                                                                                         | Sí                                                                 |
| `volume`           | Volumen de sonido. Nuestras pantallas no disponen de salida de señal de audio, excepto a petición del cliente | String<br>"0" a "100"                                                                                                                                                                          | Editable, pero no modifica las franjas horarias en el modo “ploys” |
| `volume.mode`      | Modo de volumen                                                                                               | “default” - volumen fijo<br>“ploys” - volumen variable por franjas horarias                                                                                                                    | Sí                                                                 |
| `luminance`        | Brillo de la pantalla                                                                                         | String<br>"1" a "100"                                                                                                                                                                          | Editable, pero no modifica las franjas horarias en el modo “ploys” |
| `luminance.mode`   | Modo de brillo                                                                                                | “default” - brillo fijo<br>“ploys” - brillo variable por franjas horarias<br>“sensor” - brillo automático. Nuestras pantallas no disponen de sensor de luz a no ser que lo solicite el cliente | Sí                                                                 |
| `eth.dhcp`         | DHCP                                                                                                          |                                                                                                                                                                                                | No                                                                 |
| `eth.ip`           | Dirección IP                                                                                                  |                                                                                                                                                                                                | No                                                                 |
| `wifi.enabled`     | Indica si el Wi-Fi está habilitado                                                                            |                                                                                                                                                                                                | No                                                                 |
| `wifi.mode`        | Modo Wi-Fi                                                                                                    | "ap" o "station"                                                                                                                                                                               | No                                                                 |
| `wifi.ap.ssid`     | Modo ap: SSID del punto de acceso                                                                             |                                                                                                                                                                                                | No                                                                 |
| `wifi.ap.passwd`   | Modo ap: Contraseña del punto de acceso                                                                       |                                                                                                                                                                                                | No                                                                 |
| `wifi.ap.channel`  | Modo ap: Canal del punto de acceso                                                                            |                                                                                                                                                                                                | No                                                                 |

---
### 4.1.2 SetDeviceProperty
***Cambiar atributos del dispositivo***

>Algunas propiedades son de solo lectura y no se pueden cambiar. Consultar la tabla de más arriba.

**Endpoint:** `POST 127.0.0.1:30080/api/device/{{deviceid}}`  
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**

```json
{
    "method": "setDeviceProperty",
    "data": {
        "name": "BoxPlayer 2",
        "time": "2026-04-11 14:21:00",
        "luminance": "70"
    }
}
```

**Respuesta — ejemplo:**

```json
{
    "method": "setDeviceProperty",
    "message": "ok",
    "data": [
        {
            "id": "C16-D00-A000F",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

---
### 4.1.3 getDeviceStatus
***Obtener el estado del dispositivo***

>Principalmente para saber si la pantalla está encendida o en stand-by, además de algunas propiedades como la IP, etc.

**Endpoint:** `POST 127.0.0.1:30080/api/device/{{deviceid}}`  
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**

```json
{
    "method": "getDeviceStatus",
    "data": []
}
```

**Respuesta — ejemplo:**

```json
{
    "method": "getDeviceStatus",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-00622",
            "message": "ok",
            "data": {
                "screen.openStatus": "true",
                "eth.valid": "true",
                "eth.dhcp": "true",
                "eth.ip": "192.168.1.93",
                "gsm.valid": "false",
                "wifi.valid": "true",
                "wifi.enabled": "true",
                "wifi.mode": "ap"
            }
        }
    ]
}
```


|Atributo|Descripción|
| --- | --- |
|`screen.openStatus`|pantalla encendida (”true”) o en standby con la pantalla apagada (”false”)|
|`eth.valid`|el cable ethernet está conectado|
|`eth.dhcp`|dhcp habilitado/deshabilitado|
|`eth.ip`|dirección ip|
|`gsm.valid`| módulo 4g está presente    |
|`wifi.valid `|modulo wifi está presente|
|`wifi.enabled`|wifi habilitado/deshabilitado|
|`wifi.mode`|"ap"/"station"|

___
### 4.1.4 getScheduledTask
***Obtener el horario del dispositivo***

>Para leer los horarios de brillo, apagado/encendido, etc. que se han establecido previamente con `"setScheduledTask/updateScheduledTask"`.
>
>NOTA: con este método no es posible leer los horarios establecidos con HDPlayer.

**Endpoint:** `POST 127.0.0.1:30080/api/device/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Parámetros**

|Parámetro|Descripción|`"data"`|
| --- | --- | --- |
|`screen`|Horario para el encendido/apagado de la pantalla|"false" (stand-by)<br>"true" (encendido)|
|`volume`|Horario para el volumen<br>(normalmente nuestras pantallas no tienen salida de audio a menos que lo solicite el cliente)|"1" a "100"|
|`luminance`|Horarios para el brillo|"1" a "100"|
|`relay`|Horarios para el relé integrado (normalmente no usado)|"false" "true"|


**Request body — ejemplo:**

```json
{
	"method": "getScheduledTask",
	"data": [
		"screen",
		"volume",
		"luminance",
		"relay"
	]
}
```


**Respuesta — ejemplo:**

```json
{
    "message": "ok",
    "data": {
        "luminance": [
            {
                "timeRange": "08:00:00~18:00:00",
                "dateRange": "2023-10-01~2023-10-11",
                "WeekFilter": "Mon,Tue,Wed",
                "MonthFilter": "Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec",
                "data": "80"
            },
            {
                "timeRange": "18:00:00~08:00:00",
                "dateRange": "2023-10-01~2023-10-11",
                "WeekFilter": "Mon,Tue,Wed,Thu,Fri,Sat,Sun",
                "MonthFilter": "Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec",
                "data": "60"
            }
        ],
        "volume": [
            {
                "timeRange": "08:00:00~18:00:00",
                "dateRange": "2023-10-01~2023-10-11",
                "WeekFilter": "Mon,Tue,Wed",
                "MonthFilter": "Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec",
                "data": "80"
            }
        ],
        "screen": [
            {
                "timeRange": "00:00:00~06:00:00",
                "dateRange": "2023-10-01~2023-10-11",
                "MonthFilter": "Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec",
                "data": "false"
            },
            {
                "timeRange": "06:00:00~00:00:00",
                "dateRange": "2023-10-01~2023-10-11",
                "MonthFilter": "Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec",
                "data": "true"
            }
        ],
        "relay": [
            {
                "timeRange": "08:00:00~18:00:00",
                "dateRange": "2023-10-01~2023-10-11",
                "MonthFilter": "Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec",
                "data": "true"
            }
        ]
    }
}
```

---
### 4.1.5 setScheduledTask / updateScheduledTask
***Establecer/actualizar el horario del dispositivo***

>Puedes establecer con este método un horario de encendido/apagado o de cambio de brillo por horas.
>
>NOTA: En el caso del brillo y el volumen es posible que no produzca un cambio inmediato, pues solo entra en efecto en las horas de cambio que se han establecido. Con un cambio de horario de brillo se recomienda actualizar manualmente el brillo con `"SetDeviceProperty"`.

Hay dos métodos para cambiar los horarios:
- `"setScheduledTask"`: Reemplaza todos los elementos existentes.
- `"updateScheduledTask"`: Actualiza solo los elementos transmitidos.

|Parámetro|Descripción|
| --- | --- |
|`screen`|Franjas horarias para el encendido/apagado de la pantalla|
|`volume`|Franjas horarias para el volumen<br>(normalmente nuestras pantallas no tienen salida de audio a menos que lo solicite el cliente)|
|`luminance`|Franjas horarias para el brillo|
|`relay`|Franjas horarias para el relé integrado (normalmente no usado)|

**Endpoint:** `POST 127.0.0.1:30080/api/device/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**

```json
{
    "method": "setScheduledTask",
    "data": {
        "luminance": [
            {
                "timeRange": "08:00:00~18:00:00",
                "dateRange": "2023-10-01~2023-10-11",
                "WeekFilter": "Mon,Tue,Wed",
                "MonthFilter": "Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec",
                "data": "80"
            },
            {
                "timeRange": "18:00:00~08:00:00",
                "dateRange": "2023-10-01~2023-10-11",
                "WeekFilter": "Mon,Tue,Wed,Thu,Fri,Sat,Sun",
                "MonthFilter": "Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec",
                "data": "60"
            }
        ],
        "volume": [
            {
                "timeRange": "08:00:00~18:00:00",
                "dateRange": "2023-10-01~2023-10-11",
                "WeekFilter": "Mon,Tue,Wed",
                "MonthFilter": "Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec",
                "data": "80"
            }
        ],
        "screen": [
            {
                "timeRange": "00:00:00~06:00:00",
                "dateRange": "2023-10-01~2023-10-11",
                "MonthFilter": "Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec",
                "data": "false"
            },
            {
                "timeRange": "06:00:00~00:00:00",
                "dateRange": "2023-10-01~2023-10-11",
                "MonthFilter": "Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec",
                "data": "true"
            }
        ],
        "relay": [
            {
                "timeRange": "08:00:00~18:00:00",
                "dateRange": "2023-10-01~2023-10-11",
                "MonthFilter": "Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec",
                "data": "true"
            }
        ]
    }
}
```

**Respuesta — ejemplo:**

```json
{
    "method": "setScheduledTask",
    "message": "ok",
    "data": [
        {
            "id": "C16-D00-A000F",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

### 4.1.6 pushStatus
***Push proactivo (para área dinámica)***

>Establece el valor de las variables que hemos usado con un programa de área dinámica (ver sección y ejemplo de un área dinámica, en los métodos de la interfaz de programa).
>
>💡 Cómo probar este ejemplo:
> 1. En los ejemplos de Postman abre "Dynamic area" y envíalo
> 2. Abre este ejemplo "Proactive push" y envíalo

**Endpoint:** `POST 127.0.0.1:30080/api/device/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**

```json
{
    "method": "pushStatus",
    "data": {
        "ParkingSpace": "120",
        "Temperature": "16"
    }
}
```

**Respuesta — ejemplo:**

```json
{
    "method": "pushStatus",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-007C6",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

### 4.1.7 setPeriodicTask
***Establecer tarea periódica de actualización por URL (para áreas dinámicas)***

>Permite configurar una tarea periódica para actualizar las variables de una área dinámica a partir de una URL (ver sección "área dinámica").
>
>💡 Cómo probar este ejemplo:
> 1. En los ejemplos de Postman subir "Dynamic area"
> 2. Hacer un servidor web que devuelva el texto plano `"ParkingSpace,110 Temperature,25"`
> 3. Abre el ejemplo en Postman "Set periodic task" y cambia las URLs a tu servicio web.

**Endpoint:** `POST 127.0.0.1:30080/api/device/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Parámetros del cuerpo de la solicitud:**

|**Parámetro**|**Valor de ejemplo**|**Tipo**|**Obligatorio**|**Descripción**|
|---|---|---|---|---|
|`url`|[http://ejemplo.com/datos.txt](http://ejemplo.com/datos.txt)|String|Sí|URL de la fuente de datos<br>Los datos deben estar en texto plano|
|`rege`|(Clave),([0-9]+)|String|No|Expresión regular con exactamente dos grupos de captura: primero la clave, segundo el valor. Para múltiples variables, cree tareas separadas.|
|`interval`|10|Int [3–3600 segundos]|No|Intervalo de sondeo en segundos (predeterminado: 30)|

**Ejemplo para múltiples variables**

Pongamos que la URL  `"http://192.168.90.245/1.html"` devuelve el siguiente texto plano:

```
ParkingSpace,110
Temperature,25
```

**Solicitud de ejemplo:**
```json
{
    "method": "setPeriodicTask",
    "data": [
        {
            "url": "http://192.168.90.245/1.html",
            "rege": "(ParkingSpace),([0-9]+)",
            "interval": "10"
        },
        {
            "url": "http://192.168.90.245/1.html",
            "rege": "(Temperature),([0-9]+)",
            "interval": "10"
        }
    ]
}
```

**Explicación:** Cada variable necesita su propia entrada. El dispositivo obtiene los datos una vez pero aplica cada expresión regular individualmente, actualizando tanto `{{ParkingSpace}}` como `{{Temperature}}` en el área dinámica.

El motor regex del dispositivo solo captura **el primer par clave-valor** que coincide dentro de un único patrón `rege`. Para múltiples variables desde la misma URL, **cree una entrada de tarea separada por cada variable**. El ejemplo muestra cómo.

**Respuesta — ejemplo:**
```json
{
    "method": "setPeriodicTask",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-007C6",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

### 4.1.8 getPeriodicTask
***Obtener la tarea periódica de actualización por URL (para áreas dinámicas)***

>Lee la tarea de actualización que hemos establecido en el apartado anterior, usado en áreas dinámicas (ver sección y ejemplo de un área dinámica, en los métodos de la interfaz de programa).

**Endpoint:** `POST 127.0.0.1:30080/api/device/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**

```json
{
    "method": "getPeriodicTask",
    "data": []
}
```

**Respuesta — ejemplo:**

```json
{
    "method": "getPeriodicTask",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-007C6",
            "message": "ok",
            "data": [
                {
                    "url": "http://192.168.90.245/1.html",
                    "rege": "(ParkingSpace),([0-9]+)",
                    "interval": 10
                },
                {
                    "url": "http://192.168.90.245/1.html",
                    "rege": "(Temperature),([0-9]+)",
                    "interval": 10
                }
            ]
        }
    ]
}
```



### 4.1.9 rebootDevice
***Reiniciar dispositivo***

>Reinicia el dispositivo tras unos segundos.

**Endpoint:** `POST 127.0.0.1:30080/api/device/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Parámetros:**

| Parámetro | Valor   | Obligatorio |
| --------- | ------ | ----------- |
| `delay`    | int (segundos) | No         |

**Request body — ejemplo:**

```json
{
    "method": "rebootDevice",
    "data": {
        "delay": 5
    }
}
```

**Respuesta — ejemplo:**

```json
{
    "method": "rebootDevice",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D00-A000F",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

### 4.1.10 openDeviceScreen
***Encender pantalla***

>Enciende la pantalla si ésta está apagada (stand-by).

**Endpoint:** `POST 127.0.0.1:30080/api/device/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**

```json
{
    "method": "openDeviceScreen",
    "data": {}
}
```

**Respuesta — ejemplo:**

```json
{
    "method": "openDeviceScreen",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D00-A000F",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

### 4.1.11 closeDeviceScreen
***Apagar pantalla***

>Pone la pantalla en estado stand-by con la pantalla en negro

**Endpoint:** `POST 127.0.0.1:30080/api/device/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**

```json
{
    "method": "closeDeviceScreen",
    "data": {}
}
```

**Respuesta — ejemplo:**

```json
{
    "method": "closeDeviceScreen",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D00-A000F",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

## 4.2 Lista de dispositivos en línea (/api/device/list/)

>Devuelve una lista de los dispositivos conectados en caso de operar un server. En caso de estar operando directamente con un dispositivo la lista tendrá sólo el dispositivo local.

**Endpoint:** `GET 127.0.0.1:30080/api/device/list/`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**

```
Ninguno
```

**Respuesta — ejemplo:**

```
{
    "total": "1",
    "message": "ok",
    "data": [
        "C16-D00-A000F"
    ]
}
```

---


# 5. Interfaz de programa /api/program/

La interfaz de programas se utiliza principalmente para operar contenido relacionado con programas.


## 5.1 Estructura de los programas (JSON)

Los datos del programa se representan en formato JSON. 

- El array `"data"` contiene los programas en orden. Estos irán mostrándose uno después de otro.
- Cada **programa** tiene una UUID identificativa, y una o varias áreas. El programa controla la duración de reproducción y las horas y días en los que el programa está habilitado.
- Cada **área** tiene unos parámetros de posición (x, y, width, height) y contiene un item, o bien varios items en orden que irán mostrándose uno después de otro.
- Cada **ítem** puede ser un elemento de texto, imagen, etc. con sus atributos correspondientes. También tiene una UUID única.

![](images/Pasted%20image%2020260520173128.png)

Cada programa viene identificado con una **UUID única que lo identifica**. Si al crear el programa no se especifica una UUID el dispositivo genera una aleatoria.

Los items también tienen una UUID única. No es necesario especificarla, el dispositivo genera una aleatoria.

Los programas se reproducirán en bucle. La duración de cada programa se calcula automáticamente en función del contenido (se tiene en cuenta la duración del video, velocidad del texto y de las animaciones, etc.). **Para controlar cómo se reproducen los programas vaya a la sección "Control de reproducción de programa".**

>Tanto los programas como las áreas pueden tener un borde. Consulte el anexo acerca de los bordes disponibles.

## 5.2 Métodos de programa

Los métodos disponibles son:

| Método | Comportamiento | Cuándo usarlo |
|--------|----------------|----------------|
| `append` | Agrega **nuevo(s)** programa(s) al dispositivo. No modifica los existentes. | Para agregar un programa completamente nuevo a la lista sin afectar los existentes |
| `update` | Modifica un programa existente.<br>Requiere que el **UUID coincida exactamente**. | Para cambiar contenido, posición, tamaño o configuración de un programa existente |
| `replace` | **Reemplaza todos** los programas existentes con la nueva lista enviada. Elimina todo lo que no esté en la solicitud. | Sincronización completa: hacer que el dispositivo coincida exactamente con lo que se envía |
| `remove` | Elimina programa(s) específico(s) por UUID. | Para eliminar uno o más programas manteniendo los demás |
| `getAll` | Devuelve la lista de todos los programas (solo UUID + nombre, no el contenido completo) | Para ver qué programas existen en el dispositivo |



---
### 5.2.1 Método `append`

- **Comportamiento:** Agrega nuevo(s) programa(s) al dispositivo **sin tocar los programas existentes**.
- **Manejo de UUID:** Si proporcionas un UUID, el dispositivo lo usará. Si lo omites, el dispositivo genera uno automáticamente.

**Endpoint:** `POST 127.0.0.1:30080/api/program/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**
```json
{
    "method": "append",
    "data": [
	    {
		    "name": "programa 1",
		    "type": "normal",
            "uuid": "5373b76b-e52b-421d-809a-27ea6d9556b4",
            "area": [
			    /* contenido */
		    ]
	    },
	    {
		    "name": "programa 2",
		    "type": "normal",
            "uuid": "0181e8c4-e68a-1721-3754-6413ffbdab7d",
            "area": [
			    /* contenido */
		    ]
	    }
    ]
}
```

**Respuesta — ejemplo:**
```json
{
    "method": "append",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-007C6",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

---
### 5.2.2 Método `update`

- **Comportamiento:** Modifica un **programa existente** identificado por su UUID. El programa ya debe existir en el dispositivo.
- **Manejo de UUID:** El UUID es **obligatorio** y debe coincidir con un programa existente.


**Endpoint:** `POST 127.0.0.1:30080/api/program/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**
```json
{
    "method": "update",
    "data": [
	    {
		    "name": "programa 2",
		    "type": "normal",
            "uuid": "0181e8c4-e68a-1721-3754-6413ffbdab7d",
            "area": [
			    /* contenido */
		    ]
	    }
	]
}
```

**Respuesta — ejemplo:**
```json
{
    "method": "update",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-007C6",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

---
### 5.2.3 Método `replace`

- **Comportamiento:** **Reemplaza completamente todos los programas existentes** en el dispositivo con los programas proporcionados en la solicitud. Cualquier programa no incluido en la solicitud será eliminado.
- **Manejo de UUID:** Proporcionas los UUID para el nuevo conjunto de programas, si se omite el dispositivo genera uno automáticamente. El dispositivo eliminará todos los programas existentes.

**Endpoint:** `POST 127.0.0.1:30080/api/program/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**
```json
{
    "method": "replace",
    "data": [ { /* el conjunto completo de nuevos programas */ } ]
}
```

**Respuesta — ejemplo:**
```json
{
    "method": "replace",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-007C6",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

---
### 5.2.4 Método `remove` 

- **Comportamiento:** Elimina uno o más programas específicos del dispositivo.
- **Manejo de UUID:** El UUID es **obligatorio** para cada programa que se quiera eliminar. Debe coincidir con un programa existente.

**Endpoint:** `POST 127.0.0.1:30080/api/program/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**
```json
{
    "method": "remove",
    "data": [
        {
            "name": "Program 2",
            "uuid": "0181e8c4-e68a-1721-3754-6413ffbdab7d"
        },
        {
            "name": "Program 1",
            "uuid": "5373b76b-e52b-421d-809a-27ea6d9556b4"
        }
    ]
}
```

**Respuesta — ejemplo:**
```json
{
    "method": "remove",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-007C6",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

---
### 5.2.5 Método `getAll` 

> ⚠️ **IMPORTANTE:** `getAll` es el **único** método que requiere especificar el ID del dispositivo obligatoriamente. Los demás métodos funcionan sin ID en conexión directa.

- **Comportamiento:** Devuelve una lista de todos los programas almacenados en el dispositivo. Solo incluye el **UUID** y el **nombre** de cada programa, no el contenido.
- **Manejo de UUID:** No aplica — este método no requiere enviar UUID. Solo devuelve los que ya existen.

**Endpoint:** `POST 127.0.0.1:30080/api/program/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**
```json
{
    "method": "getAll",
    "data": []
}
```

**Respuesta — ejemplo:**
```json
{
    "method": "getAll",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-007C6",
            "message": "ok",
            "data": {
                "item": [
                    {
                        "id": "bd94107c-5b99-4b9d-a49d-aba6947b7c79",
                        "name": "Program 1"
                    },
                    {
                        "id": "4fd7e7dc-0818-4b6b-99a4-8b8964d310f7",
                        "name": "Program 2"
                    }
                ]
            }
        }
    ]
}
```

---
## 5.3 Control de reproducción de programa

Los programas pueden configurarse para tener una duración determinada, o acivar/desactivar su reproducción durante ciertas horas del día o en ciertas fechas determinadas, por ejemplo durante festividades, fines de semana, etc.



| Atributo | Tipo | Descripción | Ejemplo |
|----------|------|-------------|---------|
| `duration` | String (formato `HH:MM:SS`) | Duración total de reproducción del programa antes de pasar al siguiente.<br>Si no se especifica entonces el dispositivo lo calcula automáticamente en función del contenido.| `"00:00:30"` (30 segundos) |
| `time` | Array  | Horario de inicio y fin en el que la reproducción del programa está habilitada. Se aplica a diario. | `[{ "start": "00:00:00", "end": "06:00:00" }]` |
| `week` | Object | Días de la semana en los que el programa puede reproducirse. Los valores deben ser: Mon, Tue, Wed, Thur, Fri, Sat, Sun | `{ "enable": "Mon, Tue, Wed, Thur" }` |
| `date` | Array  | Rango(s) de fechas en los que el programa está activo. Cada objeto tiene `start` y `end` en formato `YYYY-MM-DD`. | `[{ "start": "2023-10-01", "end": "2024-10-01" }]` |

**Combinación de condiciones**
Todos los atributos se combinan con **AND lógico**. Se activa la reproducción de un programa  sólo si: 
- Es dentro del rango de `date` **Y**
- Es dentro del/los bloque(s) de `time` **Y**
- El día de la semana está en `week.enable`

**Ejemplo:**

```json
{
    "method": "append",
    "data": [
        {
            "name": "Program",
            "type": "normal",
            "area": [
				/* contenido */
            ],
			"playControl": {
				"duration": "00:00:30",
				"time": [
					{
						"start": "00:00:00",
						"end": "16:27:00"
					},
					{
						"start": "16:28:00",
						"end": "18:00:00"
					}
				],
				"week": {
					"enable": "Mon,Tue,Wed,Thur,Fri,Sat,Sun"
				},
				"date": [
					{
						"start": "2025-10-01",
						"end": "2026-10-01"
					}
				]
			}
        }
    ]
}
```


---
## 5.4 Ítem de texto
>Para crear textos. Admite distintas tipografías y efectos.

| Parámetro   | Valor de ejemplo | Tipo                                                           | Obligatorio | Descripción |
| ----------- | ---------------- | -------------------------------------------------------------- | ----------- | ----------- |
| type        | text             | String                                                         | Sí          | Indica que es un ítem de texto |
| string      |                  | String | Sí | Texto a mostrar |
| PlayText    | false            | Bool                                                           | No          | Voz (función no comprobada) |
| multiLine   | false            | Bool                                                           | No          | Texto multilínea.<br>`false` por defecto|
| alignment   | left             | String <br>`center`: centrado<br>`left`: izquierda<br>`right`: derecha | No          | Alineación horizontal<br>`center` por defecto |
| valignment  | top              | String <br>`middle`: centrado<br>`top`: arriba<br>`bottom`: abajo    | No          | Alineación vertical<br>`top` por defecto |
| font        |                  | Object | No | Consultar tabla de abajo |
| effect      |                  | Object | No | Consulte el apéndice de efectos

**Parámetros de `"font"`**

| Parámetro | Tipo          | Obligatorio | Descripción |
| --------- | ------------- | ----------- | ----------- |
| font.name      | String        | No          | Nombre de la fuente (consulte tabla)|
| font.size      | Int           | No          | Tamaño de fuente |
| font.color     | Color `"#RRGGBB"` | No          | Color de fuente |
| font.bold      | Bool          | No          | Negrita |
| font.italic    | Bool          | No          | Cursiva |
| font.underline | Bool          | No          | Subrayado |


> NOTA: para agregar efectos consulte el apéndice "Efectos".


### 5.4.1 Ejemplo de programa de texto:

**Endpoint:** `POST 127.0.0.1:30080/api/program/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Cuerpo de la solicitud:**

```json
{
    "method": "replace",
    "data": [
        {
            "name": "Program 1",
            "type": "normal",
            "uuid": "6eec618f-c5b9-478b-8528-7f7af1a5145e",
            "area": [
                {
                    "x": 0,
                    "y": 0,
                    "width": 128,
                    "height": 64,
                    "item": [
                        {
                            "type": "text",
                            "string": "This is a sliding text example     ",
                            "multiLine": false,
                            "valignment": "middle",
                            "font": {
                                "name": "Sans Serif",
                                "size": 24,
                                "bold": true,
                                "color": "#ffffff"
                            },
                            "effect": {
                                "type": 26,
                                "speed": 4
                            }
                        }
                    ]
                }
            ]
        }
    ]
}
```

**Respuesta — ejemplo:**
```json
{
    "method": "replace",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-007C6",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```


---
## 5.5 Ítem de imagen
> Para mostrar imágenes que ya estén subidas en el dispositivo, o bien descargarlas desde una URL.

| Parámetro | Valor de ejemplo | Tipo                                                                                                                                                                       | Obligatorio | Descripción |
| --------- | ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- | ----------- |
| type      | image            | String                                                                                                                                                                     | Sí          | Indica que es un ítem de imagen |
| fit       | stretch          | String<br>`fill`: escala la imagen hasta cubrir todo el área, mantiene las proporciones<br>`center`: Centra la imagen y la escala hasta ajustarse al area; pueden aparecer barras negras.<br>`stretch`: Estira la imagen hasta cubrir el área; puede distorsionar la imagen.<br>`tile`: Mosaico | No          | Modo de ajuste de imagen |
| file      |                  | String                                                                                                                                                                     | Sí          | Nombre de archivo en el dispositivo, o URL válida |
| fileMd5   |                  | String                                                                                                                                                                     | No          | Hash MD5 del archivo<br>Si ya hay un archivo descargado que coincida con `fileMD5` entonces se ignora `file` |
| fileSize  |                  | Int (Bytes)                                                                                                                                                                        | No          | Tamaño del archivo; si ya existe en el dispositivo, se omite la descarga |
| effect    |                  | Object | No | Consulte el apéndice de efectos


> NOTA: para agregar efectos consulte el apéndice "Efectos".

### 5.5.1 Ejemplo de programa de imagen:

>El ejemplo requiere subir previamente tres archivos img1.jpg, img2.jpg y img3.jpg al dispositivo con la herramienta RemoteServer.exe

**Endpoint:** `POST 127.0.0.1:30080/api/program/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**
```json
{
    "method": "replace",
    "data": [
        {
            "name": "Program 1",
            "type": "normal",
            "uuid": "f5b2c8c9-218a-4470-ae28-0d6b3ecd6cef",
            "area": [
                {
                    "x": 0,
                    "y": 0,
                    "width": 128,
                    "height": 64,
                    "item": [
                        {
                            "type": "image",
                            "file": "img1.jpg",
                            "fit": "stretch",
                            "effect": {
                                "type": 1,
                                "speed": 6,
                                "hold": 5000
                            }
                        },
                        {
                            "type": "image",
                            "file": "img2.jpg",
                            "fit": "stretch",
                            "effect": {
                                "type": 1,
                                "speed": 6,
                                "hold": 5000
                            }
                        },
                        {
                            "type": "image",
                            "file": "img3.jpg",
                            "fit": "stretch",
                            "effect": {
                                "type": 1,
                                "speed": 6,
                                "hold": 5000
                            }
                        }
                    ]
                }
            ]
        }
    ]
}
```

**Respuesta — ejemplo:**
```json
{
    "method": "replace",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-007C6",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

---
## 5.6 Ítem de video

| Parámetro | Valor de ejemplo | Tipo                                                                                                                                                                       | Obligatorio | Descripción |
| --------- | ---------------- | ---------- | ----------- | ----------- |
| type      | video            | String     | Sí          | Indica que es un ítem de vídeo |
| aspectRatio| false           | Bool       | No          | Mantener relación de aspecto |
| file      |                  | String     | Sí          | Nombre de archivo en el dispositivo, o URL válida |
| fileMd5   |                  | String     | No          | Hash MD5 del archivo<br>Si ya hay un archivo descargado que coincida con `fileMD5` entonces se ignora `file` |
| fileSize  |                  | Int (Bytes)| No          | Tamaño del archivo; si ya existe en el dispositivo, se omite la descarga |


### 5.6.1 Ejemplo de programa de vídeo:

>El ejemplo requiere subir previamente un archivo video.mp4 al dispositivo con la herramienta RemoteServer.exe

**Endpoint:** `POST 127.0.0.1:30080/api/program/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**
```json
{
    "method": "replace",
    "data": [
        {
            "name": "Program 1",
            "type": "normal",
            "uuid": "951b9039-b5ba-4d99-80ae-b156ca9c9f77",
            "area": [
                {
                    "x": 0,
                    "y": 0,
                    "width": 128,
                    "height": 64,
                    "item": [
                        {
                            "type": "video",
							"file": "video.mp4",
                            "aspectRatio": false
                        }
                    ]
                }
            ]
        }
    ]
}
```

**Respuesta — ejemplo:**
```json
{
    "method": "replace",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-007C6",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

---
## 5.7 Ítem de reloj digital
> Muestra en forma de **texto** los campos:
> - Título
> - Día de la semana
> - Fecha
> - Hora
>
>Se pueden activar y desactivar los campos que uno requiera. Se pueden mostrar en varias líneas o en una única línea.


| Parámetro              | Valor de ejemplo                                                | Tipo                                                                                                                             | Obligatorio | Descripción |
| ---------------------- | --------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- | ----------- | ----------- |
| type                   | digitalClock                                                    | String                                                                                                                           | Sí          | Indica que es un reloj digital |
| timezone               | Para España usar:<br>`"+1:00"` (invierno)<br>`"+2:00"` (verano)<br>"+8:00" por def                                           | String                                                                                                                           | No         | Zona horaria |
| timeOffset             | `"+00:05:00"` (adelantar) o `"-00:05:00"` (atrasar). Predeterminado: `0` | String                                                                                                                           | No          | Ajuste de hora |
| multiLine              | `false` por def.                                                | Bool                                                                                                                             | No          | Visualización multilínea |
| title.display          | `"true"` por def.                                               | String                                                                                                                           | No | Mostrar título |
| title.string           | `"LED"` por def.                                                | String                                                                                                                           | No         | Título |
| title.color            |                                                                 | String                                                                                                                           | No| Color del título |
| week.display           | `"true"` por def.                                                                 | String                                                                                                                           | No          | Mostrar día de semana |
| week.format            | `"0"` por def.                                                                 | String (valores fijos):<br>`"0"`: 星期一 (chino) <br>`"1"`: Monday <br>`"2"`: Mon                                                               | No          | Formato de día de semana |
| week.color             |                                                                 | String                                                                                                                           | No          | Color del día de semana |
| date.display           | `"true"` por def.                                                                 | String                                                                                                                           | No          | Mostrar fecha |
| date.format            | `"0"` por def.                                                                | String (valores fijos):<br>`"0"`: YYYY/MM/DD <br>`"1"`: MM/DD/YYYY <br>`"2"`: DD/MM/YYYY <br>`"3"`: Jan DD, YYYY <br>`"4"`: DD Jan, YYYY <br>`"5"`: YYYY年MM月DD日 <br>`"6"`: MM月DD日 | No          | Formato de fecha |
| date.color             |                                                                 | String                                                                                                                           | No          | Color de la fecha |
| time.display           | `"true"` por def.                                                                 | String                                                                                                                           | No          | Mostrar hora |
| time.format            |                                                                 | String (valores fijos):<br>`"0"`: hh:mm:ss <br>`"1"`: hh:ss <br>`"2"`: hh时mm分ss秒 <br>`"3"`: hh时mm分                                                  | No          | Formato de hora |
| time.color             |                                                                 | String                                                                                                                           | No          | Color de la hora |
| lunarCalendar.display  | `"false"` por def.                                                                 | String                                                                                                                           | No          | Mostrar calendario lunar (china) |
| lunarCalendar.color    |                                                                 | String                                                                                                                           | No          | Color del calendario lunar |
| font                   | | Object | No | Consultar tabla de abajo

**Parámetros de `"font"`:**

| Parámetro | Tipo          | Obligatorio | Descripción |
| --------- | ------------- | ----------- | ----------- |
| font.name      | String        | No          | Nombre de la fuente (consulte tabla)|
| font.size      | Int           | No          | Tamaño de fuente |
| font.color     | Color `"#RRGGBB"` | No          | Color de fuente.<br>Si se determina por ej. con `week.color` entonces `font.color` se ignora |


### 5.7.1 Ejemplo de programa de reloj digital:

**Endpoint:** `POST 127.0.0.1:30080/api/program/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**
```json
{
	"method": "replace",
	"data": [
        {
            "name": "Program 1",
            "type": "normal",
            "uuid": "d57af220-c138-4ab3-bb80-a6287cf635e4",
            "area": [
                {
                    "x": 0,
                    "y": 0,
                    "width": 128,
                    "height": 64,
                    "item": [
                        {
                            "type": "digitalClock",
                            "timezone": "+2:00",
                            "timeOffset": "",
                            "multiLine": true,
                            "font": {
                                "name": "Sans Serif",
                                "size": 15,
                                "color": "#ffff00"
                            },
                            "title": {
                                "string": "Digital Clock"
                            },
                            "date": {
                                "format": "2"
                            },
                            "week": {
                                "display": "false"
                            },
                            "time": {
                                "format": "0",
                                "color": "#ffffff"
                            }
                        }
                    ]
                }
            ]
	    }
    ]
}
```

**Respuesta — ejemplo:**
```json
{
    "method": "replace",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-007C6",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

---
## 5.8 Ítem de reloj analógico

**Endpoint:** `POST 127.0.0.1:30080/api/program/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Parámetros del cuerpo de la solicitud:**

| Parámetro              | Valor de ejemplo                                                | Tipo                                                                                                                             | Obligatorio | Descripción |
| ---------------------- | --------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- | ----------- | ----------- |
| type                   | dialClock                                                       | String                                                                                                                           | Sí          | Indica que es reloj analógico |
| timezone               | Para España usar:<br>`"+1:00"` (invierno)<br>`"+2:00"` (verano)<br>"+8:00" por def                                           | String                                                                                                                           | No          | Zona horaria |
| timeOffset             | `"+00:05:00"` (adelantar) o `"-00:05:00"` (atrasar). Predeterminado: `0` | String                                                                                                                           | No          | Ajuste de hora |
| title.display          | `"true"` por def.                                               | String                                                                                                                           | No | Mostrar título |
| title.string           | `"LED"` por def.                                                | String                                                                                                                           | No         | Título |
| title.color            |                                                                 | String                                                                                                                           | No| Color del título |
| week.display           | `"true"` por def.                                                                 | String                                                                                                                           | No          | Mostrar día de semana |
| week.format            | `"0"` por def.                                                                 | String (valores fijos):<br>`"0"`: 星期一 (chino) <br>`"1"`: Monday <br>`"2"`: Mon                                                               | No          | Formato de día de semana |
| week.color             |                                                                 | String                                                                                                                           | No          | Color del día de semana |
| date.display           | `"true"` por def.                                                                 | String                                                                                                                           | No          | Mostrar fecha |
| date.format            | `"0"` por def.                                                                | String (valores fijos):<br>`"0"`: YYYY/MM/DD <br>`"1"`: MM/DD/YYYY <br>`"2"`: DD/MM/YYYY <br>`"3"`: Jan DD, YYYY <br>`"4"`: DD Jan, YYYY <br>`"5"`: YYYY年MM月DD日 <br>`"6"`: MM月DD日 | No          | Formato de fecha |
| date.color             |                                                                 | String                                                                                                                           | No          | Color de la fecha |
| style.hourHandColor    | `"#ffffff"`                                                         | String                                                                                                                           | No          | Color de la manecilla de la hora |
| style.minuteHandColor  | `"#ffffff"`                                                         | String                                                                                                                           | No          | Color de la manecilla de los minutos |
| style.secondHandColor  | `"#ffffff"`                                                         | String                                                                                                                           | No          | Color de la manecilla de los segundos |
| style.hourScaleColor   | `"#ffffff"`                                                         | String                                                                                                                           | No          | Color de las marcas de la hora |
| style.minuteScaleColor | `"#ffffff"`                                                         | String                                                                                                                           | No          | Color de las marcas de los minutos |
| font                   | | Object | No | Consultar tabla de abajo

**Parámetros de `"font"`:**

| Parámetro | Tipo          | Obligatorio | Descripción |
| --------- | ------------- | ----------- | ----------- |
| font.name      | String        | No          | Nombre de la fuente (consulte tabla)|
| font.size      | Int           | No          | Tamaño de fuente |
| font.color     | Color `"#RRGGBB"` | No          | Color de fuente.<br>Si se determina por ej. con `week.color` entonces `font.color` se ignora |


### 5.8.1 Ejemplo de programa de reloj analógico:

**Endpoint:** `POST 127.0.0.1:30080/api/program/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**
```json
{
	"method": "replace",
	"data": [
		{
			"name": "Program 1",
			"type": "normal",
			"uuid": "6342eaf6-2728-4b4e-ae5d-eb0b607dbc40",
			"area": [
				{
					"x": 0,
					"y": 0,
					"width": 128,
					"height": 64,
					"item": [
						{
							"type": "dialClock",
							"timezone": "+2:00",
							"timeOffset": "",
							"font": {
								"name": "Sans Serif",
								"size": 9,
								"color": "#249763"
							},
                            "style": {
                                "hourHandColor": "#ffffff",
                                "minuteHandColor": "#ffffff",
                                "secondHandColor": "#cd3278",
                                "hourScaleColor": "#ffffff",
                                "minuteScaleColor": "#ffffff"
                            },
							"title": {
								"string": "reloj"
							},
							"date": {
								"format": "3"
							},
							"week": {
								"format": "2"
							}
						}
					]
				}
			]
		}
	]
}
```

Respuesta — ejemplo:

```json
{
    "method": "replace",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-007C6",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

---
## 5.9 Ítem de área dinámica
> Consiste en un área de texto con la posibilidad de usar variables y actualizar estas variables con`pushStatus` o `setPeriodicTask`.

| Parámetro   | Valor de ejemplo | Tipo                                                           | Obligatorio | Descripción |
| ----------- | ---------------- | -------------------------------------------------------------- | ----------- | ----------- |
| type        | dynamic             | String                                                         | Sí          | Indica que es un ítem de área dinámica |
| string      |                  | String | Sí | Texto a mostrar. Las variables se intercalan así: {{var}} |
| keys        | "var1,var2"                 | String | Sí | Lista de variables, separada por comas |
| multiLine   | false            | Bool                                                           | No          | Texto multilínea.<br>`false` por defecto|
| alignment   | left             | String <br>`center`: centrado<br>`left`: izquierda<br>`right`: derecha | No          | Alineación horizontal<br>`center` por defecto |
| valignment  | top              | String <br>`middle`: centrado<br>`top`: arriba<br>`bottom`: abajo    | No          | Alineación vertical<br>`top` por defecto |
| font        |                  | Object | No | Consultar tabla de abajo |
| effect      |                  | Object | No | Consulte el apéndice de efectos


**Parámetros de `"font"`:**

| Parámetro | Tipo          | Obligatorio | Descripción |
| --------- | ------------- | ----------- | ----------- |
| font.name      | String        | No          | Nombre de la fuente (consulte tabla)|
| font.size      | Int           | No          | Tamaño de fuente |
| font.color     | Color `"#RRGGBB"` | No          | Color de fuente |
| font.bold      | Bool          | No          | Negrita |
| font.italic    | Bool          | No          | Cursiva |
| font.underline | Bool          | No          | Subrayado |

### 5.9.1 Ejemplo de programa de área dinámica:

**Endpoint:** `POST 127.0.0.1:30080/api/program/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**
```json
{
    "data": [
        {
            "name": "Program 1",
            "type": "normal",
            "uuid": "10d5e2e9-f771-415f-85a9-3810930e385f",
            "area": [
                {
                    "x": 0,
                    "y": 0,
                    "width": 128,
                    "height": 32,
                    "item": [
                        {
                            "type": "text",
                            "string": "Info del parking",
                            "alignment": "left",
                            "multiLine": false,
                            "font": {
                                "name": "Sans Serif",
                                "size": 14,
                                "underline": false,
                                "bold": true,
                                "italic": false,
                                "color": "#ffff00"
                            },
                            "effect": {
                                "type": 0,
                                "speed": 5,
                                "hold": 5000
                            }
                        }
                    ]
                },
                {
                    "x": 0,
                    "y": 32,
                    "width": 128,
                    "height": 32,
                    "item": [
                        {
                            "type": "dynamic",
                            "string": "Temp: {{Temperature}}°C<br>Plazas: {{ParkingSpace}}",
                            "keys": "Temperature,ParkingSpace",
                            "alignment": "center",
                            "font": {
                                "name": "Sans Serif",
                                "size": 14,
                                "underline": false,
                                "bold": true,
                                "italic": false,
                                "color": "#ffff00"
                            },
                            "effect": {
                                "type": 0,
                                "speed": 5,
                                "hold": 5000
                            }
                        }
                    ]
                }
            ]
        }
    ],
    "method": "replace"
}
```

**Respuesta — ejemplo:**
```json
{
    "method": "replace",
    "message": "ok",
    "data": [
        {
            "id": "C16L-D24-007C6",
            "message": "ok",
            "data": "kSuccess"
        }
    ]
}
```

---

# 6. Interfaz de archivo /api/file/
>Para subir archivos al dispositivo.

**Endpoint:** `POST 127.0.0.1:30080/api/file/{{deviceid}}`
**Content-Type:** `multipart/form-data`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Cuerpo de solicitud:**
```
(form-data)
```
Ejemplo en Postman:
![](images/Pasted%20image%2020260521134813.png)

**Respuesta — ejemplo:**
```json
{
    "data": [
        {
            "message": "ok",
            "name": "img1.jpg",
            "md5": "5eec9c31662766effdf6114eba9471c0",
            "size": "23047",
            "data": "http://192.168.1.13:30080/api/file/img1.jpg?_hdsdk_expired=1795265217&date=1779364017&sdkKey=2b2b5ea4aaa9503e&sign=6a6147db642c3325ecdaad30499c0f19&zzzzz=img1.jpg"
        }
    ],
    "message": "ok"
}
```


## 6.1 Cómo usar archivos subidos con la API
>NOTA: estas recomendaciones irán cambiando conforme el desarrollador de la API nos ofrezca más información. Un método alternativo a la API es usar la herramienta RemoteServer.exe

En la respuesta, el elemento `"data"` es una URL que usaremos para el programa de imagen.

![](images/Pasted%20image%2020260521163001.png)

Luego en el programa usamos esta URL para `"file"`, o bien tal cual o bien sustituyendo la ip por `localhost`. Ponemos su checksum MD5 y el tamaño en bytes.

![](images/Pasted%20image%2020260521164344.png)

## 6.2 Alternativa: subir archivos con RemoteServer.exe
Se incluye una utilidad llamada RemoteServer.exe con la que podemos subir archivos previamente a la memoria interna del dispositivo.

![](images/Pasted%20image%2020260521170011.png)

>Se incluye un pdf de cómo usar RemoteServer para subir archivos, en la carpeta de ejempos.

Luego en el programa de imagen ponemos el nombre del archivo, sin necesidad de especificar el MD5.

![](images/Pasted%20image%2020260521170908.png)



---

# 7. Interfaz de captura de pantalla /api/screenshot/

>Permite hacer una captura de pantalla para ver lo que está mostrando el dispositivo en este momento.

**Endpoint:** `GET 127.0.0.1:30080/api/screenshot/{{deviceid}}`
**Content-Type:** `application/json`

**Headers:**

| Parámetro | Valor de ejemplo    | Tipo   | Obligatorio |
| --------- | ------------------- | ------ | ----------- |
| sdkKey    | a7fa6795aaa891e2    | String | Sí          |

**Request body — ejemplo:**
```json
{
    "method": "screenshot",
    "data": {}
}
```

**Respuesta — ejemplo:**

```json
/* Cuando se utiliza la Interfaz General del Dispositivo para capturar una pantalla, la respuesta devuelve los datos de la imagen en base64 */
```

# 8. Apéndices

## 8.1 Apéndice 1: Efectos
>Los efectos puden aplicarse a items de **texto**, **imagen** y **área dinámica**.

| Parámetro | Tipo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | Obligatorio | Descripción |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- | ----------- |
| effect.type      | Int <br>0 : Visualización directa.<br>1 : Deslizar a la izquierda.<br>2 : Deslizar a la derecha.<br>3 : Deslizar hacia arriba.<br>4 : Deslizar hacia abajo.<br>5 : Cubrir a la izquierda.<br>6 : Cubrir a la derecha.<br>7 : Cubrir hacia arriba.<br>8 : Cubrir hacia abajo.<br>9 : Cubrir arriba a la izquierda.<br>10 : Cubrir abajo a la izquierda.<br>11 : Cubrir arriba a la derecha.<br>12 : Cubrir abajo a la derecha.<br>13 : Dividir horizontal.<br>14 : Dividir vertical.<br>15 : Cerrar horizontal.<br>16 : Cerrar vertical.<br>17 : Fundido de entrada/salida.<br>18 : Persiana vertical.<br>19 : Persiana horizontal.<br>20 : No limpiar pantalla.<br>25 : Efecto aleatorio.<br>// Los siguientes efectos solo son compatibles con el complemento de texto<br>26 : Desplazamiento continuo a la izquierda.<br>27 : Desplazamiento continuo a la derecha.<br>28 : Desplazamiento continuo hacia arriba.<br>29 : Desplazamiento continuo hacia abajo.<br>30 : Parpadeo. | Sí          | Tipo de efecto |
| effect.speed     | Int [0-8] (0 = más rápido, 8 = más lento)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | Sí          | Velocidad del efecto |
| effect.hold      | Int [0-9999999] (unidad: milisegundos)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | Sí          | Tiempo de retención |

**Ejemplo:**
```json
{
	"type": ["text"|"image"|"dynamic"],
	/* atributos */
	
	"effect": {
		"type": 1,
		"speed": 5,
		"hold": 5000
	}
}
```


## 8.2 Apéndice 2: Bordes
>Los bordes pueden aplicarse a una **área** o a un **programa entero**.

| Parámetro | Tipo   | Obligatorio | Descripción                                 |
| --------- | ------ | ----------- | ------------------------------------------- |
| type     | Int | Sí          | Tipo de borde (1 a 298). En HDPlayer puedes visualizar la lista de bordes completa. |
| effect    | String | Sí          | Efecto de borde (`"rotate"`, `"twinkle"`, `"static"`) |
| speed     | int    | No          | Velocidad del borde —<br>**rotate:** (no admite valores intermedios)<br>   3 = lento<br>   6 = medio<br>   9 = rápido<br>**twinkle:**<br>   0 a 10 (admite valores <0)<br>**static:**<br>   omitir|


**Ejemplo de bordes en dos áreas:**
```json
"area": [
	{
		"x": 0,
		"y": 0,
		"width": 128,
		"height": 32,
		"item": [ { /* item 1 */ }, { /* item 2 */ } ... ],
		"border": {
			"type": 0,
			"speed": 3,
			"effect": "rotate"
		}
	},
	{
		"x": 0,
		"y": 32,
		"width": 128,
		"height": 32,
		"item": [ { /* item 1 */ }, { /* item 2 */ } ... ],
		"border": {
			"type": 54,
			"speed": 6,
			"effect": "rotate"
		}
	}
]
```

**Ejemplo de borde en un programa entero:**
```json
{
    "method": "append",
    "data": [
        {
            "name": "Program 1",
            "type": "normal",
            "area": [ {/* área 1 */} ... ],
            "border": {
				"type": 195,
				"speed": 6,
				"effect": "rotate"
			}
        }
    ]
}
```
>**IMPORTANTE**: Añadir un borde en un **programa entero** hará que el espacio de programa sea menor. Tendrás que ajustar el tamaño de las áreas en función del grosor del borde.

## 8.3 Apéndice 3: Lista de fuentes disponibles

```
1. "Bitstream Vera Sans"
2. "Bitstream Vera Sans Mono"
3. "Bitstream Vera Serif"
4. "Cantarell"
5. "Century Schoolbook L"
6. "DejaVu Sans"
7. "DejaVu Sans Condensed"
8. "DejaVu Sans Mono"
9. "DejaVu Serif"
10. "DejaVu Serif Condensed"
11. "Dingbats"
12. "Inconsolata"
13. "Liberation Mono"
14. "Liberation Sans"
15. "Liberation Serif"
16. "Monospace"
17. "Nimbus Mono L"
18. "Nimbus Roman No9 L [urw]"
19. "Nimbus Roman No9 L [omega]"
20. "Nimbus Sans L"
21. "NSimSun"
22. "Sans Serif"
23. "Serif"
24. "SimSun"
25. "Standard Symbols L"
26. "URW Bookman L"
27. "URW Chancery L"
28. "URW Gothic L"
29. "URW Palladio L"
```

> La lista consiste en fuentes open-source y fuentes de símbolos ("Dingbats" y "Standard Symbols L"). Por el momento no es posible añadir fuentes.

## 8.4 Apéndice 4: Lista de códigos de error SDK
Lista sacada de la documentación del protocolo SDK "SDK3.0 Manual.pdf".
>Aunque no es necesario para el uso de esta API, se incluye en la carpeta "SDK 3.0".

| Código | Descripción |
|--------|-------------|
| `kSuccess` | Estado normal |
| `kWriteFinish` | Escritura de archivo completada |
| `kProcessError` | Error del procesador |
| `kVersionTooLow` | Versión demasiado baja |
| `kDeviceOccupa` | Dispositivo ocupado |
| `kFileOccupa` | Archivo ocupado |
| `kReadFileExcessive` | Demasiados usuarios intentando leer el archivo |
| `kInvalidPacketLen` | Longitud del paquete de datos incorrecta |
| `kInvalidParam` | Parámetro no válido |
| `kNotSpaceToSave` | Espacio de almacenamiento insuficiente |
| `kCreateFileFailed` | Error al crear el archivo |
| `kWriteFileFailed` | Error al escribir el archivo |
| `kReadFileFailed` | Error al leer el archivo |
| `kInvalidFileData` | Datos del archivo no válidos |
| `kFileContentError` | Error en el contenido del archivo |
| `kOpenFileFailed` | Error al abrir el archivo |
| `kSeekFileFailed` | Error al posicionarse en el archivo |
| `kRenameFailed` | Error al renombrar |
| `kFileNotFound` | Archivo no encontrado |
| `kFileNotFinish` | Archivo recibido pero incompleto |
| `kXmlCmdTooLong` | El comando XML es demasiado largo |
| `kInvalidXmlIndex` | Valor de índice del comando XML no válido |
| `kParseXmlFailed` | Error al analizar el XML |
| `kInvalidMethod` | Nombre del método no válido |
| `kMemoryFailed` | Error de memoria |
| `kSystemError` | Error del sistema |
| `kUnsupportVideo` | Video no compatible |
| `kNotMediaFile` | No es un archivo multimedia |
| `kParseVideoFailed` | Error al analizar el archivo de video |
| `kUnsupportFrameRate` | Frecuencia de cuadros no compatible |
| `kUnsupportResolution` | Resolución no compatible (video) |
| `kUnsupportFormat` | Formato no compatible (video) |
| `kUnsupportDuration` | Duración no compatible (video) |
| `kDownloadFileFailed` | Error al descargar el archivo |
| `kScreenNodeIsNull` | El nodo de pantalla es nulo |
| `kNodeExist` | El nodo ya existe |
| `kNodeNotExist` | El nodo no existe |
| `kPluginNotExist` | El plugin no existe |
| `kCheckLicenseFailed` | Error al verificar la licencia |
| `kNotFoundWifiModule` | Módulo WiFi no encontrado |
| `kTestWifiUnsuccessful` | Prueba del módulo WiFi fallida |
| `kRunningError` | Error en ejecución |
| `kUnsupportMethod` | Método no compatible |
| `kInvalidGUID` | GUID no válido |
| `kFirmwareFormatError` | Error en el formato del firmware |
| `kTagNotFound` | La etiqueta no existe |
| `kAttrNotFound` | El atributo no existe |
| `kCreateTagFailed` | Error al crear la etiqueta |
| `kUnsupportDeviceType` | Tipo de dispositivo no compatible |

---

# Links

Documentación original:
https://gitee.com/szhuidu/cn.huidu.device.sdk/blob/master/README.en.md

Esta guía está basada en la documentación original de Hudiu, y la información ha sido rectificada, ampliada y mejorada. 2026

*Ejemplos probados en HD-C16L SDK con firmware 7.11.18.0 y hardware v1.0.*
