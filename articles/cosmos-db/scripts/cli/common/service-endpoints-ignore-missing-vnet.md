---
title: Anslut ett befintligt Azure Cosmos-konto med tjänst slut punkter för virtuella nätverk
description: Anslut ett befintligt Azure Cosmos-konto med tjänst slut punkter för virtuella nätverk
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 4fd434fcfcc560a69b1e223730021aa4dd32c334
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87432251"
---
# <a name="connect-an-existing-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Ansluta ett befintligt Azure Cosmos-konto med tjänst slut punkter för virtuella nätverk med hjälp av Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt, kräver det här avsnittet att du kör Azure CLI-version 2.9.1 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

Det här exemplet är avsett att visa hur du ansluter ett befintligt Azure Cosmos-konto till ett befintligt nytt virtuellt nätverk där under nätet ännu inte har kon figurer ATS för tjänst slut punkter med hjälp av `ignore-missing-vnet-service-endpoint` parametern. Detta gör att konfigurationen av Cosmos-kontot kan slutföras utan fel innan konfigurationen till det virtuella nätverkets undernät har slutförts. När under näts konfigurationen är klar kommer Cosmos-kontot att vara tillgängligt via det konfigurerade under nätet.

> [!NOTE]
> Det här exemplet visar hur du använder ett SQL (Core) API-konto. Om du vill använda det här exemplet för andra API: er använder du `enable-virtual-network` `virtual-network-rules` parametrarna och i skriptet nedan för ditt API-skript.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints-ignore-missing-vnet.sh "Create an Azure Cosmos account with service endpoints.")]

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
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Skapar ett virtuellt Azure-nätverk. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Skapar ett undernät för ett virtuellt Azure-nätverk. |
| [AZ Network VNet Subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show) | Returnerar ett undernät för ett virtuellt Azure-nätverk. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Skapar ett Azure Cosmos DB-konto. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) | Uppdaterar ett undernät för ett virtuellt Azure-nätverk. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Cosmos DB CLI finns i [Azure Cosmos DB CLI-dokumentation](/cli/azure/cosmosdb).

Alla Azure Cosmos DB CLI-skript exempel finns i [Azure Cosmos DB CLI GitHub-lagringsplatsen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
