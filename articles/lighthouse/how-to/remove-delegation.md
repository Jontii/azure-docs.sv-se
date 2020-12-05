---
title: Ta bort åtkomst till en delegering
description: Lär dig hur du tar bort åtkomst till resurser som har delegerats till en tjänst leverantör för Azure Lighthouse.
ms.date: 12/03/2020
ms.topic: how-to
ms.openlocfilehash: d3442bb9fd2f6e7423fd4bf28cace1f7fd91ad80
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608460"
---
# <a name="remove-access-to-a-delegation"></a>Ta bort åtkomst till en delegering

När en kunds prenumeration eller resurs grupp har delegerats till en tjänst leverantör för [Azure Lighthouse](../overview.md)kan delegeringen tas bort om det behövs. När en delegering har tagits bort kommer [Azure-delegerad resurs hanterings](../concepts/azure-delegated-resource-management.md) åtkomst som tidigare beviljats användare i tjänst leverantörens klient organisation inte längre att gälla.

Att ta bort en delegering kan göras av en användare i antingen kund-eller tjänst leverantörs klienten, så länge användaren har rätt behörigheter.

> [!TIP]
> Även om vi refererar till tjänst leverantörer och kunder i det här avsnittet kan [företag som hanterar flera klienter](../concepts/enterprise.md) använda samma processer.

## <a name="customers"></a>Kunder

Användare i kundens klient organisation som har den [inbyggda rollen ägare](../../role-based-access-control/built-in-roles.md#owner) för en prenumeration kan ta bort tjänst leverantörens åtkomst till prenumerationen (eller resurs grupper i den prenumerationen). För att göra det kan en användare i kundens klient organisation gå till [sidan tjänst leverantörer](view-manage-service-providers.md#add-or-remove-service-provider-offers) i Azure Portal, hitta erbjudandet på sidan med **tjänst leverantörs erbjudanden** och välj pappers korgs ikonen på raden för det erbjudandet.

När du har bekräftat borttagningen kommer inga användare i tjänste leverantörens klient att kunna komma åt de resurser som tidigare har delegerats.

## <a name="service-providers"></a>Tjänst leverantörer

Användare i en hanterande klient organisation kan ta bort åtkomst till delegerade resurser om de har beviljats [ta bort rollen som hanterare för registrering av hanterade tjänster](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) för kundens resurser. Om den här rollen inte har tilldelats någon tjänst leverantörs användare kan delegeringen bara tas bort av en användare i kundens klient organisation.

Exemplet nedan visar en tilldelning som beviljar **borttagnings rollen för hanterade tjänster** som kan tas med i en parameter fil under [onboarding-processen](onboard-customer.md):

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Den här rollen kan också väljas i en **auktorisering** när du [skapar ett hanterat tjänst erbjudande](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#authorization) för att publicera på Azure Marketplace.

En användare med den här behörigheten kan ta bort en delegering på något av följande sätt.

### <a name="azure-portal"></a>Azure Portal

1. Gå till [sidan mina kunder](view-manage-customers.md).
2. Välj **delegeringar**.
3. Hitta den delegering som du vill ta bort och välj sedan pappers korgs ikonen som visas på raden.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure-delegerad resurshantering](../concepts/azure-delegated-resource-management.md).
- [Visa och hantera kunder](view-manage-customers.md) genom att gå till **mina kunder** i Azure Portal.
- Lär dig hur du [uppdaterar en tidigare delegering](update-delegation.md).
