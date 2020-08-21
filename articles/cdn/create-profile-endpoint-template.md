---
title: 'Snabb start: skapa en profil och en slut punkt för Resource Manager-mall'
titleSuffix: Azure Content Delivery Network
description: Lär dig hur du skapar en Azure Content Delivery Network-profil och en slut punkt till en Resource Manager-mall
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: aca3c9c52260d3ea2ab8def37566377fa67e712d
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705936"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---arm-template"></a>Snabb start: skapa en mall för Azure CDN profil och slut punkts ARM

Kom igång med Azure Content Delivery Network (CDN) med hjälp av en Azure Resource Manager-mall (ARM-mall). Mallen distribuerar en profil och en slut punkt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-cdn-with-custom-origin/).

Den här mallen har kon figurer ATS för att skapa en:

* Profil
* Slutpunkt

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json":::

En Azure-resurs har definierats i mallen:

* **[Microsoft. CDN/profiler](https://docs.microsoft.com/azure/templates/microsoft.cdn/profiles)**

## <a name="deploy-the-template"></a>Distribuera mallen

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az group deployment create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

### <a name="portal"></a>Portalen

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **resurs grupper** i det vänstra fönstret.

3. Välj den resurs grupp som du skapade i föregående avsnitt. Standard resurs grupps namnet är **myResourceGroupCDN**

4. Kontrol lera att följande resurser har skapats i resurs gruppen:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Azure CDN resurs grupp" border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="azure-cli"></a>Azure CLI

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) för att ta bort resurs gruppen och alla resurser som ingår i.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

### <a name="powershell"></a>PowerShell

När de inte längre behövs kan du använda kommandot [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) för att ta bort resurs gruppen och alla resurser som ingår i.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

### <a name="portal"></a>Portalen

Ta bort resurs gruppen, CDN-profilen och alla relaterade resurser när de inte längre behövs. Välj den resurs grupps **myResourceGroupCDN** som innehåller CDN-profilen och slut punkten och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en:

* CDN-profil
* Slutpunkt

Om du vill veta mer om Azure CDN och Azure Resource Manager fortsätter du till artiklarna nedan.

* Läs en [Översikt över Azure CDN](cdn-overview.md)
* Läs mer om [Azure Resource Manager](../azure-resource-manager/management/overview.md)