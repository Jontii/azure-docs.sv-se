---
title: Azure AD Connect etablering av moln etablering på begäran
description: I den här artikeln beskrivs etablerings funktionen på begäran.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72623d69f9fb0cbc68758a362bb977e5e02d2c00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91637305"
---
# <a name="azure-ad-connect-cloud-provisioning-on-demand-provisioning"></a>Azure AD Connect etablering av moln etablering på begäran

Azure AD Connect Cloud etableringen har introducerat en ny funktion som gör att du kan testa konfigurations ändringar genom att tillämpa dessa ändringar på en enskild användare.  Du kan använda detta för att verifiera och kontrol lera att ändringarna som har gjorts i konfigurationen har tillämpats korrekt och synkroniseras korrekt med Azure AD.  

> [!IMPORTANT] 
> När du använder etablering på begäran tillämpas inte omfångs filtren på den användare som du har valt.  Det innebär att du kan använda etablering på begäran för användare som finns utanför de organisationsenheter som du har angett.


## <a name="using-on-demand-provisioning"></a>Använda etablering på begäran
Följ stegen nedan om du vill använda den nya funktionen.


1.  I Azure Portal väljer du **Azure Active Directory**.
2.  Välj **Azure AD Connect**.
3.  Välj **Hantera etablering**.

    ![Hantera etablering](media/how-to-configure/manage1.png)
4. Under **konfiguration**väljer du din konfiguration.
5. Under **Verifiera** klickar du på knappen **Tillhandahåll en användare** . 

 ![Etablera en användare](media/how-to-on-demand-provision/on-demand2.png)

6. På sidan etablering på begäran.  Ange ett **unikt namn** för en användare och klicka på knappen **Tillhandahåll** .  
 
 ![Etablering på begäran](media/how-to-on-demand-provision/on-demand3.png)
7. När den är klar bör du se en lyckad skärm och en grön kryss ruta som anger att den har kon figurer ATS.  Eventuella fel kommer att visas till vänster.

  ![Klart](media/how-to-on-demand-provision/on-demand4.png)

Nu kan du titta på användaren och avgöra om ändringarna som du har gjort i konfigurationen har tillämpats.  Resten av det här dokumentet beskriver de enskilda avsnitten som visas i informationen om en korrekt synkroniserad användare.

## <a name="import-user-details"></a>Importera användar information
Det här avsnittet innehåller information om den användare som har importer ATS från Active Directory.  Så här ser det ut som om användaren är etablerad i Azure AD.  Klicka på länken **Visa information** om du vill visa den här informationen.

![Importera användare](media/how-to-on-demand-provision/on-demand5.png)

Med hjälp av den här informationen kan du se de olika attributen och deras värden, som har importer ATS.  Om du har skapat en anpassad attributmappning, kommer du att kunna se värdet här.
![Importera användar information](media/how-to-on-demand-provision/on-demand6.png)

## <a name="determine-if-user-is-in-scope-details"></a>Avgöra om användaren befinner sig i omfångs information
Det här avsnittet innehåller information om huruvida användaren som har importer ATS till Azure AD finns i omfånget.  Klicka på länken **Visa information** om du vill visa den här informationen.

![Användar omfång](media/how-to-on-demand-provision/on-demand7.png)

Med hjälp av den här informationen kan du se ytterligare information om användarens omfång.

![Användar omfattnings information](media/how-to-on-demand-provision/on-demand10a.png)

## <a name="match-user-between-source-and-target-system-details"></a>Matcha användare mellan käll-och mål system information
Det här avsnittet innehåller information om huruvida användaren redan finns i Azure AD och om en koppling ska ske i stället för att en ny användare skapas.  Klicka på länken **Visa information** om du vill visa den här informationen.
![Visa information](media/how-to-on-demand-provision/on-demand8.png)

Med hjälp av den här informationen kan du se om en matchning hittades eller om en ny användare ska skapas.

![Användarinformation](media/how-to-on-demand-provision/on-demand11.png)

Matchnings informationen visar ett meddelande med någon av följande tre åtgärder.  De är:
- Skapa – en användare skapas i Azure AD
- Uppdatera – en användare uppdateras baserat på en ändring som gjorts i konfigurationen
- Ta bort – en användare tas bort från Azure AD.

Beroende på vilken typ av åtgärd du utför, kan meddelandet variera.

## <a name="perform-action-details"></a>Utför åtgärds information
Det här avsnittet innehåller information om användaren som har tillhandahållits eller exporter ATS till Azure AD efter att konfigurationen har tillämpats.  Detta är vad användaren ser ut när den har etablerad till Azure AD.  Klicka på länken **Visa information** om du vill visa den här informationen.
![Utför åtgärds information](media/how-to-on-demand-provision/on-demand9.png)

Med hjälp av den här informationen kan du se värdena för attributen när konfigurationen har tillämpats.  Ser de ut ungefär som importerade eller är de olika?  Kan konfigurationen genomföras?  

På så sätt kan du spåra attributet Transformation när den flyttas genom molnet och till din Azure AD-klient.

![spåra attribut](media/how-to-on-demand-provision/on-demand12.png)

## <a name="next-steps"></a>Nästa steg 

- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
- [Så här installerar du Azure AD Connect Cloud-etablering](how-to-install.md)
 