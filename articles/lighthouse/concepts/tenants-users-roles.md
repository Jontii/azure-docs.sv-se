---
title: Klienter, roller och användare i Azure Lighthouse-scenarier
description: Förstå begreppen för Azure Active Directory klienter, användare och roller, samt hur de kan användas i Azure Lighthouse-scenarier.
ms.date: 07/03/2020
ms.topic: conceptual
ms.openlocfilehash: 6dae09ddd7760af1663e0329eb646c8956dff3ac
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424113"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Klienter, roller och användare i Azure Lighthouse-scenarier

Innan du registrerar kunder för [Azure Lighthouse](../overview.md)är det viktigt att förstå hur Azure Active Directory (Azure AD)-klienter, användare och roller fungerar, samt hur de kan användas i Azure Lighthouse-scenarier.

En *klient organisation* är en dedikerad och betrodd instans av Azure AD. Varje klient representerar vanligt vis en enda organisation. [Azure-delegerad resurs hantering](azure-delegated-resource-management.md) möjliggör logisk projektion av resurser från en klient till en annan klient organisation. Detta gör det möjligt för användare i hanterings klienten (till exempel en som tillhör en tjänst leverantör) att komma åt delegerade resurser i en kunds klient, eller låta [företag med flera innehavare centralisera sina hanterings åtgärder](enterprise.md).

För att uppnå den här logiska projektionen måste en prenumeration (eller en eller flera resurs grupper inom en prenumeration *) registreras i* Azure-Lighthouse. Den här onboarding-processen kan göras antingen [via Azure Resource Manager mallar](../how-to/onboard-customer.md) eller genom [att publicera ett offentligt eller privat erbjudande till Azure Marketplace](../how-to/publish-managed-services-offers.md).

Vilken onboarding-metod du väljer måste du definiera *auktoriseringar*. Varje auktorisering anger ett användar konto i hanterings klienten som kommer att ha åtkomst till de delegerade resurserna och en inbyggd roll som anger de behörigheter som var och en av dessa användare kommer att ha för dessa resurser.

## <a name="role-support-for-azure-lighthouse"></a>Roll stöd för Azure-Lighthouse

När du definierar en auktorisering måste varje användar konto tilldelas en av de [inbyggda Azure-rollerna](../../role-based-access-control/built-in-roles.md). Administratörs roller för anpassade roller och [klassiska prenumerationer](../../role-based-access-control/classic-administrators.md) stöds inte.

Alla [inbyggda roller](../../role-based-access-control/built-in-roles.md) stöds för närvarande med Azure Lighthouse, med följande undantag:

- [Ägar](../../role-based-access-control/built-in-roles.md#owner) rollen stöds inte.
- Inbyggda roller med [DataActions](../../role-based-access-control/role-definitions.md#dataactions) -behörighet stöds inte.
- Den inbyggda rollen [administratör för användar åtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator) stöds, men endast för det begränsade syftet med [att tilldela roller till en hanterad identitet i kund klienten](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). Inga andra behörigheter som vanligt vis beviljas av den här rollen kommer att gälla. Om du definierar en användare med den här rollen måste du också ange de inbyggda roller som den här användaren kan tilldela till hanterade identiteter.

> [!NOTE]
> När en ny tillämplig inbyggd roll läggs till i Azure kan den tilldelas när en kund integreras [med hjälp av Azure Resource Manager mallar](../how-to/onboard-customer.md). Det kan uppstå en fördröjning innan den nyligen tillagda rollen blir tillgänglig i Partner Center vid [publicering av ett hanterat tjänst erbjudande](../how-to/publish-managed-services-offers.md).

## <a name="best-practices-for-defining-users-and-roles"></a>Metod tips för att definiera användare och roller

När du skapar dina auktoriseringar rekommenderar vi följande metod tips:

- I de flesta fall vill du tilldela behörigheter till en användar grupp eller ett tjänst huvud namn i Azure AD, i stället för till en serie med enskilda användar konton. På så sätt kan du lägga till eller ta bort åtkomst för enskilda användare utan att behöva uppdatera och publicera om planen när dina åtkomst krav ändras.
- Se till att du följer principen om minsta behörighet så att användarna bara har de behörigheter som krävs för att utföra sitt jobb, vilket bidrar till att minska risken för oavsiktliga fel. Mer information finns i [rekommenderade säkerhets metoder](../concepts/recommended-security-practices.md).
- Ta med en användare med [tilldelnings rollen för hanterade tjänster](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) , så att du kan [ta bort åtkomsten till delegeringen](../how-to/remove-delegation.md) senare om det behövs. Om den här rollen inte är tilldelad kan delegerade resurser bara tas bort av en användare i kundens klient organisation.
- Se till att alla användare som behöver [Visa sidan mina kunder i Azure Portal](../how-to/view-manage-customers.md) har rollen [läsare](../../role-based-access-control/built-in-roles.md#reader) (eller någon annan inbyggd roll som innehåller läsar åtkomst).

> [!IMPORTANT]
> För att du ska kunna lägga till behörigheter för en Azure AD-grupp måste **grupp typen** anges till **säkerhet**. Det här alternativet väljs när gruppen skapas. Mer information finns i [Skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [rekommenderade säkerhets metoder för Azure Lighthouse](recommended-security-practices.md).
- Publicera dina kunder till Azure Lighthouse, antingen genom att [använda Azure Resource Manager mallar](../how-to/onboard-customer.md) eller genom [att publicera ett privat eller offentligt hanterat tjänst erbjudande på Azure Marketplace](../how-to/publish-managed-services-offers.md).
