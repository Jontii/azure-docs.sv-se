---
title: Azure PowerShell exempel för Azure Cosmos DB Tabell-API
description: Hämta Azure PowerShell exempel för att utföra vanliga uppgifter i Azure Cosmos DB Tabell-API
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: 4594f1d3f33f3fc20bfc5582f58945e5382a7db8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096180"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-table-api"></a>Azure PowerShell exempel för Azure Cosmos DB Tabell-API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Följande tabell innehåller länkar till vanliga Azure PowerShell skript för Azure Cosmos DB. Använd länkarna till höger för att navigera till API-/regionsspecifika exempel. Vanliga exempel är samma för alla API: er. Referens sidor för alla Azure Cosmos DB PowerShell-cmdletar finns i [referensen Azure PowerShell](/powershell/module/az.cosmosdb). Sök efter uppdateringar `Az.CosmosDB` regelbundet. Du kan också förgrena dessa PowerShell-exempel för Cosmos DB från vår GitHub-lagringsplats [Cosmos DB PowerShell-exempel på GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Vanliga exempel

|Uppgift | Beskrivning |
|---|---|
|[Uppdatera ett konto](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera standard konsekvens nivån för ett Cosmos DB-konto. |
|[Uppdatera ett kontos regioner](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera ett Cosmos DB kontos regioner. |
|[Ändra växlings prioritet eller utlös redundans](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ändra den regionala växlings prioriteten för ett Azure Cosmos-konto eller utlösa en manuell redundansväxling. |
|[Konto nycklar eller anslutnings strängar](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hämta primära och sekundära nycklar, anslutnings strängar eller återskapa en konto nyckel för ett Azure Cosmos DB konto. |
|[Skapa ett Cosmos-konto med IP-brandvägg](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa ett Azure Cosmos DB-konto med IP-brandvägg aktive rad. |
|||

## <a name="table-api-samples"></a>Tabell-API exempel

|Uppgift | Beskrivning |
|---|---|
|[Skapa ett konto och en tabell](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure Cosmos-konto och en tabell. |
|[Skapa ett konto och en tabell med autoskalning](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure Cosmos-konto och en tabell autoskalning. |
|[Lista eller hämta tabeller](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Visa eller hämta tabeller. |
|[Data flödes åtgärder](scripts/powershell/table/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Data flödes åtgärder för en tabell, inklusive Hämta, uppdatera och migrera mellan autoskalning och standard data flöde. |
|[Lås resurser från borttagning](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Förhindra att resurser tas bort med resurs lås. |
|||
