---
title: Sök på webben efter trend ande videor med hjälp av API för videosökning i Bing
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder API för videosökning i Bing för att söka på webben efter trend ande videor.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: ea6f79bf6c305ecc07b3e684ede15ad439039e7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "68500620"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Hämta videoklipp med API för videosökning i Bing 

Med API för videosökning i Bing kan du hitta dagens trender i videor från webben och i olika kategorier. 

## <a name="get-request"></a>GET-begäranden

Skicka följande GET-begäran för att få dagens trend ande videor från API för videosökning i Bing:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Marknads support

Följande marknader stöder trender i videor.  
 
-   en – AU (engelska, Australien)  
-   en-CA (engelska, Kanada)  
-   en-GB (engelska, Storbritannien)  
-   en-ID (engelska, Indonesien)  
-   en-IE (engelska, Irland)  
-   en-IN (engelska, Indien)  
-   en-NZ (engelska, Nya Zeeland)  
-   en-PH (engelska, Filippinerna)  
-   en-TG (engelska, Singapore)  
-   en-US (engelska, USA)  
-   en-WW (engelsk, global sammanställd kod)  
-   en-ZA (engelska, Sydafrika)  
-   zh-CN (kinesiska, Kina)

## <a name="example-json-response"></a>Exempel på JSON-svar  

I följande exempel visas ett API-svar som innehåller Trends videor, som listas efter kategori och under kategori. Svaret innehåller också banderoller, som är de populäraste trend ande videoklippen och kan komma från en eller flera kategorier.  

```json
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämta information om video](video-insights.md)