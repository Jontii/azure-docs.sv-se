---
title: Azure CLI-skript för data flöde (RU/s) åtgärder för API-resurser för Azure Cosmos DB Core (SQL)
description: Azure CLI-skript för data flöde (RU/s) åtgärder för API-resurser för Azure Cosmos DB Core (SQL)
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 5ad1e40f12609e708a2bb818175146967b7dccf2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839512"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>Data flöde (RU/s)-åtgärder med Azure CLI för en databas eller behållare för API för Azure Cosmos DB Core (SQL)

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt, kräver det här avsnittet att du kör Azure CLI-version 2.12.1 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar en kärn-API-databas (SQL) med delat genomflöde och en kärn-API-behållare med dedikerat data flöde och uppdaterar sedan data flödet för både databasen och behållaren. Skriptet migreras sedan från standard till autoskalning genom att läsa in värdet för autoskalning av data flödet när det har migrerats.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Throughput operations for a SQL database and container.")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Skapar ett Azure Cosmos DB-konto. |
| [AZ cosmosdb SQL Database Create](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | Skapar en Azure Cosmos Core-databas (SQL). |
| [Skapa AZ cosmosdb SQL container Create](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Skapar en Azure Cosmos Core-behållare (SQL). |
| [AZ cosmosdb SQL Database data flödes uppdatering](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | Uppdatering av data flöde för en Azure Cosmos Core-databas (SQL). |
| [AZ cosmosdb SQL container data flödes uppdatering](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | Uppdatering av data flöde för en Azure Cosmos Core-behållare (SQL). |
| [AZ cosmosdb SQL Database genomflödet migrera](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-migrate) | Migrera data flöde för en Azure Cosmos Core-databas (SQL). |
| [AZ cosmosdb SQL container data flöde migrera](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-migrate) | Migrera data flöde för en Azure Cosmos Core-behållare (SQL). |
| [az group delete](/cli/azure/resource#az-resource-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Cosmos DB CLI finns i [Azure Cosmos DB CLI-dokumentation](/cli/azure/cosmosdb).

Alla Azure Cosmos DB CLI-skript exempel finns i [Azure Cosmos DB CLI GitHub-lagringsplatsen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
