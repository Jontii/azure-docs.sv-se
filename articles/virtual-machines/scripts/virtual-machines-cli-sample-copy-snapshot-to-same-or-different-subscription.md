---
title: Kopiera ögonblicks bild av hanterade diskar till ett exempel på en prenumerations-CLI
description: Skript exempel för Azure CLI – kopiera (eller flytta) ögonblicks bild av en hanterad disk till samma eller en annan prenumeration med CLI
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 52efdb91328b5d9349eb3724c4a146348e4a975a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978585"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Kopiera en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration med CLI

Det här skriptet kopierar en ögonblicksbild av en hanterad disk till samma eller en annan prenumeration. Använd det här skriptet för följande scenarier:

1. Migrera en ögonblicks bild i Premium Storage (Premium_LRS) till standard lagring (Standard_LRS eller Standard_ZRS) för att minska kostnaderna.
1. Migrera en ögonblicks bild från lokalt redundant lagring (Premium_LRS Standard_LRS) till zonens redundanta lagring (Standard_ZRS) för att dra nytta av ZRS-lagringens högre tillförlitlighet.
1. Flytta en ögonblicks bild till en annan prenumeration i samma region för längre kvarhållning.

> [!NOTE]
> Båda prenumerationerna måste finnas under samma klient organisation

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa en ögonblicksbild i målprenumerationen med källögonblicksbildens ID. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | Hämtar alla egenskaper för en ögonblicksbild med hjälp av ögonblicksbildens namn och resursgruppsegenskaper. ID-egenskapen används för att kopiera ögonblicksbilden till en annan prenumeration.  |
| [az snapshot create](/cli/azure/snapshot) | Kopierar en ögonblicksbild genom att skapa en ögonblicksbild i en annan prenumeration med ID och namn på den överordnade ögonblicksbilden.  |

## <a name="next-steps"></a>Nästa steg

[Skapa en virtuell dator från en ögonblicksbild](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer och hanterade diskar finns i [Dokumentation för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
