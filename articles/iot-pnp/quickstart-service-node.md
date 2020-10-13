---
title: Interagera med en IoT Plug and Play-enhet som är ansluten till din Azure IoT-lösning (Node.js) | Microsoft Docs
description: Använd Node.js för att ansluta till och interagera med en IoT Plug and Play-enhet som är ansluten till din Azure IoT-lösning.
author: elhorton
ms.author: elhorton
ms.date: 10/05/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-js
ms.openlocfilehash: a6ade8d44e6c751f45849743c66d0a34075943b4
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946135"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-nodejs"></a>Snabb start: interagera med en IoT Plug and Play-enhet som är ansluten till din lösning (Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT Plug and Play fören klar IoT genom att göra det möjligt att interagera med enhetens funktioner utan att du behöver ha kunskap om den underliggande enhets implementeringen. Den här snabb starten visar hur du använder Node.js för att ansluta till och styra en IoT Plug and Play-enhet som är ansluten till din lösning.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

För att slutföra den här snabb starten behöver du Node.js på din utvecklings dator. Du kan ladda ned den senaste rekommenderade versionen för flera plattformar från [NodeJS.org](https://nodejs.org).

Du kan kontrollera den aktuella versionen av Node.js på utvecklingsdatorn med följande kommando:

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Klona SDK-lagringsplatsen med exempel koden

Klona exemplen från en [Node SDK-lagringsplats](https://github.com/Azure/azure-iot-sdk-node). Öppna ett terminalfönster i valfri mapp. Kör följande kommando för att klona [Microsoft Azure IoT SDK för Node.js](https://github.com/Azure/azure-iot-sdk-node) GitHub-lagringsplats:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>Kör exempel enheten

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Läs mer om exempel konfigurationen i [README-exemplet](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

I den här snabb starten använder du ett exempel på en termostat-enhet som är skriven i Node.js som IoT Plug and Play-enheten. Så här kör du exempel enheten:

1. Öppna ett terminalfönster och navigera till den lokala mappen som innehåller Microsoft Azure IoT SDK för Node.js lagrings plats som du har klonat från GitHub.

1. Det här terminalfönstret används som **enhetens** Terminal. Gå till mappen för den klonade lagrings platsen och navigera till mappen */Azure-IoT-SDK-Node/Device/samples/PnP* . Installera alla beroenden genom att köra följande kommando:

    ```cmd/sh
    npm install
    ```

1. Kör exempel termostat-enheten med följande kommando:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Du ser meddelanden som säger att enheten har skickat viss information och rapporterat online. Dessa meddelanden indikerar att enheten har börjat skicka telemetridata till hubben och är nu redo att ta emot kommandon och egenskaps uppdateringar. Stäng inte den här terminalen, du behöver den för att bekräfta att tjänst exemplet fungerar.

## <a name="run-the-sample-solution"></a>Kör exempel lösningen

I [Konfigurera din miljö för iot plug and Play snabb starter och självstudier](set-up-environment.md) som du har skapat två miljövariabler för att konfigurera exemplet för att ansluta till din IoT-hubb och-enhet:

* **IOTHUB_CONNECTION_STRING**: den IoT Hub-anslutningssträng som du antecknade tidigare.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

I den här snabb starten använder du en exempel-IoT-lösning i Node.js för att interagera med den exempel enhet som du nyss konfigurerat.

1. Öppna ett annat terminalfönster som ska användas som **tjänstens** Terminal.

1. I den klonade nodens SDK-lagringsplats navigerar du till mappen */Azure-IoT-SDK-Node/service/samples/JavaScript* Installera alla beroenden genom att köra följande kommando:

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>Läsa en egenskap

1. När du körde exempel termostat-enheten i **enhets** terminalen såg du följande meddelanden som visar sin onlinestatus:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Gå till **tjänsten** Terminal och Använd följande kommando för att köra exemplet för att läsa enhets information:

    ```cmd/sh
    node twin.js
    ```

1. Lägg märke till enhetens svar i terminalen för **tjänsten** . Du ser enhetens modell-ID och tillhör ande egenskaper som rapporter ATS:

    ```json
    Model Id: dtmi:com:example:Thermostat;1
    {
      "deviceId": "my-pnp-device",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "Njc3MDMxNDcy",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Connected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "dtmi:com:example:Thermostat;1",
      "version": 4,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:19.4574755Z"
          },
          "$version": 1
        },
        "reported": {
          "maxTempSinceLastReboot": 31.343640523762232,
          "serialNumber": "123abc",
          "$metadata": {
            "$lastUpdated": "2020-10-05T11:35:23.7339042Z",
            "maxTempSinceLastReboot": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            },
            "serialNumber": {
              "$lastUpdated": "2020-10-05T11:35:23.7339042Z"
            }
          },
          "$version": 3
        }
      },
      "capabilities": {
        "iotEdge": false
      },
      "tags": {}
    }
    ```

1. I följande kodfragment visas koden i *twin.js* som hämtar enhetens modell-ID:

    ```javascript
    var registry = Registry.fromConnectionString(connectionString);
    registry.getTwin(deviceId, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log('Model Id: ' + twin.modelId);
        //...
      }
      //...
    }
    ```

I det här scenariot matas den ut `Model Id: dtmi:com:example:Thermostat;1` .

> [!NOTE]
> Dessa tjänst exempel använder **register** klassen från **IoT Hub-tjänst klienten**. Mer information om API: er, inklusive digitala dubbla API: er, finns i [service Developer-guiden](concepts-developer-guide-service.md).

### <a name="update-a-writable-property"></a>Uppdatera en skrivbar egenskap

1. Öppna filen *twin.js* i en kod redigerare.

1. Granska exempel koden, visar hur du kan uppdatera enheten på två sätt. Om du vill använda det första sättet ändrar du `twinPatch` variabeln enligt följande:

    ```javascript
    var twinPatch = {
      tags: {
        city: "Redmond"
      },
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    ```

    `targetTemperature`Egenskapen definieras som en skrivbar egenskap i termostat-enhets modellen.

1. I **tjänsten** Terminal använder du följande kommando för att köra exemplet för att uppdatera egenskapen:

    ```cmd/sh
    node twin.js
    ```

1. I **enhetens** Terminal ser du att enheten har tagit emot uppdateringen:

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    ```

1. Kör följande kommando i din **tjänst-** Terminal för att bekräfta att egenskapen har uppdaterats:

    ```cmd/sh
    node twin.js
    ```

1. I **tjänstens** Terminal-utdata, i avsnittet ¬ rapporterade egenskaper, ser du att den uppdaterade mål temperaturen har rapporter ATS. Det kan ta en stund innan enheten har slutfört uppdateringen. Upprepa det här steget tills enheten har bearbetat egenskaps uppdateringen:

    ```json
    "reported": {
      //...
      "targetTemperature": {
        "value": 42,
        "ac": 200,
        "ad": "Successfully executed patch for targetTemperature",
        "av": 4
      },
      //...
    }
    ```

### <a name="invoke-a-command"></a>Anropa ett kommando

1. Öppna filen *device_method.js* och granska koden.

1. Gå till **tjänsten** Terminal. Använd följande kommando för att köra exemplet för att anropa kommandot:

    ```cmd/sh
    set IOTHUB_METHOD_NAME=getMaxMinReport
    set IOTHUB_METHOD_PAYLOAD=commandpayload
    node device_method.js
    ```

1. Utdata i **tjänst** terminalen visar följande bekräftelse:

    ```cmd/sh
    getMaxMinReport on my-pnp-device:
    {
      "status": 200,
      "payload": {
        "maxTemp": 23.460596940801928,
        "minTemp": 23.460596940801928,
        "avgTemp": 23.460596940801928,
        "endTime": "2020-10-05T12:48:08.562Z",
        "startTime": "2020-10-05T12:47:54.450Z"
      }
    }
    ```

1. I **enhetens** Terminal ser du att kommandot har bekräftats:

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-lösning. Mer information om IoT Plug and Play enhets modeller finns i:

> [!div class="nextstepaction"]
> [IoT Plug and Play Modeling Developer Guide](concepts-developer-guide-device-csharp.md)
