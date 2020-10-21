---
title: Överför Azure-prenumerationer mellan prenumeranter och molnlösningsleverantörer
description: Lär dig hur du överför Azure-prenumerationer mellan prenumeranter och molnlösningsleverantörer.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 09/24/2020
ms.author: banders
ms.openlocfilehash: ae504072e2a2cc481217933478ccbfb7bc3372b3
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132371"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Överför Azure-prenumerationer mellan prenumeranter och molnlösningsleverantörer

Den här artikeln innehåller enkla steg för att överföra Azure-prenumerationer till och från molnlösningsleverantörer (CSP) och deras kunder. Informationen här är avsedd att hjälpa Azure-prenumeranten att samarbeta med sin partner. Information som Microsoft-partner använder för överföringsprocessen dokumenteras i [Läs om hur du överför en Azure-prenumeration för en kund till en annan partner](/partner-center/switch-azure-subscriptions-to-a-different-partner).

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>Överföra EA-prenumerationer till en CSP-partner

CSP-partner som är certifierade som [Azure Expert-leverantörer av hanterade tjänster (MSP)](https://partner.microsoft.com/membership/azure-expert-msp) kan begära överföring av Azure-prenumerationer för kunder som har ett direkt Enterprise-avtal (EA). Prenumerationsöverföringar tillåts bara för kunder som har godkänt ett Microsoft-kundavtal (MCA) och köpt en Azure-plan.

När begäran har godkänts kan CSP:n tillhandahålla en kombinerad faktura till sina kunder. Mer information om hur CSP:er överför prenumerationer finns i [Få faktureringsägarskap för Azure-prenumerationer för ditt MPA-konto](mpa-request-ownership.md).

>[!IMPORTANT]
> När du har överfört en EA-prenumeration till en CSP-partner återställs alla kvotökningar som tidigare tillämpats för EA-prenumerationen till standardvärdet. Om du behöver öka kvoten efter att prenumerationen har överförts ber du molnlösningsleverantören skicka en begäran om [ökad kvot](../../azure-portal/supportability/regional-quota-requests.md). 

## <a name="other-subscription-transfers-to-a-csp-partner"></a>Andra prenumerationsöverföringar till en CSP-partner

För att kunna överföra andra Azure-prenumerationer till en CSP-partner måste prenumeranten flytta resurser från källprenumerationer till CSP-prenumerationer. Använd följande vägledning när du flyttar resurser mellan prenumerationer.

1. Samarbeta med din CSP-partner när du skapar Azure CSP-målprenumerationer.
1. Se till att käll- och målprenumerationerna finns i samma Azure Active Directory-klient (Azure AD).  
    Du kan inte ändra Azure AD-klienten för en Azure CSP-prenumeration. I stället måste du lägga till eller associera källprenumerationen till CSP Azure AD-klienten. Mer information finns i [Lägga till eller associera en Azure-prenumeration till Azure Active Directory-klienten](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    > [!IMPORTANT]
    > - När du associerar en prenumeration till en annan Azure AD-katalog förlorar de användare som har tilldelats roller med [rollbaserad åtkomstkontroll (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) sin åtkomst. Klassiska prenumerationsadministratörer, inklusive tjänstadministratörer och medadministratörer, förlorar också åtkomsten.
    > - Principtilldelningar tas också bort från en prenumeration när prenumerationen associeras med en annan katalog.
1. Det användarkonto som du använder när du gör överföringen måste ha [Azure RBAC](add-change-subscription-administrator.md)-ägaråtkomst på båda prenumerationerna.
1. Innan du börjar måste du [verifiera](/rest/api/resources/resources/validatemoveresources) att alla Azure-resurser kan flyttas från källprenumerationen till målprenumerationen.  
    Vissa Azure-resurser kan inte flyttas mellan prenumerationer. En fullständig lista över Azure-resurser som kan flyttas finns i [Stöd för flytt av resurser](../../azure-resource-manager/management/move-support-resources.md).
    > [!IMPORTANT]
    >  - Azure CSP stöder endast Azure Resource Manager-resurser. Om det finns Azure-resurser i källprenumerationen som har skapats med den klassiska distributionsmodellen i Azure måste du först migrera dem till [Azure Resource Manager](/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm) innan du flyttar mellan prenumerationerna. För att kunna visa webbsidan måste du vara en partner.

1. Kontrollera att alla källprenumerationstjänster använder Azure Resource Manager-modellen. Överför sedan resurser från källprenumerationen till målprenumerationen med hjälp av [Azure Resource Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - När du flyttar Azure-resurser mellan prenumerationer kan det uppstå avbrott i tjänsten, beroende på vilka resurser som ingår i prenumerationerna.

## <a name="transfer-csp-subscription-to-other-offer"></a>Överföra CSP-prenumeration till ett annat erbjudande

För att kunna överföra andra Azure-prenumerationer till en CSP-partner måste prenumeranten flytta resurser mellan källprenumerationer och målprenumerationer.

1. Skapa Azure-målprenumerationer.
1. Se till att käll- och målprenumerationerna finns i samma Azure Active Directory-klient (Azure AD). Mer information om hur du ändrar en Azure AD-klient finns i [Lägga till eller associera en Azure-prenumeration till Azure Active Directory-klienten](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    Observera att ändringskatalogen inte är CSP-prenumerationen. Du överförs till exempel från en CSP till en prenumeration där du betalar per användning. Du måste ändra katalog för prenumerationen med betalning per användning så att katalogen matchar.

    > [!IMPORTANT]
    >  - När du associerar en prenumeration med en annan katalog förlorar de användare som har tilldelats roller med [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) sin åtkomst. Klassiska prenumerationsadministratörer, inklusive tjänstadministratörer och medadministratörer, förlorar också åtkomsten.
    >  - Principtilldelningar tas också bort från en prenumeration när prenumerationen associeras med en annan katalog.

1. Det användarkonto som du använder när du gör överföringen måste ha [Azure RBAC](add-change-subscription-administrator.md)-ägaråtkomst på båda prenumerationerna.
1. Innan du börjar måste du [verifiera](/rest/api/resources/resources/validatemoveresources) att alla Azure-resurser kan flyttas från källprenumerationen till målprenumerationen.
    > [!IMPORTANT]
    >  - Vissa Azure-resurser kan inte flyttas mellan prenumerationer. En fullständig lista över Azure-resurser som kan flyttas finns i [Stöd för flytt av resurser](../../azure-resource-manager/management/move-support-resources.md).

1. Överför resurser från källprenumerationen till målprenumerationen med hjälp av [Azure Resource Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - När du flyttar Azure-resurser mellan prenumerationer kan det uppstå avbrott i tjänsten, beroende på vilka resurser som ingår i prenumerationerna.

## <a name="next-steps"></a>Nästa steg
- [Få faktureringsägarskap för Azure-prenumerationer för ditt MPA-konto](mpa-request-ownership.md).
- Läs om hur du kan [hantera konton och prenumerationer med Azure-fakturering](../index.yml).