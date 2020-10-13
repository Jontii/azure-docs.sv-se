---
title: Så här hämtar du en Media processor instans med REST | Microsoft Docs
description: Lär dig hur du skapar en medie processor komponent för att koda, konvertera format, kryptera eller dekryptera medie innehåll för Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: ba625aa048360a4c201b91b4a5a4a7ca4dd6277b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89269513"
---
# <a name="how-to-get-a-media-processor-instance"></a>Så här hämtar du en medie processor instans

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Översikt
Medie processorer är en komponent som hanterar en enskild video-eller ljud bearbetnings uppgift, till exempel kodning, format konvertering, kryptering eller dekryptering av medie innehåll. Alla aktiviteter som skickas till Media Services kräver en medie processor för att koda, kryptera eller konvertera video-eller ljud innehållet. 

## <a name="azure-media-processors"></a>Azure Media-processorer 

Följande avsnitt innehåller listor över medie processorer:

* [Mediebearbetare för kodning](scenarios-and-availability.md#encoding-media-processors)
* [Mediebearbetare för analys](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>När du använder entiteter i Media Services måste du ange vissa huvud fält och värden i dina HTTP-begäranden. Mer information finns i [installations programmet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS-API: et finns i [komma åt Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Hämta en medie processor

Följande REST-anrop visar hur du hämtar en medie processor instans efter namn (i det här fallet **Media Encoder Standard**). 

Begäran:

```console
GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <token>
x-ms-version: 2.19
Host: media.windows.net
```

Svar:

```console
. . .

{  
   "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
   "value":[  
      {  
         "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "Description":"Media Encoder Standard",
         "Name":"Media Encoder Standard",
         "Sku":"",
         "Vendor":"Microsoft",
         "Version":"1.1"
      }
   ]
}
```


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du vet hur du hämtar en medie processor instans går du till [så här kodar du en till gångs](media-services-rest-get-started.md) artikel som visar hur du använder Media Encoder Standard för att koda en till gång.

