---
title: Skapa hanterings grupper för att organisera resurser – Azure-styrning
description: Lär dig hur du skapar Azure-hanterings grupper för att hantera flera resurser med hjälp av portalen, Azure PowerShell och Azure CLI.
ms.date: 08/10/2020
ms.topic: conceptual
ms.openlocfilehash: 9504679062c9facad60023759b474be1675cb6a8
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048558"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Skapa hanteringsgrupper för organisering och hantering av resurser

Hanterings grupper är behållare som hjälper dig att hantera åtkomst, principer och efterlevnad över flera prenumerationer. Skapa de här behållarna för att skapa en effektiv och effektiv hierarki som kan användas med [Azure policy](../policy/overview.md) och [Azure Role-baserade åtkomst kontroller](../../role-based-access-control/overview.md). Mer information om hanterings grupper finns i [ordna dina resurser med Azures hanterings grupper](overview.md).

Den första hanterings gruppen som skapas i katalogen kan ta upp till 15 minuter att slutföra. Det finns processer som körs första gången för att konfigurera hanterings grupps tjänsten i Azure för din katalog. Du får ett meddelande när processen är klar. Mer information finns i [den första installationen av hanterings grupper](./overview.md#initial-setup-of-management-groups).

## <a name="create-a-management-group"></a>Skapa en hanteringsgrupp

Alla Azure AD-användare i klient organisationen kan skapa en hanterings grupp utan den Skriv behörighet för hanterings gruppen som tilldelats den användaren. Den nya hanterings gruppen är underordnad rot hanterings gruppen och skaparen får rollen "ägare". Hanterings grupp tjänsten tillåter den här möjligheten så att roll tilldelningar inte behövs på rotnivå. Inga användare har åtkomst till rot hanterings gruppen när den skapas. För att undvika att Hurdle för att hitta Azure AD global-administratörer ska börja använda hanterings grupper, tillåter vi att de första hanterings grupperna skapas i roten  
nivå.

Du kan skapa hanterings gruppen med hjälp av portalen, en [Azure Resource Manager mall](../../azure-resource-manager/templates/deploy-to-tenant.md#create-management-group), PowerShell eller Azure CLI.

### <a name="create-in-portal"></a>Skapa i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **all**hantering av tjänster och  >  **styrning**.

1. Välj **hanteringsgrupper**.

1. Välj **+ Lägg till hanterings grupp**.

   :::image type="content" source="./media/main.png" alt-text="Sida för att arbeta med hanterings grupper" border="false":::

1. Fyll i fältet hanterings grupp-ID.

   - **Hanterings gruppens ID** är katalogens unika identifierare som används för att skicka kommandon i den här hanterings gruppen. Den här identifieraren kan inte redige ras när den används i hela Azure-systemet för att identifiera den här gruppen. [Rot hanterings gruppen](overview.md#root-management-group-for-each-directory) skapas automatiskt med ett ID som är Azure Active Directory-ID: t. Tilldela ett unikt ID för alla andra hanterings grupper.
   - Fältet visnings namn är det namn som visas i Azure Portal. Ett separat visnings namn är ett valfritt fält när du skapar hanterings gruppen och kan ändras var som helst  
     tid.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Alternativ fönster för att skapa en ny hanterings grupp" border="false":::

1. Välj **Spara**.

### <a name="create-in-powershell"></a>Skapa i PowerShell

För PowerShell använder du cmdleten [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) för att skapa en ny hanterings grupp.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** är en unik identifierare som skapas. Detta ID används av andra kommandon för att referera till den här gruppen och kan inte ändras senare.

Om du vill att hanterings gruppen ska visa ett annat namn i Azure Portal lägger du till parametern **DisplayName** . Om du till exempel vill skapa en hanterings grupp med GroupName för Contoso och visnings namnet "contoso Group", använder du följande cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

I föregående exempel skapas den nya hanterings gruppen under rot hanterings gruppen. Använd **parametern för att ange** en annan hanterings grupp som överordnad.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Skapa i Azure CLI

För Azure CLI använder du kommandot [AZ Account Management-Group Create](/cli/azure/account/management-group#az-account-management-group-create) för att skapa en ny hanterings grupp.

```azurecli-interactive
az account management-group create --name Contoso
```

**Namnet** är en unik identifierare som skapas. Detta ID används av andra kommandon för att referera till den här gruppen och kan inte ändras senare.

Om du vill att hanterings gruppen ska visa ett annat namn i Azure Portal lägger du till **visnings namn** parametern. Om du till exempel vill skapa en hanterings grupp med GroupName för Contoso och visnings namnet "contoso Group", använder du följande kommando:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

I föregående exempel skapas den nya hanterings gruppen under rot hanterings gruppen. Om du vill ange en annan hanterings grupp som överordnad använder du den **överordnade** parametern och anger namnet på den överordnade gruppen.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hanteringslösningar här:

- [Skapa hanteringsgrupper för att organisera Azure-resurser](./create.md)
- [Så här ändrar, raderar och hanterar du dina hanteringsgrupper](./manage.md)
- [Granska hanteringsgrupper i Azure PowerShell-resursmodulen](/powershell/module/az.resources#resources)
- [Granska hanteringsgrupper i REST API](/rest/api/resources/managementgroups)
- [Granska hanteringsgrupper i Azure CLI](/cli/azure/account/management-group)
