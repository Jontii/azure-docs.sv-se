---
title: Azure CLI-exempel fönster
description: Azure CLI-exempel fönster
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6c4b707a3cebc1bcdad7c9e14a96d82a8dda2371
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318802"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Azure CLI-exempel för virtuella Windows-datorer

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI som distribuerar virtuella Windows-datorer.

| Skript | Beskrivning |
|---|---|
|**Skapa virtuella datorer**||
| [Skapa en virtuell dator](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell Windows-dator med minimal konfiguration. |
| [Skapa en fullständigt konfigurerad virtuell dator](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en resurs grupp, virtuell dator och alla relaterade resurser.|
| [Skapa virtuella datorer med hög tillgänglighet](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar flera virtuella datorer i en hög tillgänglig och belastningsutjämnad konfiguration. |
| [Skapa en virtuell dator och kör konfigurationsskript](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator och använder Azures anpassade skript tillägg för att installera IIS. |
| [Skapa en virtuell dator och kör DSC-konfiguration](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator och använder tillägget Azure Desired State Configuration (DSC) för att installera IIS. |
|**Hantera lagring**||
| [Skapa en hanterad disk från en virtuell hårddisk](../scripts/virtual-machines-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en hanterad disk från en specialiserad virtuell hård disk som en OS-disk eller från en data-VHD som data disk.  |
| [Skapa en hanterad disk från en ögonblicksbild](../scripts/virtual-machines-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en hanterad disk från en ögonblicks bild. |
| [Kopiera hanterad disk till samma eller en annan prenumeration](../scripts/virtual-machines-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopierar hanterad disk till samma eller en annan prenumeration, men i samma region som den överordnade hanterade disken. 
| [Exportera en ögonblicksbild som en virtuell hårddisk till ett lagringskonto](../scripts/virtual-machines-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporterar en hanterad ögonblicks bild som VHD till ett lagrings konto i en annan region. |
| [Exportera den virtuella hårddisken för en hanterad disk till ett lagringskonto](../scripts/virtual-machines-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporterar den underliggande virtuella hård disken för en hanterad disk till ett lagrings konto i en annan region. |
| [Kopiera ögonblicksbild till samma eller en annan prenumeration](../scripts/virtual-machines-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Kopierar ögonblicks bild till samma eller en annan prenumeration, men i samma region som den överordnade ögonblicks bilden. |
|**Virtuella datorer i nätverket**||
| [Säkra nätverkstrafik mellan virtuella datorer](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar två virtuella datorer, alla relaterade resurser och en intern och extern nätverks säkerhets grupp (NSG). |
|**Säkra virtuella datorer**||
| [Kryptera en virtuell dator och datadiskar](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en Azure Key Vault, en krypterings nyckel och ett huvud namn för tjänsten och krypterar sedan en virtuell dator. |
|**Övervakning av virtuella datorer**||
| [Övervaka en virtuell dator med Azure Monitor](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Skapar en virtuell dator, installerar Log Analytics agent och registrerar den virtuella datorn i en Log Analytics arbets yta.  |
| | |
