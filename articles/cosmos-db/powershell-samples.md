---
title: Azure PowerShell exempel för API för Azure Cosmos DB Core (SQL)
description: Hämta Azure PowerShell exempel för att utföra vanliga uppgifter i Azure Cosmos DB för Core-API (SQL)
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: 174458d9e5be24de669060ea8264f62017489c7a
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92279319"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-core-sql-api"></a>Azure PowerShell exempel för API för Azure Cosmos DB Core (SQL)

Följande tabell innehåller länkar till vanliga Azure PowerShell skript för Azure Cosmos DB. Använd länkarna till höger för att navigera till API-/regionsspecifika exempel. Vanliga exempel är samma för alla API: er. Referens sidor för alla Azure Cosmos DB PowerShell-cmdletar finns i [referensen Azure PowerShell](/powershell/module/az.cosmosdb). Sök efter uppdateringar `Az.CosmosDB` regelbundet. Du kan också förgrena dessa PowerShell-exempel för Cosmos DB från vår GitHub-lagringsplats [Cosmos DB PowerShell-exempel på GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

PowerShell-cmdletar för andra API: er i PowerShell-exempel för [Cassandra](powershell-samples-cassandra.md), PowerShell-exempel för [MongoDB-API](powershell-samples-mongodb.md), PowerShell-exempel för [Gremlin](powershell-samples-gremlin.md), [PowerShell-exempel för tabell](powershell-samples-table.md)

## <a name="common-samples"></a>Vanliga exempel

|Uppgift | Beskrivning |
|---|---|
|[Uppdatera ett konto](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera standard konsekvens nivån för ett Cosmos DB-konto. |
|[Uppdatera ett kontos regioner](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera ett Cosmos DB kontos regioner. |
|[Ändra växlings prioritet eller utlös redundans](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ändra den regionala växlings prioriteten för ett Azure Cosmos-konto eller utlösa en manuell redundansväxling. |
|[Konto nycklar eller anslutnings strängar](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hämta primära och sekundära nycklar, anslutnings strängar eller återskapa en konto nyckel för ett Azure Cosmos DB konto. |
|[Skapa ett Cosmos-konto med IP-brandvägg](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa ett Azure Cosmos DB-konto med IP-brandvägg aktive rad. |
|||

## <a name="core-sql-api-samples"></a>API-exempel för Core (SQL)

|Uppgift | Beskrivning |
|---|---|
|[Skapa ett konto, en databas och en behållare](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa ett Azure Cosmos DB konto, en databas och en behållare. |
|[Skapa ett konto, en databas och en behållare med autoskalning](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa ett Azure Cosmos DB konto, en databas och en behållare med autoskalning. |
|[Skapa en behållare med en stor partitionsnyckel](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa en behållare med en stor partitionsnyckel. |
|[Skapa en behållare utan index princip](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapa en Azure Cosmos-behållare med index princip inaktive rad.|
|[Lista eller hämta databaser eller behållare](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lista eller hämta databas eller behållare. |
|[Data flödes åtgärder](scripts/powershell/sql/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Data flödes åtgärder för en databas eller behållare, inklusive Hämta, uppdatera och migrera mellan autoskalning och standard data flöde. |
|[Lås resurser från borttagning](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Förhindra att resurser tas bort med resurs lås. |
|||
