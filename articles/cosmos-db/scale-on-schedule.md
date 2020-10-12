---
title: Skala Azure Cosmos DB enligt ett schema med Azure Functions timer
description: Lär dig hur du skalar ändringar i genomflödet i Azure Cosmos DB med PowerShell och Azure Functions.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: ec5c98d90facf9458769f235880f17d14708e425
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87923663"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Skala Azure Cosmos DB genom att använda Azure Functions timer-utlösare

Prestanda för ett Azure Cosmos-konto baseras på mängden allokerat data flöde som uttrycks i enheter för programbegäran per sekund (RU/s). Etableringen är i en andra kornig het och faktureras baserat på flest RU/s per timme. Den här etablerade kapacitets modellen gör det möjligt för tjänsten att tillhandahålla ett förutsägbart och konsekvent data flöde, garanterad låg latens och hög tillgänglighet. De flesta produktions arbets belastningar de här funktionerna. I utvecklings-och testnings miljöer där Azure Cosmos DB endast används under arbets tid kan du skala upp data flödet i morgon och skala upp i kväll efter arbets tid.

Du kan ställa in data flödet via [Azure Resource Manager mallar](resource-manager-samples.md), [Azure CLI](cli-samples.md)och [PowerShell](powershell-samples.md), för kärn-(SQL) API-konton eller med hjälp av språkspecifika Azure Cosmos DB SDK: er. Fördelen med att använda Resource Manager-mallar, Azure CLI eller PowerShell är att de stöder alla Azure Cosmos DB modell-API: er.

## <a name="throughput-scheduler-sample-project"></a>Exempel projekt för data flödes scheman

För att förenkla processen att skala Azure Cosmos DB enligt ett schema har vi skapat ett exempel projekt med namnet [Azure Cosmos data flöde Scheduler](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler). Det här projektet är en Azure Functions-app med två timer-utlösare – "ScaleUpTrigger" och "ScaleDownTrigger". Utlösarna kör ett PowerShell-skript som anger data flödet för varje resurs som definieras i `resources.json` filen i varje utlösare. ScaleUpTrigger har kon figurer ATS för att köras kl. 8, UTC och ScaleDownTrigger har kon figurer ATS att köras på 6 PM UTC och dessa tider kan enkelt uppdateras i `function.json` filen för varje utlösare.

Du kan klona det här projektet lokalt, ändra det för att ange Azure Cosmos DB resurser som ska skalas upp och ned och schemat ska köras. Senare kan du distribuera den i en Azure-prenumeration och skydda den med hjälp av hanterad tjänst identitet med [Azures rollbaserade åtkomst kontroll (Azure RBAC)](role-based-access-control.md) -behörigheter med rollen "Azure Cosmos DB operatör" för att ange data flöde för dina Azure Cosmos-konton.

## <a name="next-steps"></a>Nästa steg

- Läs mer och hämta exemplet från [Azure Cosmos DB Scheduler Scheduler](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).
