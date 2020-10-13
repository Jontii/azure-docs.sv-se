---
title: Lägga till eller ta bort Azure Role-tilldelningar med Azure CLI – Azure RBAC
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänstens huvud namn eller hanterade identiteter med Azure CLI och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2020
ms.author: rolyon
ms.custom: contperfq1
ms.openlocfilehash: 16ead03af14da70b5aaedc21118488c6dd3012c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91597648"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-cli"></a>Lägga till eller ta bort Azure-rolltilldelningar med hjälp av Azure CLI

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Den här artikeln beskriver hur du tilldelar roller med hjälp av Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

Om du vill lägga till eller ta bort roll tilldelningar måste du ha:

- `Microsoft.Authorization/roleAssignments/write` och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)
- [Bash i Azure Cloud Shell](/azure/cloud-shell/overview) eller [Azure CLI](/cli/azure)

## <a name="steps-to-add-a-role-assignment"></a>Steg för tillägg av en rolltilldelning

I Azure RBAC för att bevilja åtkomst lägger du till en roll tilldelning. En rolltilldelning består av tre delar: säkerhetsobjekt, rolldefinition och omfång. Följ dessa steg om du vill lägga till en roll tilldelning.

### <a name="step-1-determine-who-needs-access"></a>Steg 1: Bestäm vem som behöver åtkomst

Du kan tilldela en roll till en användare, grupp, tjänstens huvud namn eller en hanterad identitet. Om du vill lägga till en roll tilldelning kan du behöva ange det unika ID: t för objektet. ID: t har formatet: `11111111-1111-1111-1111-111111111111` . Du kan hämta ID: t med hjälp av Azure Portal eller Azure CLI.

**Användare**

För en Azure AD-användare hämtar du User Principal Name, till exempel *patlong \@ contoso.com* eller User Object ID. För att hämta objekt-ID kan du använda [AZ AD User show](/cli/azure/ad/user#az_ad_user_show).

```azurecli
az ad user show --id "{principalName}" --query "objectId" --output tsv
```

**Grupper**

För en Azure AD-grupp behöver du grupp objekt-ID: t. För att hämta objekt-ID kan du använda [AZ AD Group show](/cli/azure/ad/group#az_ad_group_show) eller [AZ AD Group List](/cli/azure/ad/group#az_ad_group_list).

```azurecli
az ad group show --group "{groupName}" --query "objectId" --output tsv
```

**Tjänstens huvud namn**

För ett Azure AD-tjänstens huvud namn (identitet som används av ett program) behöver du ett objekt-ID för tjänstens huvud namn. Du kan hämta objekt-ID genom att använda [AZ AD SP List](/cli/azure/ad/sp#az_ad_sp_list). För ett huvud namn för tjänsten använder du objekt-ID: t och **inte** program-ID: t.

```azurecli
az ad sp list --all --query "[].{displayName:displayName, objectId:objectId}" --output tsv
az ad sp list --display-name "{displayName}"
```

**Hanterade identiteter**

För en systemtilldelad eller användardefinierad hanterad identitet behöver du objekt-ID: t. Du kan hämta objekt-ID genom att använda [AZ AD SP List](/cli/azure/ad/sp#az_ad_sp_list).

```azurecli
az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
```

Om du bara vill visa användarspecifika hanterade identiteter kan du använda [AZ Identity List](/cli/azure/identity#az_identity_list).

```azurecli
az identity list
```
    
### <a name="step-2-find-the-appropriate-role"></a>Steg 2: hitta rätt roll

Behörigheter grupperas tillsammans i roller. Du kan välja från en lista över flera [inbyggda Azure-roller](built-in-roles.md) eller så kan du använda dina egna anpassade roller. Vi rekommenderar att du beviljar åtkomst med den minsta behörighet som krävs, så undvik att tilldela en bredare roll.

Om du vill visa en lista över roller och hämta det unika roll-ID: t kan du använda [AZ roll definitions lista](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition list --query "[].{name:name, roleType:roleType, roleName:roleName}" --output tsv
```

Så här visar du information om en viss roll.

```azurecli
az role definition list --name "{roleName}"
```

Mer information finns i [lista över Azure-roll definitioner](role-definitions-list.md#azure-cli).
 
### <a name="step-3-identify-the-needed-scope"></a>Steg 3: identifiera omfattningen som krävs

Azure tillhandahåller fyra nivåer av omfång: resurs, [resurs grupp](../azure-resource-manager/management/overview.md#resource-groups), prenumeration och [hanterings grupp](../governance/management-groups/overview.md). Vi rekommenderar att du beviljar åtkomst med den minsta behörighet som krävs, så undvik att tilldela en roll i ett större omfattning. Mer information om omfång finns i [förstå omfattning](scope-overview.md).

**Resursomfång**

För resurs omfång behöver du resurs-ID för resursen. Du hittar resurs-ID genom att titta på resursens egenskaper i Azure Portal. Ett resurs-ID har följande format.

```
/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}
```

**Omfånget för resursgrupp**

För resurs grupp omfånget behöver du namnet på resurs gruppen. Du hittar namnet på sidan **resurs grupper** i Azure Portal eller så kan du använda [AZ Group List](/cli/azure/group#az_group_list).

```azurecli
az group list --query "[].{name:name}" --output tsv
```

**Prenumerations omfång** 

Du behöver prenumerations-ID för prenumerations omfång. Du hittar ID på sidan **prenumerationer** i Azure Portal eller så kan du använda [konto listan i AZ](/cli/azure/account#az_account_list).

```azurecli
az account list --query "[].{name:name, id:id}" --output tsv
```

**Hanterings gruppens omfattning** 

För hanterings gruppens omfattning behöver du hanterings gruppens namn. Du hittar namnet på sidan **hanterings grupper** i Azure Portal eller så kan du använda [AZ Account Management-Group List](/cli/azure/account/management-group#az_account_management_group_list).

```azurecli
az account management-group list --query "[].{name:name, id:id}" --output tsv
```
    
### <a name="step-4-add-role-assignment"></a>Steg 4: Lägg till roll tilldelning

Om du vill lägga till en roll tilldelning använder du kommandot [AZ roll tilldelning skapa](/cli/azure/role/assignment#az_role_assignment_create) . Beroende på omfattningen har kommandot vanligt vis något av följande format.

**Resursomfång**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}"
```

**Omfånget för resursgrupp**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}"
```

**Prenumerations omfång** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--subscription "{subscriptionNameOrId}"
```

**Hanterings gruppens omfattning** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/providers/Microsoft.Management/managementGroups/{managementGroupName}"
``` 

Följande visar ett exempel på utdata när du tilldelar rollen [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) till en användare i ett resurs grupps omfång.

```azurecli
{
  "canDelegate": null,
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
  "name": "{roleAssignmentId}",
  "principalId": "{principalId}",
  "principalType": "User",
  "resourceGroup": "{resourceGroupName}",
  "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "type": "Microsoft.Authorization/roleAssignments"
}
```
    
## <a name="add-role-assignment-examples"></a>Lägg till Roll tilldelnings exempel

### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>Lägg till roll tilldelning för en angiven resurs omfattning för BLOB container

Tilldelar rollen [Storage BLOB data Contributor](built-in-roles.md#storage-blob-data-contributor) till ett huvud namn för tjänsten med objekt-ID *55555555-5555-5555-5555-555555555555* i en resurs omfattning för en BLOB-behållare med namnet *BLOB-container-01*.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"
```

### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Lägg till roll tilldelning för alla BLOB-behållare i ett lagrings konto resurs omfång

Tilldelar rollen [Storage BLOB data Contributor](built-in-roles.md#storage-blob-data-contributor) till ett huvud namn för tjänsten med objekt-ID *55555555-5555-5555-5555-555555555555* i en resurs omfattning för ett lagrings konto med namnet *storage12345*.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"
```

### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Lägg till roll tilldelning för en grupp i ett angivet virtuellt nätverks resurs omfång

Tilldelar rollen för [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) till den *Ann Mack* -gruppgruppen med ID 22222222-2222-2222-2222-222222222222 vid en resurs omfattning för ett virtuellt nätverk med namnet *Pharma-Sales-Project-Network*.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Virtual Machine Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network"
```

### <a name="add-role-assignment-for-a-user-at-a-resource-group-scope"></a>Lägg till roll tilldelning för en användare i ett resurs grupps omfång

Tilldelar rollen [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) till *patlong \@ contoso.com* -användare i resurs grupps omfånget *Pharma-Sales* .

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Lägg till roll tilldelning för en användare med hjälp av det unika roll-ID: t i en resurs grupp omfånget

Det finns ett par gånger när ett roll namn kan ändras, till exempel:

- Du använder din egen anpassade roll och du bestämmer dig för att ändra namnet.
- Du använder en förhands gransknings roll som har **(förhands granskning)** i namnet. När rollen släpps får rollen ett nytt namn.

Även om en roll får ett nytt namn ändras inte roll-ID: t. Om du använder skript eller automatisering för att skapa roll tilldelningar, är det en bra idé att använda det unika roll-ID: t i stället för roll namnet. Därför är skripten mer sannolika om du byter namn på en roll.

I följande exempel tilldelas rollen [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) till *patlong \@ contoso.com* -användaren i resurs grupps omfånget *Pharma-Sales* .

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "9980e02c-c2be-4d73-94e8-173b1dc7cf3c" \
--resource-group "pharma-sales"
```

### <a name="add-role-assignment-for-all-blob-containers-at-a-resource-group-scope"></a>Lägg till roll tilldelning för alla BLOB-behållare i ett resurs grupps omfång

Tilldelar rollen [Storage BLOB data Contributor](built-in-roles.md#storage-blob-data-contributor) till ett huvud namn för tjänsten med objekt-ID *55555555-5555-5555-5555-555555555555* i resurs grupps omfånget *exempel-Storage-RG* .

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--resource-group "Example-Storage-rg"
```

Alternativt kan du ange den fullständigt kvalificerade resurs gruppen med `--scope` parametern:

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg"
```

### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>Lägg till roll tilldelning för ett program i en resurs grupp omfånget

Tilldelar rollen [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) till ett program med tjänstens huvud OBJEKTS-ID 44444444-4444-4444-4444-444444444444 i resurs grupps omfånget *Pharma-Sales* .

```azurecli
az role assignment create --assignee "44444444-4444-4444-4444-444444444444" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

### <a name="add-role-assignment-for-a-new-service-principal-at-a-resource-group-scope"></a>Lägg till roll tilldelning för ett nytt huvud namn för tjänsten i ett resurs grupps omfång

Om du skapar ett nytt huvud namn för tjänsten och sedan omedelbart försöker tilldela en roll till tjänstens huvud namn kan roll tilldelningen inte utföras i vissa fall. Om du till exempel använder ett skript för att skapa en ny hanterad identitet och sedan försöker tilldela en roll till tjänstens huvud namn, kan roll tilldelningen Miss förvänta. Orsaken till det här felet är förmodligen en fördröjning i replikeringen. Tjänstens huvud namn skapas i en region. roll tilldelningen kan dock inträffa i en annan region som ännu inte har replikerat tjänstens huvud namn. För att åtgärda det här scenariot bör du ange typ av huvud konto när du skapar roll tilldelningen.

Om du vill lägga till en roll tilldelning använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az_role_assignment_create), anger ett värde för `--assignee-object-id` och anger sedan `--assignee-principal-type` till `ServicePrincipal` .

```azurecli
az role assignment create --assignee-object-id "{assigneeObjectId}" \
--assignee-principal-type "{assigneePrincipalType}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}" \
--scope "/subscriptions/{subscriptionId}"
```

I följande exempel tilldelas rollen [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) till *MSI-test* -hanterad identitet i resurs grupps omfånget *Pharma-Sales* :

```azurecli
az role assignment create --assignee-object-id "33333333-3333-3333-3333-333333333333" \
--assignee-principal-type "ServicePrincipal" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>Lägg till roll tilldelning för en användare med ett prenumerations omfång

Tilldelar [läsaren](built-in-roles.md#reader) rollen till *annm \@ example.com* -användaren i ett prenumerations omfång.

```azurecli
az role assignment create --assignee "annm@example.com" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

### <a name="add-role-assignment-for-a-group-at-a-subscription-scope"></a>Lägg till roll tilldelning för en grupp i ett prenumerations omfång

Tilldelar [läsaren](built-in-roles.md#reader) rollen till den *Ann Mack team* gruppen med ID 22222222-2222-2222-2222-222222222222 i ett prenumerations omfång.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

### <a name="add-role-assignment-for-all-blob-containers-at-a-subscription-scope"></a>Lägg till roll tilldelning för alla BLOB-behållare i ett prenumerations omfång

Tilldelar rollen [Storage BLOB data Reader](built-in-roles.md#storage-blob-data-reader) till *Alain \@ example.com* -användaren i ett prenumerations omfång.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Storage Blob Data Reader" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>Lägg till roll tilldelning för en användare i en hanterings grupps omfattning

Tilldelar rollen för [fakturerings läsare](built-in-roles.md#billing-reader) till *Alain \@ example.com* -användaren i ett hanterings grupps omfång.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="remove-role-assignment"></a>Ta bort roll tilldelning

För att ta bort åtkomst i Azure RBAC tar du bort en roll tilldelning genom att använda [AZ roll tilldelning ta bort](/cli/azure/role/assignment#az_role_assignment_delete).

I följande exempel tas roll tilldelningen för [virtuell dator deltagare](built-in-roles.md#virtual-machine-contributor) bort från *patlong \@ contoso.com* -användaren på resurs gruppen *Pharma-Sales* :

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

Tar bort rollen [läsare](built-in-roles.md#reader) från *Ann Mack team* Group med ID 22222222-2222-2222-2222-222222222222 i ett prenumerations omfång.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

Tar bort rollen för [fakturerings läsare](built-in-roles.md#billing-reader) från *Alain \@ example.com* -användaren i hanterings gruppens omfång.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="next-steps"></a>Nästa steg

- [Visa en lista med Azures roll tilldelningar med Azure CLI](role-assignments-list-cli.md)
- [Använd Azure CLI för att hantera Azure-resurser och resurs grupper](../azure-resource-manager/cli-azure-resource-manager.md)
