---
title: 'Snabb start: anropa din Anpassad sökning i Bing-slutpunkt med Node.js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Använd den här snabb starten för att börja begära Sök Resultat från Anpassad sökning i Bing-instansen med hjälp av Node.js.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 43710407386995bde6d3505286e96b0737e06f08
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91309794"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Snabb start: anropa din Anpassad sökning i Bing slut punkt med Node.js

Använd den här snabb starten för att lära dig hur du begär Sök Resultat från Anpassad sökning i Bing-instansen. Även om det här programmet är skrivet i Java Script är API för anpassad Bing-sökning en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk. Käll koden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Förutsättningar

- En instans av anpassad Bing-sökning. Mer information finns i [snabb start: skapa din första anpassad sökning i Bing-instans](quick-start.md).

- [Node.js JavaScript-körning](https://www.nodejs.org/).

- [Java Script Request-biblioteket](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

- Skapa en ny JavaScript-fil i valfri IDE eller redigeringsprogram och lägg till en `require()`-instruktion för biblioteket för begäranden. Skapa variabler för din prenumerations nyckel, anpassat konfigurations-ID och Sök villkor.

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Skicka och ta emot en sökbegäran 

1. Skapa en variabel för att lagra informationen som skickas i din begäran. Konstruera URL: en för begäran genom att lägga till sökordet i `q=` Frågeparametern och Sök instansens anpassade konfigurations-ID till `customconfig=` parametern. Avgränsa parametrarna med ett et-tecken ( `&` ). Du kan använda den globala slut punkten i följande kod eller använda den [anpassade slut domänen](../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. Använd Java Script Request-biblioteket för att skicka en sökbegäran till din Anpassad sökning i Bing instans och skriva ut information om resultaten, inklusive dess namn, URL och det datum då webb sidan senast crawlades.

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp för anpassad sökning](./tutorials/custom-search-web-page.md)
