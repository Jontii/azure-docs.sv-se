---
title: Lägg till eller ändra administratörer för en Azure-prenumeration
description: Här beskrivs hur du lägger till eller ändrar administratörer för en Azure-prenumeration med hjälp av rollbaserad åtkomst i Azure (Azure RBAC).
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 10956953f9ab3a9e32b9da4ab8a3501d38b0e2c3
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369666"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Lägg till eller ändra administratörer för en Azure-prenumeration


Du måste ha rätt administratörsroll för att hantera åtkomst till Azure-resurser. Azure har ett auktoriseringssystem som kallas [rollbaserad åtkomstkontroll i Azure](../../role-based-access-control/overview.md) (Azure RBAC) där du kan välja mellan flera olika inbyggda roller. Du kan tilldela dessa roller för olika omfång, som en hanteringsgrupp, en prenumeration eller en resursgrupp. Som standard kan personen som skapar en ny Azure-prenumeration tilldela andra användare administrativ åtkomst till en prenumeration.

I den här artikeln beskrivs hur du lägger till eller ändrar administratörsroller för användare med Azure RBAC i ett prenumerationsomfång.

Microsoft rekommenderar att du hanterar åtkomsten till resurser med Azure RBAC. Om du fortfarande använder den klassiska distributionsmodellen och hanterar de klassiska resurserna med hjälp av [Azure Service Management Powershell-modulen](/powershell/module/servicemanagement/azure.service) måste du använda en klassisk administratör.

> [!TIP]
> Om du bara använder Microsoft Azure-portalen för att hantera de klassiska resurserna behöver du inte använda den klassiska administratören.

Mer information finns i [Azure Resource Manager jämfört med klassisk distribution](../../azure-resource-manager/management/deployment-models.md) och [Administratörer för klassiska Azure-prenumerationer](../../role-based-access-control/classic-administrators.md).

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-subscription-administrator"></a>Tilldela en prenumerationsadministratör

En administratör måste tilldela rollen [Ägare](../../role-based-access-control/built-in-roles.md#owner) (en Azure-roll) i prenumerationsomfånget för att göra en användare till administratör för en Azure-prenumeration. Rollen ägare ger fullständig åtkomst till alla resurser i prenumerationen, inklusive rätten att ge åtkomst till andra. De här stegen är desamma som för andra rolltilldelningar.

Om du inte vet som är kontoadministratör för en prenumeration, tar du reda på det med hjälp av följande steg.

1. Öppna [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration du vill kontrollera och gå till **Inställningar** .
1. Välj **Egenskaper** . Prenumerationens kontoadministratör visas i rutan **Kontoadministratör** .

### <a name="to-assign-a-user-as-an-administrator"></a>Så här gör du en användare till administratör

1. Logga in på Microsoft Azure-portalen som prenumerationsägare och öppna [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Klicka på prenumerationen du vill ge åtkomst till.

1. Klicka på **Åtkomstkontroll (IAM)** .

1. Klicka på fliken **Rolltilldelningar** så att du ser alla rolltilldelningar för prenumerationen.

    ![Skärmbild som visar rolltilldelningar](./media/add-change-subscription-administrator/role-assignments.png)

1. Klicka på **Lägg till** > **Lägg till rolltilldelning** för att öppna fönstret **Lägg till rolltilldelning** .

    Om du inte har behörighet att tilldela roller är det här alternativet inaktiverat.

1. I listrutan **Roll** väljer du rollen **Ägare** .

1. Välj en användare i listan **Välj** . Om du inte ser användaren i listan kan du ange visningsnamn och e-postadresser i rutan **Välj** om du vill söka i katalogen.

    ![Skärmbild där rollen Ägare är vald](./media/add-change-subscription-administrator/add-role.png)

1. Klicka på **Spara** för att tilldela rollen.

    Efter en stund tilldelas rollen Ägare till användaren i prenumerationsomfånget.

## <a name="next-steps"></a>Nästa steg

* [Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
* [Förstå de olika rollerna i Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Associera eller lägga till en Azure-prenumeration till Azure Active Directory-klienten](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Behörigheter för administratörsrollen i Azure Active Directory](../../active-directory/roles/permissions-reference.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.
