---
title: 'Snabb start: skapa en Azure DB för MySQL-ARM-mall'
description: I den här snabb starten får du lära dig hur du skapar en Azure Database for MySQL-server med integrering av virtuella nätverk med hjälp av en Azure Resource Manager mall.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/19/2020
ms.openlocfilehash: 952bfa7182997511ce072c051d872140c3b907cb
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041201"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql-server"></a>Snabb start: Använd en ARM-mall för att skapa en Azure Database for MySQL-Server

Azure Database for MySQL är en hanterad tjänst som du använder för att köra, hantera och skala hög tillgängliga MySQL-databaser i molnet. I den här snabb starten använder du en Azure Resource Manager mall (ARM-mall) för att skapa en Azure Database for MySQL-server med integrering av virtuella nätverk. Du kan skapa servern i Azure Portal, Azure CLI eller Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure** . Mallen öppnas på Azure-portalen.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>Förutsättningar

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/).
* Om du vill köra koden lokalt [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[CLI](#tab/CLI)

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/).
* Om du vill köra koden lokalt kan du köra [Azure CLI](/cli/azure/).

---

## <a name="review-the-template"></a>Granska mallen

Du skapar en Azure Database för MySQL-server med en definierad uppsättning Compute- och Storage-resurser. Läs mer i [Azure Database for MySQL pris nivåer](concepts-pricing-tiers.md). Du skapar servern i en [Azure-resursgrupp](../azure-resource-manager/management/overview.md).

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-managed-mysql-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-mysql-with-vnet/azuredeploy.json":::

Mallen definierar fem Azure-resurser:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/virtualNetworks/undernät**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft. DBforMySQL/servers**](/azure/templates/microsoft.dbformysql/servers)
* [**Microsoft. DBforMySQL/servers/virtualNetworkRules**](/azure/templates/microsoft.dbformysql/servers/virtualnetworkrules)
* [**Microsoft. DBforMySQL/servers/firewallRules**](/azure/templates/microsoft.dbformysql/servers/firewallrules)

Du hittar fler Azure Database for MySQL mal sampel i [galleriet snabb starts mal len](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformysql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Välj följande länk för att distribuera Azure Database for MySQL server mal len i Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

På sidan **distribuera Azure Database for MySQL med VNet** :

1. För **resurs grupp** väljer du **Skapa ny** , anger ett namn för den nya resurs gruppen och väljer **OK** .

2. Om du har skapat en ny resurs grupp väljer du en **plats** för resurs gruppen och den nya servern.

3. Ange ett **Server namn** , **Administratörs inloggning** och **lösen ord för Administratörs inloggning** .

    :::image type="content" source="./media/quickstart-create-mysql-server-database-using-arm-template/deploy-azure-database-for-mysql-with-vnet.png" alt-text="Distribuera till Azure":::

4. Ändra de andra standardinställningarna om du vill:

    * **Prenumeration** : den Azure-prenumeration som du vill använda för servern.
    * **SKU-kapacitet** : vCore-kapaciteten, som kan *vara 2* (standard), *4* , *8* , *16* , *32* eller *64* .
    * **SKU-namn** : SKU-nivåns prefix, SKU-serien och SKU-kapaciteten som ingår i under streck, till exempel *B_Gen5_1* , *GP_Gen5_2* (standard) eller *MO_Gen5_32* .
    * **SKU-storlek MB** : lagrings utrymmet i megabyte för Azure Database for MySQL server (standard *5120* ).
    * **SKU-nivå** : distributions nivån, till *exempel Basic* , *generalpurpose* (standard) eller *MemoryOptimized* .
    * **SKU-familj** : *Gen4* eller *Gen5* (standard), vilket indikerar maskin varu generering för Server distribution.
    * **MySQL-version** : den version av MySQL-server som ska distribueras, till exempel *5,6* eller *5,7* (standard).
    * **Kvarhållning av säkerhets kopierings dagar** : den önskade perioden för kvarhållning av Geo-redundant säkerhets kopia i dagar (standard *7* ).
    * **Geo-redundant säkerhets kopiering** : *aktive rad* eller *inaktive* rad (standard), beroende på krav för geo-haveri beredskap (geo-Dr).
    * **Virtual Network namn** : namnet på det virtuella nätverket (standard *azure_mysql_vnet* ).
    * **Under näts namn** : namnet på under nätet (standard *azure_mysql_subnet* ).
    * **Virtual Network regel namn** : namnet på den virtuella nätverks regel som tillåter under nätet (standard *AllowSubnet* ).
    * **VNet-** adressprefix: adressprefixet för det virtuella nätverket (standard *10.0.0.0/16* ).
    * **Undernätsprefixet** : adressprefixet för under nätet (standard *10.0.0.0/16* ).

5. Läs de allmänna villkoren och välj sedan **Jag accepterar villkoren som anges ovan** .

6. Välj **Köp** .

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Använd följande interaktiva kod för att skapa en ny Azure Database for MySQL server med hjälp av mallen. I koden uppmanas du att ange det nya Server namnet, namnet och platsen för en ny resurs grupp och ett administratörs konto namn och lösen ord.

Om du vill köra koden i Azure Cloud Shell väljer du **testa den** i det övre hörnet av ett kodblock.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

Använd följande interaktiva kod för att skapa en ny Azure Database for MySQL server med hjälp av mallen. I koden uppmanas du att ange det nya Server namnet, namnet och platsen för en ny resurs grupp och ett administratörs konto namn och lösen ord.

Om du vill köra koden i Azure Cloud Shell väljer du **testa den** i det övre hörnet av ett kodblock.

```azurecli-interactive
echo "Enter a name for the new Azure Database for MySQL server:" &&
read serverName &&
echo "Enter a name for the new resource group where the server will exist:" &&
read resourceGroupName &&
echo "Enter an Azure region (for example, centralus) for the resource group:" &&
read location &&
echo "Enter the Azure Database for MySQL server's administrator account name:" &&
read adminUser &&
echo "Enter the administrator password:" &&
read adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill se en översikt över din nya Azure Database for MySQL server:

1. I [Azure Portal](https://portal.azure.com)söker du efter och väljer **Azure Database for MySQL servrar** .

2. I listan databas väljer du den nya servern. **Översikts** sidan för din nya Azure Database for MySQL-server visas.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Kör följande interaktiva kod om du vill visa information om din Azure Database for MySQL-server. Du måste ange namnet på den nya servern.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/servers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

Kör följande interaktiva kod om du vill visa information om din Azure Database for MySQL-server. Du måste ange namnet och resurs gruppen för den nya servern.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/servers"
```

---

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs tar du bort resurs gruppen, som tar bort resurserna i resurs gruppen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)söker du efter och väljer **resurs grupper** .

2. I listan resurs grupp väljer du namnet på din resurs grupp.

3. På sidan **Översikt** i resurs gruppen väljer du **ta bort resurs grupp** .

4. I bekräftelse dialog rutan skriver du namnet på din resurs grupp och väljer sedan **ta bort** .

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Nästa steg

En steg-för-steg-guide som vägleder dig genom processen att skapa en ARM-mall finns i:

> [!div class="nextstepaction"]
> [ Självstudie: skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
