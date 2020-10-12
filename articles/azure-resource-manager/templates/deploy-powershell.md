---
title: Distribuera resurser med PowerShell och mall
description: Använd Azure Resource Manager och Azure PowerShell för att distribuera resurser till Azure. Resurserna definieras i en Resource Manager-mall.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: e47de54558962215fe3be78f5b9c45c8d46c54a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372450"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Distribuera resurser med ARM-mallar och Azure PowerShell

Den här artikeln förklarar hur du använder Azure PowerShell med Azure Resource Manager mallar (ARM-mallar) för att distribuera dina resurser till Azure. Om du inte är bekant med principerna för att distribuera och hantera dina Azure-lösningar kan du läsa [Översikt över mall-distribution](overview.md).

## <a name="deployment-scope"></a>Distributions omfång

Du kan rikta distributionen till en resurs grupp, prenumeration, hanterings grupp eller klient organisation. I de flesta fall riktar du distribution till en resurs grupp. Använd prenumerationer, hanterings grupper eller klient distributioner för att tillämpa principer och roll tilldelningar i ett större omfång. När du distribuerar till en prenumeration kan du skapa en resurs grupp och distribuera resurser till den.

Beroende på distributionens omfattning använder du olika kommandon.

* Använd [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)för att distribuera till en **resurs grupp**:

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
  ```

* Om du vill distribuera till en **prenumeration**använder du New-AzSubscriptionDeployment:

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Mer information om distributioner på prenumerations nivå finns i [skapa resurs grupper och resurser på prenumerations nivå](deploy-to-subscription.md).

* Använd [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment)för att distribuera till en **hanterings grupp**.

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Mer information om distributioner på hanterings grupp nivå finns i [Skapa resurser på hanterings grupps nivå](deploy-to-management-group.md).

* Om du vill distribuera till en **klient**använder du [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Mer information om distributioner på klient nivå finns i [Skapa resurser på klient nivå](deploy-to-tenant.md).

I exemplen i den här artikeln används resurs grupps distributioner.

## <a name="prerequisites"></a>Förutsättningar

Du behöver en mall för att distribuera. Om du inte redan har ett kan du hämta och spara en exempel-mall från lagrings platsen för Azure snabb starts [mal len](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) . Det lokala fil namnet som används i den här artikeln **c:\MyTemplates\azuredeploy.jspå**.

Om du inte använder Azure Cloud Shell för att distribuera mallar måste du installera Azure PowerShell och ansluta till Azure:

- **Installera Azure PowerShell-cmdlets på den lokala datorn.** Mer information finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps).
- **Anslut till Azure med hjälp av [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Om du har flera Azure-prenumerationer kan du också behöva köra [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Mer information finns i [använda flera Azure-prenumerationer](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Distribuera lokal mall

I följande exempel skapas en resurs grupp och en mall distribueras från den lokala datorn. Namnet på resurs gruppen får bara innehålla alfanumeriska tecken, punkter, under streck, bindestreck och parenteser. Det kan vara upp till 90 tecken. Det får inte sluta med en punkt.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -Name ExampleDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Det kan ta några minuter att slutföra distributionen.

## <a name="deployment-name"></a>Distributions namn

I föregående exempel hette du distributionen `ExampleDeployment` . Om du inte anger något namn på distributionen används namnet på mallfilen. Om du till exempel distribuerar en mall med namnet `azuredeploy.json` och inte anger ett distributions namn, namnges distributionen `azuredeploy` .

Varje gång du kör en distribution läggs en post till i resurs gruppens distributions historik med distributions namnet. Om du kör en annan distribution och ger den samma namn ersätts den tidigare posten med den aktuella distributionen. Om du vill behålla unika poster i distributions historiken ger du varje distribution ett unikt namn.

Om du vill skapa ett unikt namn kan du tilldela ett slumpmässigt nummer.

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

Eller Lägg till ett datum värde.

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

Om du kör samtidiga distributioner till samma resurs grupp med samma distributions namn slutförs bara den senaste distributionen. Alla distributioner med samma namn som inte har avslut ATS ersätts av den senaste distributionen. Om du till exempel kör en distribution med namnet `newStorage` som distribuerar ett lagrings konto med namnet `storage1` , och samtidigt kör en annan distribution med namnet `newStorage` som distribuerar ett lagrings konto med namnet `storage2` , distribuerar du bara ett lagrings konto. Det resulterande lagrings kontot namnges `storage2` .

Men om du kör en distribution med namnet `newStorage` som distribuerar ett lagrings konto med namnet `storage1` och omedelbart efter att det har slutförts, kör du en annan distribution med namnet `newStorage` som distribuerar ett lagrings konto med namnet `storage2` , så har du två lagrings konton. En är namngiven `storage1` och den andra heter `storage2` . Men du har bara en post i distributions historiken.

När du anger ett unikt namn för varje distribution kan du köra dem samtidigt utan konflikter. Om du kör en distribution med namnet `newStorage1` som distribuerar ett lagrings konto med namnet `storage1` , och samtidigt kör en annan distribution med namnet `newStorage2` som distribuerar ett lagrings konto med namnet `storage2` , har du två lagrings konton och två poster i distributions historiken.

För att undvika konflikter med samtidiga distributioner och för att säkerställa unika poster i distributions historiken ger du varje distribution ett unikt namn.

## <a name="deploy-remote-template"></a>Distribuera fjärran sluten mall

I stället för att lagra ARM-mallar på den lokala datorn kanske du föredrar att lagra dem på en extern plats. Du kan lagra mallar i ett lager för käll kontroll (till exempel GitHub). Du kan också lagra dem i ett Azure Storage-konto för delad åtkomst i din organisation.

Om du vill distribuera en extern mall använder du parametern **TemplateUri** . Använd URI i exemplet för att distribuera exempel mal len från GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

I föregående exempel krävs en offentligt tillgänglig URI för mallen som fungerar i de flesta fall eftersom din mall inte ska innehålla känsliga data. Om du behöver ange känsliga data (som ett administratörs lösen ord) skickar du det värdet som en säker parameter. Men om du inte vill att din mall ska vara offentligt tillgänglig kan du skydda den genom att lagra den i en privat lagrings behållare. Information om hur du distribuerar en mall som kräver en SAS-token (signatur för delad åtkomst) finns i [distribuera privat mall med SAS-token](secure-template-with-sas-token.md). Information om hur du går igenom självstudierna finns i [Självstudier: integrera Azure Key Vault i distribution av arm-mallar](template-tutorial-use-key-vault.md).

## <a name="deploy-template-spec"></a>Specifikation för att distribuera mall

I stället för att distribuera en lokal mall eller en fjärran sluten mall kan du skapa en [mall-specifikation](template-specs.md). Mallen specifikation är en resurs i din Azure-prenumeration som innehåller en ARM-mall. Det gör det enkelt att på ett säkert sätt dela mallen med användare i din organisation. Du använder rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att ge åtkomst till mallen specifikation. Den här funktionen är för närvarande en för hands version.

I följande exempel visas hur du skapar och distribuerar en mall-specifikation. De här kommandona är bara tillgängliga om du har [registrerat dig för för hands versionen](https://aka.ms/templateSpecOnboarding).

Först skapar du mallens specifikation genom att tillhandahålla ARM-mallen.

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -Location westus2 -TemplateJsonFile ./mainTemplate.json
```

Sedan hämtar du ID för mall-specifikation och distribuerar det.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

Mer information finns i [specifikationer för Azure Resource Manager mallar (för hands version)](template-specs.md).

## <a name="preview-changes"></a>Förhandsgranska ändringar

Innan du distribuerar din mall kan du förhandsgranska de ändringar som mallen kommer att göra i din miljö. Använd [åtgärden konsekvens](template-deploy-what-if.md) för att kontrol lera att mallen gör de ändringar som du förväntar dig. Vad händer om även validerar mallen för fel.

## <a name="deploy-from-azure-cloud-shell"></a>Distribuera från Azure Cloud Shell

Du kan använda [Azure Cloud Shell](https://shell.azure.com) för att distribuera mallen. Om du vill distribuera en extern mall anger du mallens URI. Om du vill distribuera en lokal mall måste du först läsa in din mall till lagrings kontot för Cloud Shell. Om du vill ladda upp filer till gränssnittet väljer du Meny ikonen **Ladda upp/ladda ned filer** från fönstret Shell.

Om du vill öppna Cloud Shell bläddrar du till [https://shell.azure.com](https://shell.azure.com) eller väljer **prova** i följande kod avsnitt:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Om du vill klistra in koden i gränssnittet högerklickar du inuti gränssnittet och väljer sedan **Klistra in**.

## <a name="pass-parameter-values"></a>Pass parameter värden

Om du vill skicka parameter värden kan du använda antingen infogade parametrar eller en parameter fil.

### <a name="inline-parameters"></a>Infogade parametrar

Om du vill skicka infogade parametrar anger du namnet på parametern med `New-AzResourceGroupDeployment` kommandot. Om du till exempel vill skicka en sträng och matris till en mall använder du:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Du kan också hämta innehållet i filen och ange innehållet som en infogad parameter.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Att hämta ett parameter värde från en fil är användbart när du behöver ange konfigurations värden. Du kan till exempel ange [värden för Cloud-Init för en virtuell Linux-dator](../../virtual-machines/linux/using-cloud-init.md).

Om du behöver skicka en matris med objekt skapar du hash-tabeller i PowerShell och lägger till dem i en matris. Skicka matrisen som en parameter under distributionen.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Parameter-filer

I stället för att skicka parametrar som infogade värden i skriptet, kan det vara lättare att använda en JSON-fil som innehåller parameter värden. Parameter filen kan vara en lokal fil eller en extern fil med en tillgänglig URI.

Mer information om parameter filen finns i [create Resource Manager parameter File](parameter-files.md).

Om du vill skicka en lokal parameter fil använder du parametern **TemplateParameterFile** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Om du vill skicka en extern parameter fil använder du parametern **TemplateParameterUri** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="next-steps"></a>Nästa steg

- Om du vill återställa till en lyckad distribution när du får ett fel, se [återställa vid fel till lyckad distribution](rollback-on-error.md).
- Information om hur du hanterar resurser som finns i resurs gruppen men som inte har definierats i mallen finns i [Azure Resource Manager distributions lägen](deployment-modes.md).
- Information om hur du definierar parametrar i din mall finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
- Information om hur du distribuerar en mall som kräver en SAS-token finns i [distribuera privat mall med SAS-token](secure-template-with-sas-token.md).
