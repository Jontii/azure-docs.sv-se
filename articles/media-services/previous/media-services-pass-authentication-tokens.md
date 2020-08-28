---
title: Skicka autentiseringstoken till Azure Media Services | Microsoft Docs
description: Lär dig hur du skickar autentiseringstoken från klienten till den Azure Media Services nyckel leverans tjänsten
services: media-services
keywords: innehålls skydd, DRM, token-autentisering
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: a15afcba26c8c8dcc8b023e57d05b04dfa5e3e88
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89016724"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Lär dig hur klienter skickar token till den Azure Media Services nyckel leverans tjänsten
Kunderna frågar ofta hur en spelare kan skicka token till den Azure Media Services nyckel leverans tjänsten för verifiering så att spelaren kan hämta nyckeln. Media Services stöder formatet simple web token (SWT) och JSON Web Token (JWT). Token-autentisering används för alla typer av nycklar, oavsett om du använder common Encryption eller Advanced Encryption Standard (AES) kryptering i systemet.

 Beroende på vilken spelare och plattform du riktar in dig på, kan du skicka token med spelaren på följande sätt:

- Via HTTP-Authorization-huvudet.
    > [!NOTE]
    > Prefixet "Bearer" förväntas enligt OAuth 2,0-specifikationerna. En exempels pelare med token-konfigurationen finns på Azure Media Player [demonstrations sidan](https://ampdemo.azureedge.net/). Ange video källan genom att välja **AES (JWT-token)** eller **AES (SWT-token)**. Token skickas via Authorization-huvudet.

- Genom att lägga till en URL-frågeparameter med "token = tokenvalue".  
    > [!NOTE]
    > Prefixet "Bearer" förväntas inte. Eftersom token skickas via en URL måste du skydda token-strängen. Här är en exempel kod i C# som visar hur du gör det:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Via fältet CustomData.
Det här alternativet används endast för PlayReady License Acquisition genom CustomData-fältet i PlayReady License Acquisition Challenge. I det här fallet måste token finnas i XML-dokumentet som beskrivs här:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Lägg till din autentiseringstoken i token-elementet.

- Via en alternativ HTTP Live Streaming (HLS)-spelnings lista. Om du behöver konfigurera token-autentisering för AES + HLS-uppspelning på iOS/Safari, finns det inte något sätt som du kan skicka direkt i token. Mer information om hur du växlar spelnings listan för att aktivera det här scenariot finns i det här [blogg inlägget](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
