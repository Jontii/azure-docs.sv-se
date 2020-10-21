---
title: Skapa ett Azure Key Vault och en valv åtkomst princip med hjälp av ARM-mall
description: Den här artikeln visar hur du skapar Azure Key Vault och valv åtkomst principer med hjälp av en Azure Resource Manager mall.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: 1f62e0b3a40382c911cd07c777c521adb3649c4d
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282318"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>Så här skapar du en Azure Key Vault-och en valv åtkomst princip med hjälp av en Resource Manager-mall

[Azure Key Vault](../general/overview.md) är en moln tjänst som tillhandahåller en säker lagring för hemligheter som nycklar, lösen ord och certifikat. I den här artikeln beskrivs processen för att distribuera en Azure Resource Manager-mall (ARM-mall) för att skapa ett nyckel valv.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här artikeln:

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="create-a-key-vault-resource-manager-template"></a>Skapa en Key Vault Resource Manager-mall

Följande mall visar ett enkelt sätt att skapa ett nyckel valv. Vissa värden anges i mallen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

Mer information om inställningar för Key Vault mal len finns i [referens för Key Vault arm-mall](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Om en mall omdistribueras kommer alla befintliga åtkomst principer i nyckel valvet att åsidosättas. Vi rekommenderar att du fyller i `accessPolicies` egenskapen med befintliga åtkomst principer för att undvika att förlora åtkomst till nyckel valvet. 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>Lägga till en åtkomst princip i en Key Vault Resource Manager-mall

Du kan distribuera åtkomst principer till ett befintligt nyckel valv utan att omdistribuera hela Key Vault-mallen. Följande mall visar ett enkelt sätt att skapa åtkomst principer:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": [parameters('certificatesPermissions')]
            }
          }
        ]
      }
    }
  ]
}

```
Mer information om inställningar för Key Vault mal len finns i [referens för Key Vault arm-mall](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="more-key-vault-resource-manager-templates"></a>Fler Key Vault Resource Manager-mallar

Det finns andra tillgängliga Resource Manager-mallar för Key Vault objekt:

| Hemligheter | Nycklar | Certifikat |
|--|--|--|
|<ul><li>[Snabbstart](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-template)<li>[Referens](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/secrets)|Saknas|Saknas|

Du hittar fler Key Vault mallar här: [Key Vault Resource Manager-referens](https://docs.microsoft.com/azure/templates/microsoft.keyvault/allversions).

## <a name="deploy-the-templates"></a>Distribuera mallarna

Du kan använda Azure Portal för att distribuera de föregående mallarna med hjälp av alternativet för att **bygga en egen mall i redigerings** alternativet som beskrivs här: [distribuera resurser från en anpassad mall](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template).

Du kan också spara föregående mallar till filer och använda följande kommandon:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) och [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med efterföljande snabb starter och självstudier kan du lämna resurserna på plats. När du inte behöver resurserna längre tar du bort resurs gruppen. Om du tar bort gruppen raderas även nyckel valvet och relaterade resurser. Slutför följande steg för att ta bort resurs gruppen med hjälp av Azure CLI eller Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>Resurser

- Läs en [Översikt över Azure Key Vault](../general/overview.md).
- Läs mer om [Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Granska [Azure Key Vault bästa praxis](../general/best-practices.md).

## <a name="next-steps"></a>Nästa steg

- [Säker åtkomst till ett nyckel valv](secure-your-key-vault.md)
- [Autentisera till ett nyckel valv](authentication.md)
- [Utvecklarguide för Azure Key Vault](developers-guide.md)
