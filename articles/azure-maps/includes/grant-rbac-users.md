---
title: Bevilja rollbaserad åtkomst för användare
titleSuffix: Azure Maps
description: Använd rollbaserad åtkomst kontroll för att bevilja användare tillstånd att Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 816e4e439bb98ced8bbc5b5d8a7d38ef65aee33f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88011029"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Bevilja rollbaserad åtkomst för användare att Azure Maps

Du beviljar *Azure rollbaserad åtkomst kontroll (Azure RBAC)* genom att tilldela antingen en Azure AD-grupp eller säkerhets objekt till en eller flera Azure Maps roll definitioner. Om du vill visa de roll definitioner för Azure som är tillgängliga för Azure Maps går du till **åtkomst kontroll (IAM)**. Välj **roller**och Sök sedan efter roller som börjar med *Azure Maps*.

* För att effektivt hantera en stor mängd användares åtkomst till Azure Maps, se [Azure AD-grupper](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups).
* För att användarna ska kunna autentisera till programmet måste användarna skapas i Azure AD. Se [Lägg till eller ta bort användare med Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory).

Läs mer i [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/) för att effektivt hantera en katalog för användare.

1. Gå till ditt **Azure Maps-konto**. Välj roll tilldelningen **åtkomst kontroll (IAM)**  >  **Role assignment**.

    ![Bevilja åtkomst med Azure RBAC](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. På fliken **roll tilldelningar** under **roll**väljer du en inbyggd Azure Maps roll definition som **Azure Maps data läsare** eller **Azure Maps data deltagare**. Under **tilldela åtkomst till**väljer du **Azure AD-användare, grupp eller tjänstens huvud namn**. Välj huvudobjektet efter namn. Välj sedan **Spara**.

   * Se information om [Lägg till eller ta bort roll tilldelningar](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

> [!WARNING]
> Azure Maps inbyggda roll definitioner ger en mycket stor behörighet till många Azure Maps REST-API: er. Om du vill begränsa API: erna för användare till ett minimum, se [skapa en anpassad roll definition och tilldela användare](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) till den anpassade roll definitionen. Detta gör det möjligt för användarna att ha den minsta behörighet som krävs för programmet.