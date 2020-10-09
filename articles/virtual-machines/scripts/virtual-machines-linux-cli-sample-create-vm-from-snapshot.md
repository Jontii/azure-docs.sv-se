---
title: Skapa en virtuell dator från ett snapshot-CLI-exempel
description: Azure CLI-skriptexempel – Skapa en virtuell dator från en ögonblicksbild
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: a6110ba2787cb99e20c099eb466e2dbd0c3df28e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87085307"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-cli"></a>Skapa en virtuell dator från en ögonblicksbild med CLI

Det här skriptet skapar en virtuell dator från en ögonblicksbild av en OS-disk.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.sh "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en hanterad disk, en virtuell dator och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | Hämtar en ögonblicksbild med namnet på ögonblicksbilden och resursgruppens namn. ID-egenskapen för det returnerade objektet används för att skapa en hanterad disk.  |
| [az disk create](/cli/azure/disk) | Skapar hanterade diskar från en ögonblicksbild med hjälp av ögonblicksbild-ID, namn på disk, lagringstyp och storlek  |
| [az vm create](/cli/azure/vm) | Skapar en virtuell dator med hjälp av en hanterad operativsystemsdisk |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [Dokumentation för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
