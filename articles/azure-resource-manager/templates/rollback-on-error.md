---
title: Det gick inte att återställa till en lyckad distribution
description: Ange att en misslyckad distribution ska återställas till en lyckad distribution.
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 206c794996f58a4c5b6982c551ae50128ed4f5eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "79460151"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Det gick inte att återställa vid lyckad distribution

När en distribution Miss lyckas kan du automatiskt distribuera om en tidigare distribuerad distribution från din distributions historik. Den här funktionen är användbar om du har ett känt bra tillstånd för distribution av infrastrukturen och vill återställa till det här läget. Det finns ett antal varningar och begränsningar:

- Omdistributionen körs exakt eftersom den kördes tidigare med samma parametrar. Du kan inte ändra parametrarna.
- Den tidigare distributionen körs med [slutfört läge](./deployment-modes.md#complete-mode). Alla resurser som inte ingår i den tidigare distributionen tas bort och alla resurspooler anges till sitt tidigare tillstånd. Se till att du förstår [distributions lägena](./deployment-modes.md)fullständigt.
- Omdistributionen påverkar endast resurserna, alla data ändringar påverkas inte.
- Du kan bara använda den här funktionen med distributioner av resurs grupper, inte på prenumerationer eller på hanterings grupps nivå. Mer information om distribution av prenumerations nivåer finns i [skapa resurs grupper och resurser på prenumerations nivå](./deploy-to-subscription.md).
- Du kan bara använda det här alternativet med distributioner på rotnivå. Distributioner från en kapslad mall är inte tillgängliga för omdistribution.

Om du vill använda det här alternativet måste distributionerna ha unika namn så att de kan identifieras i historiken. Om du inte har unika namn kan den aktuella misslyckade distributionen skriva över den tidigare lyckade distributionen i historiken.

## <a name="powershell"></a>PowerShell

Om du vill distribuera om den senaste lyckade distributionen lägger du till `-RollbackToLastDeployment` parametern som en flagga.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Om du vill distribuera om en speciell distribution använder du `-RollBackDeploymentName` parametern och anger namnet på distributionen. Den angivna distributionen måste ha slutförts.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure CLI

Om du vill distribuera om den senaste lyckade distributionen lägger du till `--rollback-on-error` parametern som en flagga.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Om du vill distribuera om en speciell distribution använder du `--rollback-on-error` parametern och anger namnet på distributionen. Den angivna distributionen måste ha slutförts.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST-API

Om du vill distribuera om den senaste lyckade distributionen om den aktuella distributionen Miss lyckas, använder du:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Om du vill distribuera om en angiven distribution, om den aktuella distributionen Miss lyckas, använder du:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

Den angivna distributionen måste ha slutförts.

## <a name="next-steps"></a>Nästa steg

- För att på ett säkert sätt distribuera tjänsten till mer än en region, se [Azure Deployment Manager](deployment-manager-overview.md).
- Information om hur du hanterar resurser som finns i resurs gruppen men som inte har definierats i mallen finns i [Azure Resource Manager distributions lägen](deployment-modes.md).
- Information om hur du definierar parametrar i din mall finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](template-syntax.md).
- Information om hur du distribuerar en mall som kräver en SAS-token finns i [distribuera privat mall med SAS-token](secure-template-with-sas-token.md).
