---
title: Översikt över Azure Media Services v3
titleSuffix: Azure Media Services
description: En översikt på hög nivå av Azure Media Services v3 med länkar till snabb starter, självstudier och kod exempel.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, strömma, sändning, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: mvc
ms.openlocfilehash: b11cd602850d23ac6ec37a9be51912adceb9afbc
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267623"
---
# <a name="azure-media-services-v3-overview"></a>Översikt över Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services är en molnbaserad plattform för att skapa lösningar för videor i sändningskvalitet för direktuppspelning, förbättra tillgängligheten och distributionen, analysera innehållet och mycket mer. Oavsett om du är en app-utvecklare, ett Call Center, ett myndighets organ eller ett underhållnings företag, kan Media Services hjälpa dig att skapa appar som levererar medie upplevelser av enastående kvalitet till stora publiker på dagens mest populära mobila enheter och webbläsare.

Media Services v3-SDK: er baseras på [Media Services v3 openapi-specifikation (Swagger)](https://aka.ms/ams-v3-rest-sdk).

> [!NOTE]
> För närvarande kan du använda [Azure Portal](https://portal.azure.com/) för att: Hantera Media Services v3 [Live-händelser](live-events-outputs-concept.md), Visa (inte hantera) v3- [till gångar](assets-concept.md), [få information om åtkomst till API: er](./access-api-howto.md). För alla andra hanterings uppgifter (t. ex. [transformationer och jobb](transforms-jobs-concept.md) och [innehålls skydd](content-protection-overview.md)) använder du [REST API](/rest/api/media/), [CLI](https://aka.ms/ams-v3-cli-ref)eller någon av de [SDK](media-services-apis-overview.md#sdks): er som stöds.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Efterlevnad, sekretess och säkerhet

Som en viktig påminnelse måste du följa alla tillämpliga lagar i din användning av Azure Media Services och du får inte använda Media Services eller någon Azure-tjänst på ett sätt som strider mot andras rättigheter eller som kan vara skadliga för andra.

Innan du laddar upp en video/bild till Media Services måste du ha alla de rättigheter som krävs för att använda videon/bilden, inklusive, vid behov enligt lag, alla nödvändiga samspelade individer (om de finns) i videon/bilden, för användning, bearbetning och lagring av data i Media Services och Azure. Vissa jurisdiktioner kan införa särskilda juridiska krav för insamling, online-bearbetning och lagring av vissa kategorier av data, t. ex. Bio metriska data. Innan du använder Media Services och Azure för bearbetning och lagring av data som omfattas av särskilda juridiska krav, måste du säkerställa efterlevnaden av sådana juridiska krav som kan gälla för dig.

Om du vill veta mer om efterlevnad, sekretess och säkerhet i Media Services går du till Microsoft [Trust Center](https://www.microsoft.com/trust-center/?rtc=1). För Microsofts sekretess krav, data hantering och bevarande praxis, inklusive hur du tar bort dina data, kan du läsa Microsofts [sekretess policy](https://privacy.microsoft.com/PrivacyStatement), [Online Services-villkoren](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("ost") och [data bearbetnings tillägget](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Genom att använda Media Services samtycker du till att vara kopplad till OST-, DPA-och sekretess policyn.
 
## <a name="what-can-i-do-with-media-services"></a>Vad kan jag göra med Media Services?

Med Media Services kan du bygga en mängd olika medie arbets flöden i molnet. Några exempel på vad du kan göra med Media Services är:

* Leverera video i olika format, så att de kan spelas upp på en mängd olika webbläsare och enheter. För att kunna leverera både på begäran och Live streaming till olika klienter (mobila enheter, TV, datorer och så vidare) måste video-och ljud innehållet kodas och paketeras korrekt. Information om hur du levererar och strömmar innehållet finns i [Snabbstart: koda och strömma filer](stream-files-dotnet-quickstart.md).
* Strömma direktsända sport händelser till en stor online-publik, till exempel fotboll, baseboll, skol sport och gymnasie sport, med mera.
* Skicka offentliga möten och händelser, t. ex. stadens hus, ort rådets möten och lagstiftnings organ.
* Analysera inspelat video- eller ljudinnehåll. För att till exempel uppnå högre kundnöjdhet kan organisationer extrahera tal till text och skapa sökindex och instrumentpaneler. De kan sedan extrahera information kring vanliga klagomål, källor till klagomål och andra relevanta data.
* Skapa en prenumerationsvideotjänst och strömma DRM-skyddat innehåll när en kund (till exempel en filmstudio) behöver begränsa åtkomst och användning av upphovsrättsskyddat arbete.
* Leverera offline-innehåll för uppspelning på flygplan tåg och bilar. En kund kan behöva ladda ner innehåll till sin telefon eller surfplatta för uppspelning när de vet att de kan kopplas ned från nätverket.
* Implementera en video plattform för e-utbildning i utbildning med Azure Media Services och [Azure API:er för Cognitive Services](../../index.yml?pivot=products&panel=ai) för tal-till-text-textning, översättning till flera språk och så vidare.
* Använd Azure Media Services tillsammans med [API:er för Cognitive Services](../../index.yml?pivot=products&panel=ai) för att lägga till textning och beskrivningar till videor och därmed tillgodose en bredare publik (till exempel personer med handikapp) eller användare som vill kunna läsa på ett annat språk.
* Aktivera Azure CDN för att uppnå hög skalning för att bättre hantera omedelbara höga belastningar (till exempel start av en produktlansering).

## <a name="how-can-i-get-started-with-v3"></a>Hur kan jag komma igång med v3? 

Lär dig att koda och paketera innehåll, strömma videor på begäran, sända live och analysera dina videor med Media Services v3. Självstudier, API-referenser och annan dokumentation visar hur du säkert levererar ljud- eller videoströmmar på begäran och live, som kan skalas till miljontals användare.

> [!TIP]
> Innan du börjar utveckla bör du gå igenom följande:<br/>* [Grundläggande begrepp](concepts-overview.md) (incudes viktiga begrepp som paketering, kodning och skydd)<br/>* [Utveckla med Media Services v3-API: er](media-services-apis-overview.md) (innehåller information om åtkomst till API: er, namn konventioner och så vidare)

### <a name="sdks"></a>SDK:er

Börja utveckla med [Azure Media Services v3-klient-SDK](media-services-apis-overview.md#sdks): er.

### <a name="quickstarts"></a>Snabbstarter  

Snabb starterna visar grundläggande dags-1-instruktioner för nya kunder för att snabbt testa Media Services.

* [Strömma videofiler – .NET](stream-files-dotnet-quickstart.md)
* [Strömma videofiler – CLI](stream-files-cli-quickstart.md)
* [Strömma videofiler – Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>Självstudier

Självstudierna visar scenariobaserade procedurer för några av de främsta Media Services uppgifterna.

* [Koda fjärrfiler och strömma video – REST](stream-files-tutorial-with-rest.md)
* [Koda uppladdade filer och strömma video – NET](stream-files-tutorial-with-api.md)
* [Strömma live – .NET](stream-live-tutorial-with-api.md)
* [Analysera din video – .NET](analyze-videos-tutorial-with-api.md)
* [AES-128, dynamisk kryptering – .NET](protect-with-aes128.md)

### <a name="samples"></a>Exempel

Använd [den här exempel webbläsaren](/samples/browse/?products=azure-media-services) för att bläddra bland Azure Media Services kod exempel.

### <a name="how-to-guides"></a>Anvisningar

Instruktions guider innehåller kod exempel som visar hur du utför en uppgift. I det här avsnittet hittar du många exempel. Här är några av dem:

* [Skapa ett konto – CLI](./create-account-howto.md)
* [Använd API:er – CLI](./access-api-howto.md)
* [Koda med HTTPS som Job-inkommande – .NET](job-input-from-http-how-to.md)  
* [Övervaka händelser – portalen](monitor-events-portal-how-to.md)
* [Kryptera dynamiskt med multi-DRM-.NET](protect-with-drm.md) 
* [Koda med en anpassad Transform-CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om grundläggande begrepp](concepts-overview.md)
