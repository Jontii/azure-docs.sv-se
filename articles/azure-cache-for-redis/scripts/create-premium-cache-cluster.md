---
title: Skapa en Premium Azure-cache för Redis med klustring – Azure CLI
description: Detta exempel på Azure CLI-kod visar hur du skapar en Azure-cache på 6 GB Premium-nivå för Redis med klustring aktiverat och två Shards.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: ad29c7d12428d8f010017f9ef3a66cecb82db43a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502842"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Skapa en Premium Azure-cache för Redis med klustring

I det här scenariot får du lära dig hur du skapar en 6 GB Premium-nivå i Azure-cache för Redis med klustring aktiverat och två Shards.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en resurs grupp och en Premium-nivå i Azure-cache för Redis med aktive rad kluster. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [AZ Redis Create](https://docs.microsoft.com/cli/azure/redis) | Skapa Azure-cache för Redis-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare Azure-cache för Redis CLI-skript exempel finns i [Azure-cachen för Redis-dokumentationen](../cli-samples.md).
