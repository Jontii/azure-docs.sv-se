---
title: Självstudie – Skapa ett program för video analys – identifiering av objekt och motion i Azure IoT Central (YOLO v3)
description: I den här självstudien visas hur du skapar ett video analys program i IoT Central. Du skapar den, anpassar den och ansluter den till andra Azure-tjänster. I den här självstudien används YOLO v3-systemet för objekt identifiering i real tid.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: ecc32908aea2fb474d2ebe5bd94f556527eda814
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763454"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-yolo-v3"></a>Självstudie: skapa ett program för video analys – identifiering av objekt och motion i Azure IoT Central (YOLO v3)

Som Solution Builder får du lära dig hur du skapar ett video analys program med programmallen IoT Central *video analys – objekt och rörelse identifiering* , Azure IoT Edge enheter, Azure Media Services och Yolo v3 real tids objekt och rörelse identifierings system. I lösningen används en detalj handels butik för att visa hur du kan möta de vanliga affärs behov för att övervaka säkerhets kameror. Lösningen använder automatisk objekt identifiering i ett video flöde för att snabbt identifiera och hitta intressanta händelser.

> [!TIP]
> Om du vill använda openYOLO &trade; i stället för v3 för objekt som en rörelse identifiering, se [självstudie: skapa ett program för video analys-objekt och motion-identifiering &trade; i Azure IoT Central (](tutorial-video-analytics-create-app-openvino.md)openövning).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) – den här filen hjälper dig att registrera de olika konfigurations alternativ som du behöver när du arbetar med de här självstudierna.
- [deployment.amd64.jspå](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.jspå](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json) du behöver bara hämta den här filen om du planerar att använda Intel NUC-enheten i den andra själv studie kursen.

> [!NOTE]
> GitHub-lagringsplatsen innehåller också käll koden för modulerna **LvaEdgeGatewayModule** och **lvaYolov3** IoT Edge. Mer information om hur du arbetar med käll koden finns i avsnittet [bygga lva Gateway-moduler](tutorial-video-analytics-build-module.md).

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Redigera distributions manifestet

Du distribuerar en IoT Edge-modul med ett distributions manifest. I IoT Central kan du importera manifestet som en enhets mall och sedan låta IoT Central hantera modul distributionen.

Så här förbereder du distributions manifestet:

1. Öppna filen *deployment.amd64.js* som du sparade i mappen *lva-konfiguration* med hjälp av en text redigerare.

1. Sök efter `LvaEdgeGatewayModule` inställningarna och se till att avbildningens namn är så som visas i följande kodfragment:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Lägg till namnet på ditt Media Services-konto i `env` noden i `LvaEdgeGatewayModule` avsnittet. Du antecknade Media Services konto namnet i *scratchpad.txt* -filen:

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_SERVICES_ACCOUNT_NAME>"
        }
    }
    ```

1. Mallen visar inte dessa egenskaper i IoT Central, så du måste lägga till Media Services-konfigurations värden i distributions manifestet. Leta upp `lvaEdge` modulen och ersätt plats hållarna med de värden som du antecknade i *scratchpad.txt* -filen när du skapade ditt Media Services-konto.

    `azureMediaServicesArmId`Är **resurs-ID: t** som du antecknade i *scratchpad.txt* -filen när du skapade Media Services-kontot.

    I följande tabell visas värdena från **anslutning till Media Services API (JSON)** i *scratchpad.txt* -filen som du bör använda i distributions manifestet:

    | Distributionsmanifest       | Scratchpad  |
    | ------------------------- | ----------- |
    | aadTenantId               | AadTenantId |
    | aadServicePrincipalAppId  | AadClientId |
    | aadServicePrincipalSecret | AadSecret   |

    > [!CAUTION]
    > Använd föregående tabell för att kontrol lera att du lägger till rätt värden i distributions manifestet, annars fungerar inte enheten.

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Spara ändringarna.

Den här självstudien konfigurerar din lösning att använda YOLO v3-modulen för objekt-och rörelse identifiering. I följande kodfragment visas konfigurationen av modulen:

```json
"lvaYolov3": {
    "settings": {
        "image": "mcr.microsoft.com/lva-utilities/yolov3-onnx:1.0"
    },
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "version": "1.0"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>Ersätt manifestet

På sidan **lva Edge Gateway v2** väljer du **+ Ersätt manifest**.

:::image type="content" source="./media/tutorial-video-analytics-create-app-yolo-v3/replace-manifest.png" alt-text="Ersätt manifest":::

Navigera till mappen *lva-Configuration* och välj *deployment.amd64.jspå* manifest filen som du redigerade tidigare. Välj **Överför**. När verifieringen är klar väljer du **Ersätt**.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]
