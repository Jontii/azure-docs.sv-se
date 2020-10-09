---
title: Automatiska förslags Sök termer – API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: Para ihop API för webbsökning i Bing med API för automatiska förslag i Bing för att ge användarna en förbättrad Sök upplevelse.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 0fb62966c78eb19c1daf9294efba786a267ae200
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "66384864"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Autoföreslå Bing-sökord i ditt program

Om du tillhandahåller en sökruta där användaren anger sin sökterm bör du använda [API för automatiska förslag i Bing ](../bing-autosuggest/get-suggested-search-terms.md) för att ge bättre funktioner. API:t returnerar föreslagna frågesträngar baserat på partiella söktermer som användaren skriver in.

När användaren har angett en sökterm måste den vara URL-kodad innan [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) -Frågeparametern anges. Om användaren till exempel anger *segeljollar* ställer du in `q` till `sailing+dinghies` eller `sailing%20dinghies`.

Om frågetermen innehåller en stavnings fel innehåller Sök svaret ett [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) -objekt. Objektet visar den ursprungliga stavningen och den korrigerade stavningen som Bing använde för sökningen.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Du kan använda den här informationen för att låta användaren veta att du har ändrat deras frågesträng när du visar Sök resultaten.

![GRÄNSSNITTs exempel för frågeuttryck](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Nästa steg  

* Granska exempel [API för webbsökning i Bing svar](search-responses.md).  

## <a name="see-also"></a>Se även  

* [API för webbsökning i Bing referens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
