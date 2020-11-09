---
title: Snabb start för Anpassad sökning i Bing python-klient bibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: 92df45e38a926b4a791315f731e1d1cb078d5852
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371560"
---
Kom igång med Anpassad sökning i Bing klient biblioteket för python. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Med API för anpassad Bing-sökning kan du skapa skräddarsydda, annons fria Sök upplevelser för ämnen som du bryr dig om. Du hittar käll koden för det här exemplet på [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py).

Använd Anpassad sökning i Bing klient bibliotek för python för att:
* Hitta Sök resultat på webben från Anpassad sökning i Bing-instansen.

[Referens dokumentation](/python/api/azure-cognitiveservices-search-customsearch/?view=azure-python)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch)  |  [Paket (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/)  |  [Exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Förutsättningar

- En instans av anpassad Bing-sökning. Se [snabb start: skapa din första anpassad sökning i Bing-instans](../../quick-start.md) för mer information.
- Python[ 2.x eller 3.x](https://www.python.org/) 
- [Anpassad sökning I Bing SDK för python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>Installera python-klient biblioteket

Installera klient biblioteket för Anpassad sökning i Bing med följande kommando.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Skapa ett nytt program

Skapa en ny python-fil i din favorit redigerare eller IDE och Lägg till följande importer.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Skapa en Sök klient och skicka en begäran

1. Skapa en variabel för din prenumerations nyckel och slut punkt.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Skapa en instans av med `CustomSearchClient` hjälp av ett `CognitiveServicesCredentials` objekt med prenumerations nyckeln. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. Skicka en Sök förfrågan med `client.custom_instance.search()` . Lägg till din sökterm till `query` parametern och Ställ in `custom_config` på ditt anpassade KONFIGURATIONS-ID för att använda din Sök instans. Du kan hämta ditt ID från [Anpassad sökning i Bing Portal](https://www.customsearch.ai/)genom att klicka på fliken **produktion** .

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Visa Sök resultaten

Om det finns Sök Resultat från en webb sida, hämta det första och skriv ut dess namn, URL och totalt antal webb sidor som hittas.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp för anpassad sökning](../../tutorials/custom-search-web-page.md)