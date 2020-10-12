---
title: Skript exempel för Azure CLI – peer två virtuella nätverk | Microsoft Docs
description: Använd ett skript exempel för Azure CLI för att skapa och ansluta två virtuella nätverk i samma region via Azure-nätverket.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 507e17b2edb0db2d21265f3e945745d97395e7c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87503862"
---
# <a name="use-an-azure-cli-sample-script-to-connect-two-virtual-networks"></a>Använd ett Azure CLI-exempel skript för att ansluta två virtuella nätverk

Det här skriptet skapar och ansluter två virtuella nätverk i samma region via Azure-nätverket. När du har kört skriptet har du en peering mellan två virtuella nätverk.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en virtuell dator och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [az network vnet peering create](https://docs.microsoft.com/cli/azure/network/vnet/peering) | Skapar en peering mellan två virtuella nätverk.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare exempel på CLI-nätverksskript finns i [dokumentation för översikt över Azure-nätverk](../cli-samples.md).
