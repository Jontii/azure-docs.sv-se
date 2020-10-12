---
title: Skapa en virtuell dator från en generaliserad avbildning med hjälp av Azure CLI
description: Skapa en virtuell dator från en generaliserad avbildnings version med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 090b041c605c2328add8b46a97b6f151bae268c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501090"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-cli"></a>Skapa en virtuell dator från en generaliserad avbildnings version med hjälp av CLI

Skapa en virtuell dator från en [generaliserad avbildnings version](./linux/shared-image-galleries.md#generalized-and-specialized-images) som lagras i ett delat avbildnings Galleri. Om du vill skapa en virtuell dator med hjälp av en specialiserad avbildning, se [skapa en virtuell dator från en specialiserad avbildning](vm-specialized-image-version-powershell.md). 


## <a name="get-the-image-id"></a>Hämta bild-ID

Visa en lista över bild definitionerna i ett galleri med hjälp av [AZ sig-bild definitions listan](/cli/azure/sig/image-definition#az-sig-image-definition-list) för att se namn och ID för definitionerna.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az-vm-create). Om du vill använda den senaste versionen av avbildningen anger du `--image` ID för avbildnings definitionen. 

Ersätt resurs namn efter behov i det här exemplet. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

Du kan också använda en speciell version med hjälp av avbildningens versions-ID för `--image` parametern. Om du till exempel vill använda bild versionen *1.0.0* typ: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` .

## <a name="next-steps"></a>Nästa steg

[Azure Image Builder (för hands version)](./linux/image-builder-overview.md) kan hjälpa dig att automatisera avbildnings versionen, du kan även använda den för att uppdatera och [skapa en ny avbildnings version från en befintlig avbildnings version](./linux/image-builder-gallery-update-image-version.md). 
