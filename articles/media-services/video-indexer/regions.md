---
title: Regioner där Video Indexer är tillgängligt – Azure
titleSuffix: Azure Media Services
description: Den här artikeln pratar om Azure-regioner där Azure Media Services Video Indexer är tillgängligt.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/08/2020
ms.author: kumud
ms.openlocfilehash: dd95f022e40b9ae6fa60a6536a87146049c53b68
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89565335"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Azure-regioner där Video Indexer finns

Video Indexer-API: er innehåller en **plats** parameter som du bör ange till den Azure-region som anropet ska dirigeras till. Det måste vara en [Azure-region där video Indexer är tillgängligt](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Platser

`location`Parametern måste tilldelas kod namnet för Azure-regionen som dess värde. Om du använder Video Indexer i förhands gransknings läge bör du ange `"trial"` värdet. `trial` är standardvärdet för `location` parametern. Om du vill hämta kod namnet för den Azure-region som ditt konto finns i och att ditt anrop ska dirigeras till kan du köra följande rad i [Azure CLI](/cli/azure):

```azurecli-interactive
az account list-locations
```

När du har kört raden som visas ovan får du en lista över alla Azure-regioner. Navigera till den Azure-region som har det *visnings* namn du söker och använd dess *Name* -värde för parametern **location** .

För Azure-regionen Västra USA 2 (visas nedan) använder du till exempel "westus2" för **plats** parametern.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Nästa steg

- [Anpassa språk modellen med hjälp av API: er](customize-language-model-with-api.md)
- [Anpassa varumärkes-modellen med API: er](customize-brands-model-with-api.md)
- [Anpassa person modellen med hjälp av API: er](customize-person-model-with-api.md)
