---
title: Exempel på Resource Manager-mallar för åtgärds grupper
description: Exempel Azure Resource Manager mallar för att distribuera Azure Monitor åtgärds grupper.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 12/03/2020
ms.openlocfilehash: 55b56d9eeb2c7b05af86c42d12e82aef6d806b0a
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600968"
---
# <a name="resource-manager-template-samples-for-action-groups-in-azure-monitor"></a>Exempel på Resource Manager-mallar för åtgärds grupper i Azure Monitor
Den här artikeln innehåller exempel [Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md) för att skapa [Åtgärds grupper](../platform/action-groups.md) i Azure Monitor. Varje exempel innehåller en mallfil och en parameter fil med exempel värden som du kan använda för mallen.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="create-an-action-group"></a>Skapa en åtgärds grupp
I följande exempel skapas en åtgärds grupp.


### <a name="template-file"></a>Mallfil

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name within the resource group for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name up to 12 characters for the Action group."
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
            "emailAddress": "devops@contoso.com",
            "useCommonAlertSchema": true
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com",
            "useCommonAlertSchema": true
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1",
            "useCommonAlertSchema": true
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2",
            "useCommonAlertSchema": true
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

### <a name="parameter-file"></a>Parameter fil

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "actionGroupName": {
        "value": "My Action Group"
      },
      "actionGroupShortName": {
        "value": "mygroup"
      }
  }
}
```



## <a name="next-steps"></a>Nästa steg

* [Hämta andra exempel mallar för Azure Monitor](resource-manager-samples.md).
* [Läs mer om åtgärds grupper](../platform/action-groups.md).

