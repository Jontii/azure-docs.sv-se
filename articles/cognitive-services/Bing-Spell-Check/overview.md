---
title: Vad är API för stavningskontroll i Bing?
titleSuffix: Azure Cognitive Services
description: Lär dig mer om API för Bing-stavningskontroll, som använder maskininlärning och statistisk maskinöversättning för kontextbaserad stavningskontroll.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 205d7991551ca48c3ea2e61baef53f5d06bdcbd2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "86200500"
---
# <a name="what-is-the-bing-spell-check-api"></a>Vad är API för stavningskontroll i Bing?

Med API för Bing-stavningskontroll kan du utföra kontextbaserad kontroll av grammatik och stavning i text. Medan de flesta stavningskontroller använder ordlistebaserade regeluppsättningar utnyttjar stavningskontroll i Bing maskininlärning och statistisk maskinöversättning för att ge noggranna och kontextbaserade korrigeringar. 

## <a name="features"></a>Funktioner

| Funktion | Beskrivning |
|---------|---------|
|Flera stavningskontrollslägen     | Med hjälp av flera stavningskontrollslägen kan du få korrigeringar som fokuserar på grammatik och/eller stavning. |
|Känner igen slang och talspråk     | Identifiera vanliga uttryck och informella termer som används i texten.         |
|Skilja mellan liknande ord     | Hitta rätt användning mellan ord som låter liknande men skiljer sig åt i betydelse (till exempel, ”see” och ”sea”)        |
|Stöd för varumärke, titlar och populär användning     | Identifiera nya varumärken, titlar och andra populära uttryck när de dyker upp |

## <a name="workflow"></a>Arbetsflöde

API:et för stavningskontroll för Bing är enkelt att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och tolka JSON-svaren. Tjänsten är tillgänglig med hjälp av REST-API:et och SDK:erna för stavningskontroll i Bing. 

1. Skapa ett [Cognitive Services-API-konto](../cognitive-services-apis-create-account.md) med åtkomst till API:er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto. 
2. Skicka en begäran till API:et för webbsökning i Bing.
3. Tolka JSON-svaret

## <a name="next-steps"></a>Nästa steg

Prova först den [interaktiva demon](https://azure.microsoft.com/services/cognitive-services/spell-check/) för söknings-API:et för stavningskontroll i Bing för att se hur du snabbt kan kontrollera en mängd olika texter.

När du är redo att anropa API:et skapar du ett [Cognitive Services-API-konto](../../cognitive-services/cognitive-services-apis-create-account.md). Om du inte har någon Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/free/cognitive-services/) kostnads fritt.

Du kan också besöka [sidan Bing-sökning API Hub](../bing-web-search/search-the-web.md) för att utforska de andra tillgängliga API: erna.