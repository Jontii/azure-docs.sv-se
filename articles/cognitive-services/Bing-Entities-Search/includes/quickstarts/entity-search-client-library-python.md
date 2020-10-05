---
title: Snabb start för Entitetssökning i Bing python-klient bibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 0c0a124773eab8166806312dc47ded24e1cd841f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "79136762"
---
Använd den här snabb starten för att börja söka efter entiteter med Entitetssökning i Bing klient bibliotek för python. Även om Entitetssökning i Bing har en REST API som är kompatibel med de flesta programmeringsspråk, är klient biblioteket ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Förutsättningar

* Python[ 2.x eller 3.x](https://www.python.org/)

* [SDK för entitetssökning i Bing för Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Vi rekommenderar att du använder en virtuell Python-miljö. Du kan installera och initiera den virtuella miljön med venv-modulen. Du kan installera virtuell miljö med:

```Console
python -m venv mytestenv
```

Installera klient biblioteket för Entitetssökning i Bing med:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

1. Skapa en ny Python-fil i valfri IDE eller redigeringsprogram och lägg till följande importinstruktioner. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchClient
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Skapa en variabel för din prenumerations nyckel och slut punkt. Instansiera klienten genom att skapa ett nytt `CognitiveServicesCredentials` objekt med din nyckel.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    client = EntitySearchclient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Skicka en sökbegäran och ta emot ett svar

1. Skicka en sökbegäran till entitetssökning i Bing med `client.entities.search()` och en sökfråga. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Om entiteter returnerades konverterar du `entity_data.entities.value` till en lista och skriver ut det första resultatet.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../../tutorial-bing-entities-search-single-page-app.md)

* [Vad är API för entitetsökning i Bing?](../../overview.md )