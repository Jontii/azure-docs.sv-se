---
title: Skapa åtgärds grupper med Resource Manager-mallar
description: Lär dig hur du skapar en åtgärds grupp med hjälp av en Azure Resource Manager mall.
author: dkamstra
services: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 5c02812d4c97b94667fcddcb275243e7a9b36b29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87321912"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Skapa en åtgärds grupp med en Resource Manager-mall
Den här artikeln visar hur du använder en [Azure Resource Manager-mall](../../azure-resource-manager/templates/template-syntax.md) för att konfigurera åtgärds grupper. Med hjälp av mallar kan du automatiskt konfigurera åtgärds grupper som kan återanvändas i vissa typer av aviseringar. Dessa åtgärds grupper säkerställer att alla rätt parter meddelas när en avisering utlöses.

De grundläggande stegen är:

1. Skapa en mall som en JSON-fil som beskriver hur du skapar åtgärds gruppen.

2. Distribuera mallen med hjälp av [valfri distributions metod](../../azure-resource-manager/templates/deploy-powershell.md).

Först beskriver vi hur du skapar en Resource Manager-mall för en åtgärds grupp där åtgärds definitionerna är hårdkodade i mallen. För det andra beskriver vi hur du skapar en mall som hämtar konfigurations informationen för webhooken som indataparametrar när mallen distribueras.

## <a name="resource-manager-templates-for-an-action-group"></a>Resource Manager-mallar för en åtgärds grupp

Om du vill skapa en åtgärds grupp med hjälp av en Resource Manager-mall skapar du en resurs av typen `Microsoft.Insights/actionGroups` . Fyll sedan i alla relaterade egenskaper. Här följer två exempel på mallar som skapar en åtgärds grupp.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service Name."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>Nästa steg
* Läs mer om [Åtgärds grupper](./action-groups.md).
* Lär dig mer om [aviseringar](alerts-overview.md).
* Lär dig hur du lägger till [aviseringar med hjälp av en Resource Manager-mall](./alerts-activity-log.md).

