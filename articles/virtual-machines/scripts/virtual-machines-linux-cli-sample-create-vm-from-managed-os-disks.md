---
title: Skapa en virtuell dator genom att koppla en hanterad disk som ett OS-exempel på disk-CLI
description: Skriptexempel för Azure CLI – Skapa en virtuell dator genom att ansluta en hanterad diskar och använda den som operativsystemsdisk
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
ms.openlocfilehash: 83f0ea094c26a1ff664ef27729731b77d987e7ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86501508"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-cli"></a>Skapa en virtuell dator med hjälp av en befintlig hanterad operativsystemsdisk med CLI

Det här skriptet skapar en virtuell dator genom att ansluta en befintlig hanterad disk och använda den som operativsystemsdisk. Använd skriptet i dessa scenarion:
* Skapa en virtuell dator utifrån en befintlig hanterad operativsystemsdisk som har kopierats från en hanterad disk i en annan prenumeration
* Skapa en virtuell dator utifrån en befintlig hanterad disk som har skapats med en särskild VHD-fil 
* Skapa en virtuell dator utifrån en befintlig hanterad operativsystemsdisk som har skapats med en ögonblicksbild 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-attach-existing-managed-os-disk/create-vm-attach-existing-managed-os-disk.sh "Create VM from a managed disk")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att hämta egenskaper för hanterade diskar, ansluta en hanterad disk till en ny virtuell dator och skapa en virtuell dator. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [az disk show](/cli/azure/disk) | Hämtar egenskaper för hanterade diskar med hjälp av diskens namn och resursgruppens namn. ID-egenskapen används för att koppla en hanterad disk till en ny virtuell dator |
| [az vm create](/cli/azure/vm) | Skapar en virtuell dator med hjälp av en hanterad operativsystemsdisk |
## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [Dokumentation för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
