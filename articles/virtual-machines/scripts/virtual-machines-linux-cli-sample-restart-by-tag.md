---
title: Skript exempel för Azure CLI – starta om virtuella datorer
description: Skriptexempel för Azure CLI – Starta om virtuella datorer med tagg och ID
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 03a1e44ee3bdaa168fb3eb17078bfecb1f45c443
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87479497"
---
# <a name="restart-vms"></a>Starta om virtuella datorer

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Det här exemplet visar ett par olika sätt att hämta vissa virtuella datorer och starta om dem.

Det första startar om alla virtuella datorer i resursgruppen.

```azurecli
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

Det andra hämtar de taggade virtuella datorerna med hjälp av `az resource list` och filtrerar till de resurser som är virtuella datorer samt startar om de här virtuella datorerna.

```azurecli
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

Det här exemplet fungerar i ett Bash-gränssnitt. Körningsalternativ för Azure CLI-skript på Windows-klienten finns i [Installera Azure CLI på Windows](/cli/azure/install-azure-cli-windows).


## <a name="sample-script"></a>Exempelskript

Exemplet har tre skript.
Det första etablerar de virtuella datorerna.
Det använder alternativet no-wait så att kommandot returnerar utan att vänta på att varje virtuell dator ska etableras.
Det andra väntar på att de virtuella datorerna ska etableras helt.
Det tredje skriptet startar om alla de virtuella datorerna som etablerades, och sedan endast de taggade virtuella datorerna.

### <a name="provision-the-vms"></a>Etablera de virtuella datorerna

Det här skriptet skapar en resursgrupp och sedan skapar det tre virtuella datorer som ska startas om.
Två av dem märks med taggar.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "Provision the VMs")]

### <a name="wait"></a>Vänta

Det här skriptet kontrollerar etableringsstatusen var 20:e sekund tills alla tre virtuella datorer har etablerats eller tills en av dem inte går att etablera.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "Wait for the VMs to be provisioned")]

### <a name="restart-the-vms"></a>Starta om den virtuella datorn

Det här skriptet startar om alla virtuella datorer i resursgruppen och sedan startar det endast om taggade virtuella datorer.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "Restart VMs by tag")]

## <a name="clean-up-deployment"></a>Rensa distribution 

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgrupperna, virtuella datorer och alla relaterade resurser.

```azurecli-interactive
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, virtuell dator, tillgänglighetsuppsättning, lastbalanserare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az vm create](/cli/azure/vm/availability-set) | Skapar de virtuella datorerna.  |
| [az vm list](/cli/azure/vm) | Används med `--query` för att säkerställa att de virtuella datorerna etableras innan de startas om, och sedan för att hämta ID:na för de virtuella datorerna för att starta om dem. |
| [az resource list](/cli/azure/vm) | Används med `--query` att hämta ID:na för de virtuella datorerna med taggen. |
| [az vm restart](/cli/azure/vm) | Startar om de virtuella datorerna. |
| [az group delete](/cli/azure/vm/extension) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [Dokumentation för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
