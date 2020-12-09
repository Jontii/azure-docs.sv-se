---
title: Distribuera flera instanser av resurser
description: Använd kopierings åtgärd och matriser i en Azure Resource Manager mall (ARM-mall) för att distribuera resurs typen flera gånger.
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 47f3d693b84347973889a6003360d7113c427f4d
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905918"
---
# <a name="resource-iteration-in-arm-templates"></a>Resurs upprepning i ARM-mallar

Den här artikeln visar hur du skapar fler än en instans av en resurs i din Azure Resource Manager-mall (ARM-mall). Genom att lägga till `copy` elementet i avsnittet resurser i mallen kan du dynamiskt ange antalet resurser som ska distribueras. Du behöver inte heller upprepa syntaxen för mallar.

Du kan också använda `copy` med [Egenskaper](copy-properties.md), [variabler](copy-variables.md)och [utdata](copy-outputs.md).

Om du behöver ange om en resurs har distribuerats alls, se [villkors element](conditional-resource-deployment.md).

## <a name="syntax"></a>Syntax

`copy`Elementet har följande allmänna format:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

`name`Egenskapen är ett värde som identifierar slingan. `count`Egenskapen anger antalet iterationer som du vill använda för resurs typen.

Använd- `mode` och- `batchSize` egenskaperna för att ange om resurserna distribueras parallellt eller i följd. Dessa egenskaper beskrivs i [serie eller parallellt](#serial-or-parallel).

## <a name="copy-limits"></a>Kopierings gränser

Antalet får inte överskrida 800.

Antalet får inte vara ett negativt tal. Det kan vara noll om du distribuerar mallen med en senare version av Azure CLI, PowerShell eller REST API. Mer specifikt måste du använda:

* Azure PowerShell **2,6** eller senare
* Azure CLI- **2.0.74** eller senare
* REST API version **2019-05-10** eller senare
* [Länkade distributioner](linked-templates.md) måste använda API version **2019-05-10** eller senare för distributions resurs typen

Tidigare versioner av PowerShell, CLI och REST API stöder inte noll för Count.

Var försiktig med att använda [fullständig läges distribution](deployment-modes.md) med Copy. Om du omdistribuerar med slutfört läge till en resurs grupp raderas alla resurser som inte är angivna i mallen när du har löst kopierings slingen.

## <a name="resource-iteration"></a>Resurs upprepning

I följande exempel skapas antalet lagrings konton som anges i `storageCount` parametern.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {}
}
```

Observera att namnet på varje resurs inkluderar `copyIndex()` funktionen, som returnerar den aktuella iterationen i slingan. `copyIndex()` är nollbaserat. I följande exempel:

```json
"name": "[concat('storage', copyIndex())]",
```

Skapar följande namn:

* storage0
* storage1
* storage2.

Du kan ange ett värde i funktionen för att kompensera värdet för indexet `copyIndex()` . Antalet iterationer har fortfarande angetts i kopierings elementet, men värdet för `copyIndex` motbokas med det angivna värdet. I följande exempel:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Skapar följande namn:

* storage1
* storage2
* storage3

Kopierings åtgärden är användbar när du arbetar med matriser eftersom du kan iterera igenom varje element i matrisen. Använd `length` funktionen på matrisen för att ange antalet iterationer och `copyIndex` för att hämta det aktuella indexet i matrisen.

I följande exempel skapas ett lagrings konto för varje namn som anges i parametern.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

Om du vill returnera värden från de distribuerade resurserna kan du använda [Kopiera i avsnittet utdata](copy-outputs.md).

## <a name="serial-or-parallel"></a>Serie eller parallell

Som standard skapar Resource Manager resurserna parallellt. Den tillämpar ingen gräns för antalet resurser som distribueras parallellt, förutom den totala gränsen på 800-resurser i mallen. Ordningen som de har skapats i är inte garanterad.

Men du kanske vill ange att resurserna distribueras i följd. Till exempel, när du uppdaterar en produktions miljö, kanske du vill sprida uppdateringarna så att bara ett visst nummer uppdateras vid ett tillfälle. Om du vill distribuera mer än en instans av en resurs kan du `mode` ange **seriell** och `batchSize` antalet instanser som ska distribueras i taget. Med seriellt läge skapar Resource Manager ett beroende på tidigare instanser i slingan, så det går inte att starta en batch förrän den föregående batchen har slutförts.

Värdet för `batchSize` får inte överstiga värdet för `count` i kopierings elementet.

Om du till exempel vill distribuera lagrings konton två i taget, använder du:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

`mode`Egenskapen accepterar också **parallell**, vilket är standardvärdet.

## <a name="depend-on-resources-in-a-loop"></a>Är beroende av resurser i en slinga

Du anger att en resurs distribueras efter en annan resurs med hjälp av- `dependsOn` elementet. Om du vill distribuera en resurs som är beroende av resurs samlingen i en slinga, anger du namnet på kopierings slingan i dependsOn-elementet. I följande exempel visas hur du distribuerar tre lagrings konton innan du distribuerar den virtuella datorn. Den fullständiga definitionen av virtuell dator visas inte. Observera att kopierings elementet har namn inställt på `storagecopy` och dependsOn-elementet för den virtuella datorn också är inställt på `storagecopy` .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="iteration-for-a-child-resource"></a>Iteration för en underordnad resurs

Du kan inte använda en kopierings slinga för en underordnad resurs. Om du vill skapa mer än en instans av en resurs som du vanligt vis definierar som kapslad i en annan resurs måste du i stället skapa den resursen som en resurs på den översta nivån. Du definierar relationen med den överordnade resursen genom egenskaperna typ och namn.

Anta till exempel att du vanligt vis definierar en data uppsättning som en underordnad resurs i en data fabrik.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Om du vill skapa mer än en data uppsättning flyttar du den utanför data fabriken. Data uppsättningen måste vara på samma nivå som data fabriken, men den är fortfarande en underordnad resurs till data fabriken. Du bevarar relationen mellan data uppsättningen och data fabriken genom egenskaperna typ och namn. Eftersom typen inte längre kan härledas från dess position i mallen måste du ange den fullständigt kvalificerade typen i formatet: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}` .

Om du vill upprätta en överordnad/underordnad relation med en instans av data fabriken anger du ett namn för den data uppsättning som innehåller namnet på den överordnade resursen. Använd formatet `{parent-resource-name}/{child-resource-name}`.

I följande exempel visas implementeringen:

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

## <a name="example-templates"></a>Exempel på mallar

I följande exempel visas vanliga scenarier för att skapa mer än en instans av en resurs eller egenskap.

|Mall  |Beskrivning  |
|---------|---------|
|[Kopiera lagring](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Distribuerar fler än ett lagrings konto med ett index nummer i namnet. |
|[Lagring av serie kopia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Distribuerar flera lagrings konton en i taget. Namnet innehåller index numret. |
|[Kopiera lagring med matris](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Distribuerar flera lagrings konton. Namnet innehåller ett värde från en matris. |
|[VM-distribution med ett variabel antal data diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Distribuerar flera data diskar till en virtuell dator. |
|[Flera säkerhets regler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Distribuerar flera säkerhets regler till en nätverks säkerhets grupp. Den skapar säkerhets reglerna från en parameter. För-parametern, se [flera NSG-parameter fil](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Nästa steg

* Information om hur du går igenom självstudierna finns i [Självstudier: skapa flera resurs instanser med ARM-mallar](template-tutorial-create-multiple-instances.md).
* För andra användnings områden av elementet Copy, se:
  * [Egenskaps upprepning i ARM-mallar](copy-properties.md)
  * [Variabel iteration i ARM-mallar](copy-variables.md)
  * [Utdata iteration i ARM-mallar](copy-outputs.md)
* Information om hur du använder kopiera med kapslade mallar finns i [använda kopiera](linked-templates.md#using-copy).
* Om du vill lära dig mer om avsnitten i en mall, se [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
* Information om hur du distribuerar din mall finns i [distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md).
