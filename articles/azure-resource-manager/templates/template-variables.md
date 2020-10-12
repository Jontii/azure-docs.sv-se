---
title: Variabler i mallar
description: Beskriver hur du definierar variabler i en Azure Resource Manager mall.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: cf135959d30702ea58b7a1d4fdd82625a39245d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "75483822"
---
# <a name="variables-in-azure-resource-manager-template"></a>Variabler i Azure Resource Manager-mallar

I den här artikeln beskrivs hur du definierar och använder variabler i din Azure Resource Manager-mall. Du kan använda variabler för att förenkla din mall. I stället för att upprepa komplexa uttryck i hela mallen definierar du en variabel som innehåller det komplexa uttrycket. Sedan kan du referera till variabeln efter behov i hela mallen.

Resource Manager löser variabler innan distributions åtgärderna påbörjas. Oavsett var variabeln används i mallen ersätter Resource Manager den med det matchade värdet.

## <a name="define-variable"></a>Definiera variabel

I följande exempel visas en variabel definition. Det skapar ett sträng värde för ett lagrings konto namn. Den använder flera mallar för att hämta ett parameter värde och sammanfogar dem till en unik sträng.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Du kan inte använda funktionen [Reference](template-functions-resource.md#reference) eller någon av [list](template-functions-resource.md#list) funktionerna i avsnittet Variables. Dessa funktioner hämtar körnings status för en resurs och kan inte utföras före distributionen när variablerna är lösta.

## <a name="use-variable"></a>Använd variabel

I mallen refererar du till värdet för parametern med hjälp av funktionen [variabler](template-functions-deployment.md#variables) . I följande exempel visas hur du använder variabeln för en resurs egenskap.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="configuration-variables"></a>Variabler för konfiguration

Du kan definiera variabler som innehåller relaterade värden för att konfigurera en miljö. Du definierar variabeln som ett objekt med värdena. I följande exempel visas ett objekt som innehåller värden för två miljöer – **test** och **Prod**.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

I parametrar skapar du ett värde som anger vilka konfigurations värden som ska användas.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Använd variabeln och parametern tillsammans för att hämta inställningarna för den angivna miljön.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>Exempel på mallar

Följande exempel visar scenarier för att använda variabler.

|Mall  |Beskrivning  |
|---------|---------|
| [variabel definitioner](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Visar de olika typerna av variabler. Mallen distribuerar inga resurser. Den skapar variabel värden och returnerar dessa värden. |
| [konfigurations variabel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Visar användningen av en variabel som definierar konfigurations värden. Mallen distribuerar inga resurser. Den skapar variabel värden och returnerar dessa värden. |
| [nätverks säkerhets regler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) och [parameter fil](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Skapar en matris i rätt format för att tilldela säkerhets regler till en nätverks säkerhets grupp. |

## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga egenskaper för variabler finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](template-syntax.md).
* Rekommendationer om hur du skapar variabler finns i [metod tips – variabler](template-best-practices.md#variables).
