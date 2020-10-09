---
title: Belastnings Utjämnings trafik till virtuella datorer för HA-Azure CLI – Azure Load Balancer
description: Det här CLI-skriptexemplet visar hur du belastningsutjämnar trafik till virtuella datorer för hög tillgänglighet
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: e142316fa81ed8b669363c63f0b1550a890a890c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501754"
---
# <a name="azure-cli-script-example-load-balance-traffic-to-vms-for-high-availability"></a>Azure CLI-skriptexempel: Belastningsutjämna trafik för virtuella datorer för hög tillgänglighet

Det här Azure CLI-skriptexemplet skapar allt som behövs för att köra flera virtuella Ubuntu-datorer, konfigurerade med hög tillgänglighet och belastningsutjämning. När du har kört skriptet har du tre virtuella datorer som är anslutna till en Azure-tillgänglighetsuppsättning och är tillgängliga via Azure Load Balancer. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, virtuell dator, tillgänglighetsuppsättning, lastbalanserare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az-network-vnet-create) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-create) | Skapar en offentlig IP-adress med en statisk IP-adress och ett tillhörande DNS-namn. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#az-network-lb-create) | Skapar en Azure-lastbalanserare. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#az-network-lb-probe-create) | Skapar en lastbalanseringsavsökning. En lastbalanseringsavsökning används för att övervaka varje virtuell dator i lastbalanseringsuppsättningen. Om en virtuell dator blir otillgänglig dirigeras trafiken förbi den. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az-network-lb-rule-create) | Skapar en lastbalanseringsregel. I det här exemplet skapas en regel för port 80. När HTTP-trafik anländer vid lastbalanseraren dirigeras den till port 80, en av de virtuella datorerna i lastbalanseringsuppsättningen. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#az-network-lb-inbound-nat-rule-create) | Skapar en lastbalanseringsregel för NAT (Network Address Translation).  NAT-regler mappar en port för lastbalanseraren till en port på en virtuell dator. I det här exemplet skapas en NAT-regel för SSH-trafik till varje virtuell dator i lastbalanseringsuppsättningen.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#az-network-nsg-create) | Skapar en nätverkssäkerhetsgrupp (NSG), som är en säkerhetsgräns mellan internet och den virtuella datorn. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#az-network-nsg-rule-create) | Skapar en NSG-regel för att tillåta inkommande trafik. I det här exemplet öppnas port 22 för SSH-trafik. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#az-network-nic-create) | Skapar ett virtuellt nätverkskort och ansluter det till det virtuella nätverket, undernätet och NSG. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule#az-network-lb-rule-create) | Skapar en tillgänglighetsuppsättning. Tillgänglighetsuppsättningar garanterar programmets drifttid genom att fördela virtuella datorer mellan fysiska resurser så att hela uppsättningen inte berörs om något fel inträffar. |
| [az vm create](/cli/azure/vm#az-vm-create) | Skapar den virtuella datorn och ansluter den till nätverkskortet, ett virtuellt nätverk, ett undernät och en NSG. Kommandot specificerar även avbildningen av den virtuella dator som ska användas samt administrativa autentiseringsuppgifter.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare exempel på Azure Networking CLI-skript finns i [Azure Networking-dokumentationen](../cli-samples.md).
