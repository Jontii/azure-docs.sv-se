---
title: Tagga en virtuell dator med hjälp av PowerShell
description: Lär dig mer om att tagga en virtuell dator med hjälp av PowerShell
author: mmccrory
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 3aa40ed396e87b342207fc51576cd28170c7d4e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89322389"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>Tagga en virtuell dator i Azure med hjälp av PowerShell
I den här artikeln beskrivs olika sätt att tagga en virtuell Windows-dator i Azure via distributions modellen för Resource Manager. Taggar är användardefinierade nyckel/värde-par som kan placeras direkt på en resurs eller en resurs grupp. Azure har för närvarande stöd för upp till 50 Taggar per resurs och resurs grupp. Taggar kan placeras på en resurs vid tidpunkten för skapandet eller läggs till i en befintlig resurs. Observera att Taggar endast stöds för resurser som skapats via distributions modellen för Resource Manager. Om du vill tagga en virtuell dator med hjälp av Azure CLI kan du läsa om [att tagga en virtuell dator i Azure med hjälp av Azure CLI](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Tagga med PowerShell
Om du vill skapa, lägga till och ta bort taggar via PowerShell måste du först konfigurera din [PowerShell-miljö med Azure Resource Manager][PowerShell environment with Azure Resource Manager]. När du har slutfört installationen kan du Placera taggar på beräknings-, nätverks-och lagrings resurser vid skapandet eller när resursen har skapats via PowerShell. Den här artikeln fokuserar på att visa/redigera taggar som placerats på Virtual Machines.

 

Börja med att navigera till en virtuell dator via `Get-AzVM` cmdleten.

```azurepowershell
PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"
```

Om den virtuella datorn redan innehåller taggar visas alla Taggar på din resurs:

```json
Tags : {
        "Application": "MyApp1",
        "Created By": "MyName",
        "Department": "MyDepartment",
        "Environment": "Production"
        }
```

Om du vill lägga till taggar via PowerShell kan du använda `Set-AzResource` kommandot. Obs! när du uppdaterar Taggar via PowerShell uppdateras taggarna som helhet. Så om du lägger till en tagg till en resurs som redan har taggar, måste du ta med alla Taggar som du vill placera i resursen. Nedan visas ett exempel på hur du lägger till ytterligare taggar till en resurs via PowerShell-cmdletar.

Den första cmdleten anger alla Taggar som placerats på *MyTestVM* till variabeln *$Tags* , med hjälp av `Get-AzResource` `Tags` egenskapen och.

```azurepowershell
PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags
```

Det andra kommandot visar taggarna för den aktuella variabeln.

```azurepowershell
PS C:\> $tags

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

Det tredje kommandot lägger till en ytterligare tagg till variabeln *$Tags* . Observera användningen av **+=** för att lägga till det nya nyckel/värde-paret i *$Tags* listan.

```azurepowershell
PS C:\> $tags += @{Location="MyLocation"}
```

Det fjärde kommandot anger alla Taggar som definierats i *$Tags* -variabeln till den angivna resursen. I det här fallet är det MyTestVM.

```azurepowershell
PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

Det femte kommandot visar alla Taggar på resursen. Som du kan se definieras *platsen* nu som en tagg med min *plats* som värde.

```azurepowershell
PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```

Om du vill lära dig mer om att tagga genom PowerShell kan du ta en titt på [Azures resurs-cmdletar][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Nästa steg
* Läs mer om att tagga dina Azure-resurser i [Azure Resource Manager översikt][Azure Resource Manager Overview] och [använda taggar för att ordna dina Azure-resurser][Using Tags to organize your Azure Resources].
* För att se hur taggar kan hjälpa dig att hantera din användning av Azure-resurser, se [förstå din Azure-faktura][Understanding your Azure Bill] och [få insikter om din Microsoft Azure resursförbrukning][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/management/manage-resources-powershell.md
[Azure Resource Cmdlets]: /powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
