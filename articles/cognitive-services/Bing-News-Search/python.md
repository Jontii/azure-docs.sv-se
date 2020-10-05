---
title: 'Snabb start: utföra en nyhets sökning med python och Nyhetssökning i Bing REST API'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten om du vill skicka en begäran till REST-API:et för nyhetssökning i Bing med hjälp av Python och få ett JSON-svar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/16/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 115d0204b4e1a05e5d4c655efb52b3fc3123bebe
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "87851842"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Snabb start: utföra en nyhets sökning med python och Nyhetssökning i Bing REST API

Använd den här snabb starten för att göra ditt första anrop till API för nyhetssökning i Bing. Det här enkla python-programmet skickar en Sök fråga till API: et och bearbetar JSON-resultatet. 

Även om det här programmet är skrivet i python är API: et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

Om du vill köra det här kod exemplet som en Jupyter Notebook på en- [binder](https://mybinder.org)väljer du **Start binder** -märket: 

[![Starta binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

Källkoden till det här exemplet finns även på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Skapa och initiera appen

Skapa en ny Python-fil i valfri IDE eller redigeringsprogram och importera begärandemodulen. Skapa variabler för din prenumerations nyckel, slut punkt och Sök villkor. Du kan använda den globala slut punkten i följande kod eller använda den [anpassade slut domänen](../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

## <a name="create-parameters-for-the-request"></a>Skapa parametrar för begäran

Lägg till din prenumerationsnyckel i en ny ordlista med hjälp av `Ocp-Apim-Subscription-Key` som nyckel. Gör samma sak för sökparametrarna.

```python
headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
```

## <a name="send-a-request-and-get-a-response"></a>Skicka en begäran och få ett svar

1. Använd begär ande biblioteket för att anropa API för visuell sökning i Bing med din prenumerations nyckel och de Dictionary-objekt som du skapade i föregående steg.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = json.dumps(response.json())
    ```

2. Få åtkomst till beskrivningar av artiklarna som finns i svaret från API: et, som lagras i `search_results` som JSON-objekt. 
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="display-the-results"></a>Visa resultaten

Dessa beskrivningar kan sedan återges som en tabell med sökordet markerat i fetstil.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](tutorial-bing-news-search-single-page-app.md)
