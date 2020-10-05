---
title: 'Snabb start: Sök efter bilder med hjälp av Bildsökning i Bing REST API och Node.js'
titleSuffix: Azure Cognitive Services
description: Använd den här snabb starten för att skicka avbildnings Sök begär anden till Bildsökning i Bing REST API med hjälp av Java Script och JSON-svar.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-js
ms.openlocfilehash: b9f677c9596974129b38d56e8ef9aeb304c5f690
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91324917"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-nodejs"></a>Snabb start: Sök efter bilder med hjälp av Bildsökning i Bing REST API och Node.js

Använd den här snabb starten för att lära dig hur du skickar Sök begär anden till API för bildsökning i Bing. Det här JavaScript-programmet skickar en sökfråga till API:et och visar URL:en till den första bilden i resultatet. Även om det här programmet är skrivet i Java Script är API: et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

Käll koden för det här exemplet finns [på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js) med ytterligare fel hantering och anteckningar.

## <a name="prerequisites"></a>Förutsättningar

* Den senaste versionen av [Node.js](https://nodejs.org/en/download/).

* [Java Script Request-biblioteket](https://github.com/request/request).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Mer information finns i [Cognitive Services priser – Bing-sökning API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny JavaScript-fil i din favorit-IDE eller-redigerare och ange sträng-och HTTPS-krav.

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. Skapa variabler för API-slutpunkten, sökväg för bild-API, din prenumerationsnyckel och sökord. För `host` kan du använda den globala slut punkten i följande kod eller använda den anpassade slut [domänen](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>Konstruera sökbegäran och fråga.

1. Använd variablerna från det sista steget för att formatera en sök-URL för API-begäran. URL – koda Sök termen innan du skickar den till API: et.

    ```javascript
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + '?q=' + encodeURIComponent(search),
        headers : {
        'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    ```

2. Använd begäransbiblioteket för att skicka din fråga till API:et. 
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>Hantera och parsa svaret

1. Definiera en funktion med namnet `response_handler` som tar ett HTTP-anrop, `response`, som parameter. 

2. I den här funktionen definierar du en variabel som innehåller bröd texten i JSON-svaret. 

    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. Lagra bröd texten i svaret när `data` flaggan anropas.

    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

4. `end`Hämta det första resultatet från JSON-svaret när en flagga signaleras. Skriv ut URL:en för den första bilden samt det totala antalet returnerade bilder.

    ```javascript
    response.on('end', function () {
        let firstImageResult = imageResults.value[0];
        console.log(`Image result count: ${imageResults.value.length}`);
        console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
        console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
     });
    ```

## <a name="example-json-response"></a>Exempel på JSON-svar

Svar från API för bildsökning i Bing returneras som JSON. Det här exempelsvaret har trunkerats för att visa ett enskilt resultat.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ensidesapp](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Se även

* [Vad är API:et för bildsökning i Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Prova en interaktiv demo online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)
* [Pris information för API:er för Bing-sökresultat](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [Dokumentation om Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [API-referens för bildsökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
