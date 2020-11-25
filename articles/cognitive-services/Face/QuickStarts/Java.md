---
title: 'Snabbstart: Identifiera ansikten i en bild med Azure REST API och Java'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du använda Azure ansikts-REST API med Java för att identifiera ansikten i en bild.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: b8ea2908711497db5e86a7dd665548e33b9d9f25
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020865"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Snabbstart: Identifiera ansikten i en bild med REST API och Java

I den här snabb starten ska du använda Azures ansikts REST API med Java för att identifiera mänskliga ansikten i en bild.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar. 

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" skapar du en ansikts resurs "  target="_blank"> skapa en ansikts resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Ansikts-API. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* En valfri Java IDE.

## <a name="create-the-java-project"></a>Skapa Java-projekt

1. Skapa en ny kommando rads java-app i IDE och Lägg till en **huvud** klass med en **main** -metod.
1. Importera följande bibliotek till Java-projektet. Om du använder Maven så tillhandahålls Maven-koordinaterna för varje bibliotek.
   - [Apache HTTP-klient](https://hc.apache.org/downloads.cgi) (org. apache. httpcomponents: httpclient: 4.5.6)
   - [Apache HTTP Core](https://hc.apache.org/downloads.cgi) (org. apache. httpcomponents: httpcore: 4.4.10)
   - [JSON-biblioteket](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Apache Commons-loggning](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (Commons-Logging: Commons-Logging: 1.1.2)

## <a name="add-face-detection-code"></a>Lägga till kod för ansiktsigenkänning

Öppna huvudklassen i ditt projekt. Här lägger du till den kod som behövs för att läsa in bilder och identifiera ansikten.

### <a name="import-packages"></a>Importera paket

Lägg till följande `import`-instruktioner överst i filen.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="dependencies":::

### <a name="add-essential-fields"></a>Lägga till grundläggande fält

Ersätt **huvud** klassen med följande kod. Dessa data anger hur du ansluter till ansiktsigenkänningstjänsten och var du hämtar indata. Du måste uppdatera `subscriptionKey` fältet med värdet för din prenumerations nyckel och ändra `uriBase` strängen så att den innehåller rätt slut punkts sträng. Du kan också ange värdet `imageWithFaces` till en sökväg som pekar på en annan bildfil.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

Fältet `faceAttributes` är en lista över vissa typer av attribut. Det anger vilken information som ska hämtas om de identifierade ansiktena.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="environment":::

### <a name="call-the-face-detection-rest-api"></a>Anropa REST API för ansiktsigenkänning

Lägg till **main** -metoden med följande kod. Den skapar ett REST-anrop till Ansikts-API för att identifiera ansiktsinformation i fjärrbilden (`faceAttributes`-strängen anger vilka ansiktsattribut som ska hämtas). Sedan skriver den utdata till en JSON-sträng.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="main":::

### <a name="parse-the-json-response"></a>Tolka JSON-svaret

Direkt under föregående kod lägger du till följande block, som konverterar returnerade JSON-data till ett mer lättläst format innan du skriver ut dem till konsolen. Slutligen avslutar du try-catch-block, **main** -metoden och **main** -klassen.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="print":::

## <a name="run-the-app"></a>Kör appen

Kompilera koden och kör den. Ett lyckat svar visar ansiktsinformation i lättläst JSON-format i konsolfönstret. Exempel:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  }
}]
```

## <a name="extract-face-attributes"></a>Extrahera ansikts attribut
 
Om du vill extrahera ansikts attribut använder du identifierings modell 1 och lägger till `returnFaceAttributes` Frågeparametern.

```java
builder.setParameter("detectionModel", "detection_01");
builder.setParameter("returnFaceAttributes", "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise");
```

Svaret innehåller nu ansikts attribut. Exempel:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
        },
        {
          "color": "black",
          "confidence": 0.87
        },
        {
          "color": "other",
          "confidence": 0.51
        },
        {
          "color": "blond",
          "confidence": 0.08
        },
        {
          "color": "red",
          "confidence": 0.08
        },
        {
          "color": "gray",
          "confidence": 0.02
        }
      ]
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett enkelt Java-konsolprogram som använder REST-anrop till Azure-Ansikts-API för att identifiera ansikten i en bild och returnera deras attribut. Utforska sedan referensdokumentationen för Ansikts-API för att lära dig mer om de scenarier som stöds.

> [!div class="nextstepaction"]
> [Ansikts-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
