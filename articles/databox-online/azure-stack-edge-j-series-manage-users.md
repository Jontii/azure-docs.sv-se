---
title: Azure Stack Edge Pro GPU hantera användare | Microsoft Docs
description: Beskriver hur du använder Azure Portal för att hantera användare på din Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 66967c1fa8f6ca42459559caf46bffb0e5afe925
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743598"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-stack-edge-pro"></a>Använd Azure Portal för att hantera användare på din Azure Stack Edge Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Den här artikeln beskriver hur du hanterar användare i Azure Stack Edge Pro. Du kan hantera Azure Stack Edge Pro via Azure Portal eller via det lokala webb gränssnittet. Använd Azure-portalen om du vill lägga till, ändra eller ta bort användare.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Lägga till en användare
> * Ändra användare
> * Ta bort en användare

## <a name="about-users"></a>Om användare

Användare kan ha skrivskyddad åtkomst eller fullständig behörighet. Som namnet antyder kan användare med skrivskyddad åtkomst endast visa data som delas. Användare med fullständig behörighet kan läsa data som delas, skriva till dessa resurser och ändra eller ta bort delade data.

 - **Användare med fullständig behörighet** – En lokal användare med fullständig åtkomst.
 - **Skrivskyddad användare** – En lokal användare med skrivskyddad åtkomst. Dessa användare är kopplade till resurser som tillåter skrivskyddade åtgärder.

Användarbehörigheterna definieras när användaren skapas i samband med att resurserna skapas. När behörigheterna som associeras med en användare har definierats kan dessa ändras med hjälp av Utforskaren.


## <a name="add-a-user"></a>Lägga till en användare

Lägg till en användare genom att följa de här instruktionerna på Azure-portalen.

1. I Azure Portal går du till din Azure Stack Edge-resurs och går sedan till **översikt > användare**. Välj **+ Lägg till användare** i kommando fältet.

    ![Välj Lägg till användare](media/azure-stack-edge-j-series-manage-users/add-user-1.png)

2. Ange användarnamnet och lösenordet för den användare som du vill lägga till. Bekräfta lösen ordet och välj **Lägg till**.

    ![Ange användar namn och lösen ord](media/azure-stack-edge-j-series-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Dessa användare är reserverade i systemet och bör inte användas: Administratör, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount och Gäst.  

3. Ett meddelande visas när användaren startar och har slutförts. När användaren har skapats går du till kommando fältet och väljer **Uppdatera** för att visa den uppdaterade listan med användare.


## <a name="modify-user"></a>Ändra användare

Du kan ändra lösenordet som associeras med en användare när användaren har skapats. Välj i listan över användare. Ange och bekräfta det nya lösen ordet. Spara ändringarna.
 
![Ändra användare](media/azure-stack-edge-j-series-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Ta bort en användare

Utför följande steg på Azure-portalen om du vill ta bort en användare.


1. I Azure Portal går du till din Azure Stack Edge-resurs och går sedan till **översikt > användare**.

    ![Välj användare som ska tas bort](media/azure-stack-edge-j-series-manage-users/delete-user-1.png)

2. Välj en användare i listan över användare och välj sedan **ta bort**. Bekräfta borttagningen när du uppmanas att göra det.

    ![Välj användare att ta bort 2](media/azure-stack-edge-j-series-manage-users/delete-user-2.png)

Listan över användare uppdateras för att återspegla den borttagna användaren.

![Uppdaterad lista över användare](media/azure-stack-edge-j-series-manage-users/delete-user-4.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig att [hantera bandbredd](azure-stack-edge-j-series-manage-bandwidth-schedules.md).
