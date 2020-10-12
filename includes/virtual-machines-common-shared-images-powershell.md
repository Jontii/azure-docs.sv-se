---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 10c2b447a3f174afe93f56084827756d24d982cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "82792198"
---
## <a name="create-an-image-gallery"></a>Skapa ett bild galleri 

Ett avbildnings galleri är den primära resurs som används för att aktivera avbildnings delning. Tillåtna tecken för Galleri namn är versaler eller gemener, siffror, punkter och punkter. Galleri namnet får inte innehålla bindestreck. Galleri namn måste vara unika inom din prenumeration. 

Skapa ett bild galleri med [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). I följande exempel skapas ett galleri med namnet min *Galleri* i resurs gruppen *myGalleryRG* .

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   

## <a name="share-the-gallery"></a>Dela galleriet

Vi rekommenderar att du delar åtkomst på avbildnings Galleri nivån. Använd en e-postadress och cmdleten [Get-AzADUser](/powershell/module/az.resources/get-azaduser) för att hämta objekt-ID: t för användaren och Använd sedan [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) för att ge dem åtkomst till galleriet. Ersätt e-postmeddelandet alinne_montes@contoso.com i det här exemplet med din egen information.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName

```

