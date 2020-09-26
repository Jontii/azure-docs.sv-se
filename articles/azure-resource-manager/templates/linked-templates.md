---
title: Länka mallar för distribution
description: 'Beskriver hur du använder länkade mallar i en Azure Resource Manager mall för att skapa en lösning för modulär mall. Visar hur du skickar parameter värden, anger en parameter fil och dynamiskt skapade URL: er.'
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: fb742ed4fabd6630d2d27f5876719e2e2b1a9a4d
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369322"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Använda länkade och nästlade mallar vid distribution av Azure-resurser

Om du vill distribuera komplexa lösningar kan du dela upp mallen i flera relaterade mallar och sedan distribuera dem tillsammans via en huvud-mall. De relaterade mallarna kan vara separata filer eller mall-syntax som är inbäddad i huvud mal len. Den här artikeln använder den **länkade termen mall** för att referera till en separat mallfil som refereras via en länk från huvud mal len. Den använder termen **kapslad mall** för att referera till inbäddad mall-syntax i huvud mal len.

För små till medel stora lösningar är det enklare att förstå och underhålla en enda mall. Du kan se alla resurser och värden i en enda fil. Med länkade mallar i avancerade scenarier kan du dela upp lösningen i riktade komponenter. Du kan enkelt återanvända dessa mallar för andra scenarier.

En själv studie kurs finns i [Självstudier: Skapa länkade Azure Resource Manager mallar](./deployment-tutorial-linked-template.md).

> [!NOTE]
> För länkade eller kapslade mallar kan du bara ange distributions läget som [stegvis](deployment-modes.md). Men huvud mal len kan distribueras i fullständigt läge. Om du distribuerar huvud mal len i det fullständiga läget och den länkade eller kapslade mallen har samma resurs grupp, inkluderas de resurser som distribueras i den länkade eller kapslade mallen i utvärderingen för fullständig läges distribution. Den kombinerade samling resurser som distribueras i huvud mal len och länkade eller kapslade mallar jämförs med de befintliga resurserna i resurs gruppen. Alla resurser som inte ingår i den kombinerade samlingen tas bort.
>
> Om den länkade eller kapslade mallen är riktad mot en annan resurs grupp, använder distributionen stegvist läge.
>

## <a name="nested-template"></a>Kapslad mall

Om du vill kapsla en mall lägger du till en [distributions resurs](/azure/templates/microsoft.resources/deployments) i huvud mal len. I egenskapen **mall** anger du mallens syntax.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

I följande exempel distribueras ett lagrings konto via en kapslad mall.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="expression-evaluation-scope-in-nested-templates"></a>Uttrycks utvärderings omfång i kapslade mallar

När du använder en kapslad mall kan du ange om mall uttryck ska utvärderas inom omfånget för den överordnade mallen eller den kapslade mallen. Omfånget avgör hur parametrar, variabler och funktioner som [resourceGroup](template-functions-resource.md#resourcegroup) och [prenumerationen](template-functions-resource.md#subscription) löses.

Du ställer in omfånget via `expressionEvaluationOptions` egenskapen. Som standard `expressionEvaluationOptions` är egenskapen inställd på `outer` , vilket innebär att den använder den överordnade mallens omfattning. Ställ in värdet på `inner` för att orsaka att uttryck utvärderas inom omfånget för den kapslade mallen.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2019-10-01",
  "name": "nestedTemplate1",
  "properties": {
  "expressionEvaluationOptions": {
    "scope": "inner"
  },
  ...
```

Följande mall visar hur mall uttryck matchas enligt omfånget. Den innehåller en variabel med namnet `exampleVar` som definieras i både den överordnade mallen och den kapslade mallen. Den returnerar variabelns värde.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

Värdet för `exampleVar` ändringar beroende på `scope` egenskapens värde i `expressionEvaluationOptions` . I följande tabell visas resultaten för båda omfattningarna.

| `expressionEvaluationOptions` utrymme | Utdata |
| ----- | ------ |
| innersta | från kapslad mall |
| yttre (eller standard) | från överordnad mall |

I följande exempel distribueras en SQL-Server och en nyckel valvs hemlighet som används för lösen ordet hämtas. Omfånget är inställt på `inner` eftersom det dynamiskt skapar Key Vault-ID: t (se `adminPassword.reference.keyVault` i de yttre mallarna `parameters` ) och skickar det som en parameter till den kapslade mallen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

> [!NOTE]
>
> När omfånget är inställt på `outer` , kan du inte använda `reference` funktionen i avsnittet utdata i en kapslad mall för en resurs som du har distribuerat i den kapslade mallen. Om du vill returnera värdena för en distribuerad resurs i en kapslad mall, kan du antingen använda `inner` omfång eller konvertera den kapslade mallen till en länkad mall.

## <a name="linked-template"></a>Länkad mall

Om du vill länka en mall lägger du till en [distributions resurs](/azure/templates/microsoft.resources/deployments) i huvud mal len. I egenskapen **templateLink** anger du URI för den mall som ska tas med. Följande exempel länkar till en mall som distribuerar ett nytt lagrings konto.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

När du refererar till en länkad mall får värdet `uri` inte vara en lokal fil eller en fil som bara är tillgänglig i det lokala nätverket. Du måste ange ett URI-värde som kan hämtas som **http** eller **https**.

> [!NOTE]
>
> Du kan referera till mallar med parametrar som i slut ända till något som använder **http** eller **https**, till exempel med hjälp av `_artifactsLocation` parametern som så här: `"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]",`

Resource Manager måste kunna komma åt mallen. Ett alternativ är att placera din länkade mall i ett lagrings konto och använda URI: n för objektet.

### <a name="parameters-for-linked-template"></a>Parametrar för länkad mall

Du kan ange parametrar för den länkade mallen antingen i en extern fil eller infogad. När du anger en extern parameter fil använder du egenskapen **parametersLink** :

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2019-10-01",
  "name": "linkedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
      "contentVersion":"1.0.0.0"
    },
    "parametersLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
      "contentVersion":"1.0.0.0"
    }
  }
  }
]
```

Om du vill skicka parameter värden infogade använder **du parameter** egenskapen.

```json
"resources": [
  {
   "type": "Microsoft.Resources/deployments",
   "apiVersion": "2019-10-01",
   "name": "linkedTemplate",
   "properties": {
     "mode": "Incremental",
     "templateLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
      "contentVersion":"1.0.0.0"
     },
     "parameters": {
      "storageAccountName":{"value": "[parameters('storageAccountName')]"}
    }
   }
  }
]
```

Du kan inte använda både infogade parametrar och en länk till en parameter fil. Distributionen Miss lyckas med ett fel när både `parametersLink` och `parameters` har angetts.

## <a name="template-specs"></a>Mallspecifikationer

I stället för att underhålla de länkade mallarna på en tillgänglig slut punkt kan du skapa en [mall-specifikation](template-specs.md) som paketerar huvud mal len och dess länkade mallar i en enda enhet som du kan distribuera. Mallens specifikation är en resurs i din Azure-prenumeration. Det gör det enkelt att på ett säkert sätt dela mallen med användare i din organisation. Du använder rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att ge åtkomst till mallen specifikation. Den här funktionen är för närvarande en för hands version.

Mer information finns i:

- [Självstudie: skapa en mall-specifikation med länkade mallar](./template-specs-create-linked.md).
- [Självstudie: Distribuera en mall specifikation som en länkad mall](./template-specs-deploy-linked-template.md).

## <a name="contentversion"></a>contentVersion

Du behöver inte ange `contentVersion` egenskapen för `templateLink` `parametersLink` egenskapen eller. Om du inte anger någon `contentVersion` , distribueras den aktuella versionen av mallen. Om du anger ett värde för innehålls version måste det matcha versionen i den länkade mallen. annars Miss lyckas distributionen med ett fel.

## <a name="using-variables-to-link-templates"></a>Använda variabler för att länka mallar

I föregående exempel visades hårdkodade URL-värden för mallens länkar. Den här metoden kan fungera för en enkel mall, men den fungerar inte bra för en stor uppsättning modulära mallar. I stället kan du skapa en statisk variabel som lagrar en bas-URL för huvudmallen och sedan dynamiskt skapa URL: er för de länkade mallarna från den bas-URL: en. Fördelen med den här metoden är att du enkelt kan flytta eller förgrena mallen eftersom du bara behöver ändra den statiska variabeln i huvud mal len. Huvud mal len skickar rätt URI: er i den sammanställda mallen.

I följande exempel visas hur du använder en bas-URL för att skapa två URL: er för länkade mallar (**sharedTemplateUrl** och **vmTemplate**).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Du kan också använda [Deployment ()](template-functions-deployment.md#deployment) för att hämta bas-URL: en för den aktuella mallen och använda den för att hämta URL: en för andra mallar på samma plats. Den här metoden är användbar om din malls plats ändras eller om du vill undvika URL: er för hård kodning i mallfilen. Egenskapen templateLink returneras bara vid länkning till en fjärrmall med en URL. Om du använder en lokal mall är den egenskapen inte tillgänglig.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Slutligen använder du variabeln i `uri` egenskapen för en `templateLink` egenskap.

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>Använda kopiera

Om du vill skapa flera instanser av en resurs med en kapslad mall lägger du till kopiera-elementet på nivån i resursen **Microsoft. Resources/distributions** . Eller, om omfånget är Inner, kan du lägga till kopian i den kapslade mallen.

I följande exempel mall visas hur du använder kopiera med en kapslad mall.

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2019-10-01",
  "name": "[concat('nestedTemplate', copyIndex())]",
  // yes, copy works here
  "copy":{
    "name": "storagecopy",
    "count": 2
  },
  "properties": {
    "mode": "Incremental",
    "expressionEvaluationOptions": {
    "scope": "inner"
    },
    "template": {
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(variables('storageName'), copyIndex())]",
      "location": "West US",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2"
      // Copy works here when scope is inner
      // But, when scope is default or outer, you get an error
      //"copy":{
      //  "name": "storagecopy",
      //  "count": 2
      //}
      }
    ]
    }
  }
  }
]
```

## <a name="get-values-from-linked-template"></a>Hämta värden från den länkade mallen

Hämta ett utdata-värde från en länkad mall genom att hämta egenskap svärdet med syntax som: `"[reference('deploymentName').outputs.propertyName.value]"` .

När du hämtar en output-egenskap från en länkad mall får egenskaps namnet inte innehålla något bindestreck.

Följande exempel visar hur du refererar till en länkad mall och hämtar ett utdata-värde. Den länkade mallen returnerar ett enkelt meddelande.  Först den länkade mallen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "greetingMessage": {
      "value": "Hello World",
      "type" : "string"
    }
  }
}
```

Huvud mal len distribuerar den länkade mallen och hämtar det returnerade värdet. Observera att den refererar till distributions resursen efter namn och använder namnet på den egenskap som returnerades av den länkade mallen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
    }
  }
}
```

Precis som med andra resurs typer kan du ange beroenden mellan den länkade mallen och andra resurser. När andra resurser kräver ett värde för utdata från den länkade mallen kontrollerar du att den länkade mallen har distribuerats före dem. Eller, när den länkade mallen är beroende av andra resurser, se till att andra resurser distribueras före den länkade mallen.

I följande exempel visas en mall som distribuerar en offentlig IP-adress och returnerar resurs-ID för Azure-resursen för den offentliga IP-adressen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "eastus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 4
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "resourceID": {
      "type": "string",
      "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
    }
  }
}
```

Om du vill använda den offentliga IP-adressen från föregående mall när du distribuerar en belastningsutjämnare länkar du till mallen och deklarerar ett beroende på `Microsoft.Resources/deployments` resursen. Den offentliga IP-adressen för belastningsutjämnaren anges till värdet output från den länkade mallen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "loadBalancers_name": {
      "defaultValue": "mylb",
      "type": "string"
    },
    "publicIPAddresses_name": {
      "defaultValue": "myip",
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/loadBalancers",
      "apiVersion": "2018-11-01",
      "name": "[parameters('loadBalancers_name')]",
      "location": "eastus",
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "LoadBalancerFrontEnd",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                // this is where the output value from linkedTemplate is used
                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
              }
            }
          }
        ],
        "backendAddressPools": [],
        "loadBalancingRules": [],
        "probes": [],
        "inboundNatRules": [],
        "outboundNatRules": [],
        "inboundNatPools": []
      },
      // This is where the dependency is declared
      "dependsOn": [
        "linkedTemplate"
      ]
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
          "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
        }
      }
    }
  ]
}
```

## <a name="deployment-history"></a>Distributions historik

Resource Manager behandlar varje mall som en separat distribution i distributions historiken. En huvud-mall med tre länkade eller kapslade mallar visas i distributions historiken som:

![Distributions historik](./media/linked-templates/deployment-history.png)

Du kan använda dessa separata poster i historiken för att hämta värden för utdata efter distributionen. Följande mall skapar en offentlig IP-adress och matar ut IP-adressen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "southcentralus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Static",
        "idleTimeoutInMinutes": 4,
        "dnsSettings": {
          "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
        }
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "returnedIPAddress": {
      "type": "string",
      "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
    }
  }
}
```

Följande mall länkar till föregående mall. Den skapar tre offentliga IP-adresser.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "[concat('linkedTemplate', copyIndex())]",
      "copy": {
        "count": 3,
        "name": "ip-loop"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
        "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
        "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
        }
      }
    }
  ]
}
```

Efter distributionen kan du hämta värdena för utdata med följande PowerShell-skript:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Eller Azure CLI-skript i ett bash-gränssnitt:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Skydda en extern mall

Även om den länkade mallen måste vara extern tillgänglig, behöver den inte vara allmänt tillgänglig för allmänheten. Du kan lägga till din mall till ett privat lagrings konto som endast är tillgängligt för lagrings kontots ägare. Sedan skapar du en signatur för signatur för delad åtkomst (SAS) för att aktivera åtkomst under distributionen. Du lägger till denna SAS-token i URI: n för den länkade mallen. Även om token skickas som en säker sträng, loggas URI: n för den länkade mallen, inklusive SAS-token, i distributions åtgärderna. Ange ett förfallo datum för token för att begränsa exponeringen.

Parameter filen kan också begränsas till åtkomst via en SAS-token.

För närvarande kan du inte länka till en mall i ett lagrings konto som ligger bakom en [Azure Storage brand vägg](../../storage/common/storage-network-security.md).

> [!IMPORTANT]
> I stället för att skydda din länkade mall med en SAS-token kan du skapa en [mall-specifikation](template-specs.md). I retemplate-specifikationen lagras huvud mal len och de länkade mallarna som en resurs i din Azure-prenumeration. Du använder Azure RBAC för att bevilja åtkomst till användare som behöver distribuera mallen.

I följande exempel visas hur du skickar en SAS-token vid länkning till en mall:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "containerSasToken": { "type": "securestring" }
  },
  "resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "linkedTemplate",
    "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
      "contentVersion": "1.0.0.0"
    }
    }
  }
  ],
  "outputs": {
  }
}
```

I PowerShell får du en token för behållaren och distribuerar mallarna med följande kommandon. Observera att parametern **containerSasToken** definieras i mallen. Det är inte en parameter i kommandot **New-AzResourceGroupDeployment** .

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

För Azure CLI i ett bash-gränssnitt får du en token för behållaren och distribuera mallarna med följande kod:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
  --resource-group ManageGroup \
  --name storagecontosotemplates \
  --query connectionString)
token=$(az storage container generate-sas \
  --name templates \
  --expiry $expiretime \
  --permissions r \
  --output tsv \
  --connection-string $connection)
url=$(az storage blob url \
  --container-name templates \
  --name parent.json \
  --output tsv \
  --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Exempel på mallar

I följande exempel visas vanliga användnings områden för länkade mallar.

|Huvud mal len  |Länkad mall |Description  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[länkad mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Returnerar en sträng från den länkade mallen. |
|[Load Balancer med offentlig IP-adress](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[länkad mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Returnerar den offentliga IP-adressen från den länkade mallen och anger värdet i belastningsutjämnaren. |
|[Flera IP-adresser](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [länkad mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Skapar flera offentliga IP-adresser i den länkade mallen.  |

## <a name="next-steps"></a>Nästa steg

* Information om hur du går igenom självstudierna finns i [Självstudier: Skapa länkade Azure Resource Manager mallar](./deployment-tutorial-linked-template.md).
* Mer information om hur du definierar distributions ordningen för dina resurser finns i [definiera beroenden i Azure Resource Manager mallar](define-resource-dependency.md).
* Information om hur du definierar en resurs men skapar många instanser av den finns i [skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md).
* Anvisningar om hur du konfigurerar en mall i ett lagrings konto och genererar en SAS-token finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](deploy-powershell.md) eller [distribuera resurser med Resource Manager-mallar och Azure CLI](deploy-cli.md).
