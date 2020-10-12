---
title: inkludera fil
description: inkludera fil
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 05/26/2020
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: ee02200f1fb32d430a858d218e27534561ebc24a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "83868065"
---
## <a name="create-one-or-more-access-reviews"></a>Skapa en eller flera åtkomst granskningar

1. Klicka på **ny** för att skapa en ny åtkomst granskning.

1. Ge åtkomst granskningen namnet. Du kan också ange en beskrivning av granskningen. Namnet och beskrivningen visas för granskarna.

    ![Skapa en åtkomst granskning – granska namn och beskrivning](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Ange **start datum**. Som standard sker en åtkomst granskning en gång, startas samma gång som den skapas och den avslutas om en månad. Du kan ändra start-och slutdatumen så att en åtkomst granskning börjar i framtiden och det senaste antalet dagar som du vill.

    ![Start datum, frekvens, varaktighet, slut, antal gånger och slutdatum](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Om du vill göra åtkomst granskningen återkommande ändrar du **frekvens** inställningen från **en gång** till **varje vecka**, **varje månad**, varje kvartal, **varje** **kvartal**eller varje **halvår**. Använd skjutreglaget **varaktighet** eller text rutan för att definiera hur många dagar varje granskning av den återkommande serien ska vara öppen för inmatade i granskare. Den längsta tid som du kan ange för en månatlig granskning är till exempel 27 dagar för att undvika överlappande granskningar.

1. Använd **End** -inställningen för att ange hur du ska avsluta gransknings serien för återkommande åtkomst. Serien kan sluta på tre sätt: den körs kontinuerligt för att starta recensioner på obestämd tid, fram till ett visst datum eller efter att ett definierat antal förekomster har slutförts. En annan användar administratör eller en annan global administratör kan stoppa serien när den har skapats genom att ändra datumet i **Inställningar**, så att det slutar på det datumet.

1. I avsnittet **användare** väljer du en eller flera roller som du vill granska medlemskap i.

    ![Användarens omfång att granska roll medlemskap för](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > - Roller som väljs här inkluderar både [permanenta och berättigade roller](../articles/active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md).
    > - Om du väljer mer än en roll skapas flera åtkomst granskningar. Om du till exempel väljer fem roller skapas fem olika åtkomst granskningar.

    Om du skapar en åtkomst granskning av **Azure AD-roller**, visar följande ett exempel på listan granska medlemskap.

    ![Fönstret granska medlemskap visar Azure AD-roller som du kan välja](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Om du skapar en åtkomst granskning av **Azures resurs roller**visar följande bild ett exempel på listan granska medlemskap.

    ![Rutan granska medlemskap visar de Azure-resurstilldelningar du kan välja](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. I avsnittet **granskare väljer du en** eller flera personer för att granska alla användare. Alternativt kan du välja att låta medlemmarna granska sin egen åtkomst.

    ![Listor över granskare av markerade användare eller medlemmar (Self)](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Valda användare** – Använd det här alternativet om du inte vet vem som behöver åtkomst. Med det här alternativet kan du tilldela granskningen till en resurs ägare eller grupp hanterare för att slutföra.
    - **Medlemmar (Self)** – Använd det här alternativet om du vill att användarna ska granska sina egna roll tilldelningar.

### <a name="upon-completion-settings"></a>Vid slut för ande inställningar

1. Om du vill ange vad som händer när en granskning har slutförts expanderar du avsnittet **när inställningarna slutförs** .

    ![Vid slut för ande inställningar som ska tillämpas automatiskt och bör granskning inte svara](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Om du vill ta bort åtkomst automatiskt för användare som har nekats, ställer du in **automatiskt tillämpa resultat på resursen** för att **Aktivera**. Om du vill tillämpa resultaten manuellt när granskningen är klar ställer du in växeln på **inaktivera**.

1. Använd listan **ska inte svara** på listan om du vill ange vad som händer för användare som inte granskas av granskaren under gransknings perioden. Den här inställningen påverkar inte användare som har granskats manuellt av granskarna. Om den sista granskaren av beslutet är neka tas användarens åtkomst bort.

    - **Ingen ändring** – lämna användarens åtkomst oförändrad
    - **Ta bort åtkomst** – ta bort användares åtkomst
    - **Godkänn åtkomst** – Godkänn användarens åtkomst
    - **Ta rekommendationer** – beakta systemets rekommendation om att neka eller godkänna användarens fortsatta åtkomst

### <a name="advanced-settings"></a>Avancerade inställningar

1. Om du vill ange ytterligare inställningar expanderar du avsnittet **Avancerade inställningar** .

    ![Avancerade inställningar för Visa rekommendationer, Kräv orsak för godkännande, e-postaviseringar och påminnelser](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Ange **Visa rekommendationer** för att **Aktivera** för att Visa granskarna system rekommendationer baserat på användarens åtkomst information.

1. Ange **Kräv orsak för godkännande** för att **göra det möjligt** för granskaren att ange en orsak till godkännande.

1. Ange **e-postaviseringar** för att **tillåta** att Azure AD skickar e-postmeddelanden till granskare när en åtkomst granskning startar och till administratörer när en granskning har slutförts.

1. Ställ in **påminnelser** för att **tillåta** att Azure AD skickar påminnelser om åtkomst granskningar till granskare som inte har slutfört sin granskning.
