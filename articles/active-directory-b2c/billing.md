---
title: Fakturerings modell för Azure Active Directory B2C
description: Lär dig om fakturerings modellen för Azure AD B2C's Monthly Active Users (MAU) och hur du aktiverar fakturering för en särskild Azure-prenumeration.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 10/25/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: f9adf6ce4559234eec74c92f09aa752eb1f9ab51
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2020
ms.locfileid: "89177337"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Fakturerings modell för Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) används faktureras till en länkad Azure-prenumeration och använder en månatlig aktiv MAU-fakturerings modell. Lär dig hur du länkar en Azure AD B2C resurs till en prenumeration och hur MAU-fakturerings modellen fungerar i följande avsnitt.

> [!IMPORTANT]
> Den här artikeln innehåller ingen pris information. Den senaste informationen om fakturering och prissättning för användning finns [Azure Active Directory B2C prissättning](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="monthly-active-users-mau-billing"></a>Månatliga aktiva användare (MAU) fakturering

Azure AD B2C fakturering mäts på antalet unika användare med autentisering under en kalender månad, som kallas för månatliga aktiva användare (MAU) fakturering.

Med början **01 November 2019**faktureras alla nyskapade Azure AD B2C-klienter per månad aktiva användare (MAU). Befintliga klienter som är [länkade till en prenumeration](#link-an-azure-ad-b2c-tenant-to-a-subscription) på eller efter 01 november 2019 faktureras per månad aktiva användare (MAU).

Om du har en befintlig Azure AD B2C klient som var länkad till en prenumeration före 01 november 2019, kan du välja att göra något av följande:

* Uppgradera till MAU-fakturerings modellen (Monthly Active Users) eller
* Håll koll på fakturerings modellen per autentisering

### <a name="upgrade-to-monthly-active-users-billing-model"></a>Uppgradera till månatliga aktiva användare fakturerings modell

Azure-Prenumerationens ägare som har administrativ åtkomst till den Azure AD B2C resursen kan växla till fakturerings modellen för MAU. Fakturerings alternativ konfigureras i din Azure AD B2C-resurs.

MAU-faktureringen växlar till månatliga aktiva användare () kan inte **ångras**. När du har konverterat en Azure AD B2C resurs till den MAU fakturerings modellen kan du inte återställa resursen till fakturerings modellen per autentisering.

Så här gör du för att växla till MAU fakturering för en befintlig Azure AD B2C-resurs:

1. Logga in på [Azure Portal](https://portal.azure.com) som prenumerations ägare.
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den Azure AD B2C katalog som du vill uppgradera till Mau fakturering.<br/>
    ![Katalog-och prenumerations filter i Azure Portal](./media/billing/portal-mau-01-select-b2c-directory.png)
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. På sidan **Översikt** i Azure AD B2C klient organisationen väljer du länken under **resurs namn**. Du dirigeras till Azure AD B2C resursen i din Azure AD-klient.<br/>
    ![Azure AD B2C resurs länk markerad i Azure Portal](./media/billing/portal-mau-02-b2c-resource-link.png)
1. På sidan **Översikt** i Azure AD B2C resurs, under **fakturerbara enheter**, väljer du länken **per autentisering (ändra till Mau)** .<br/>
    ![Ändra till MAU-länken är markerad i Azure Portal](./media/billing/portal-mau-03-change-to-mau-link.png)
1. Välj **Bekräfta** för att slutföra uppgraderingen till Mau-fakturering.<br/>
    ![MAU fakturerings bekräftelse dialog ruta i Azure Portal](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Vad ska förväntas när du övergår till MAU debitering från fakturering per autentisering

MAU-baserad avläsning aktive ras så snart du, prenumerationen/resurs ägaren, bekräftar ändringen. Din månads faktura visar de enheter med autentisering som faktureras tills ändringen och nya enheter av MAU börjar med ändringen.

Användarna är inte dubbelt räknade under över gångs månaden. Unika aktiva användare som autentiseras innan ändringen debiteras per autentisering under en kalender månad. Samma användare ingår inte i MAU-beräkningen för resten av prenumerationens fakturerings period. Exempel:

* Contoso B2C-klienten har 1 000 användare. 250 användare är aktiva under en månad. Prenumerations administratören ändras från per autentisering till månatliga aktiva användare (MAU) den 10: a i månaden.
* Faktureringen för 1 – 10 – 10 faktureras med modellen per autentisering.
  * Om 100 användare loggar in under den här perioden (1st-tionde) är dessa användare märkta som *betalda för månaden*.
* Fakturering från den tionde (den effektiva tiden för över gång) debiteras enligt MAU-priset.
  * Om ytterligare 150 användare loggar in under denna tid (10 – 30) debiteras bara ytterligare 150.
  * Den fortsatta aktiviteten för de första 100 användarna påverkar inte faktureringen för resten av kalender månaden.

Under över gångens fakturerings period kommer prenumerations ägaren troligen att se poster för båda metoderna (per autentisering och per-MAU) i fakturerings instruktionen för Azure-prenumerationen:

* En post för användningen fram till datum/tid för ändring som reflekterar per autentisering.
* En post för användningen efter den ändring som visar månatliga aktiva användare (MAU).

Den senaste informationen om fakturering och priser för användning för Azure AD B2C finns i [Azure Active Directory B2C prissättning](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Länka en Azure AD B2C-klient till en prenumeration

Användnings kostnader för Azure Active Directory B2C (Azure AD B2C) faktureras till en Azure-prenumeration. När en Azure AD B2C-klient skapas måste klient organisationens administratör uttryckligen länka Azure AD B2C klient till en Azure-prenumeration.

Prenumerations länken uppnås genom att skapa en Azure AD B2C- *resurs* i Azure-målets Azure-prenumeration. Flera Azure AD B2C-resurser kan skapas i en enda Azure-prenumeration, tillsammans med andra Azure-resurser som virtuella datorer, lagrings konton och Logic Apps. Du kan se alla resurser i en prenumeration genom att gå till den Azure Active Directory (Azure AD) som prenumerationen är associerad med.

En prenumeration som är länkad till en Azure AD B2C-klient kan användas för fakturering av Azure AD B2C användning eller andra Azure-resurser, inklusive ytterligare Azure AD B2C resurser. Det kan inte användas för att lägga till andra Azure licensbaserade tjänster eller Office 365-licenser inom Azure AD B2C klient organisationen.

### <a name="prerequisites"></a>Krav

* [Azure-prenumeration](https://azure.microsoft.com/free/)
* [Azure AD B2C klient](tutorial-create-tenant.md) som du vill länka till en prenumeration
  * Du måste vara klient administratör
  * Klienten får inte redan vara länkad till en prenumeration

### <a name="create-the-link"></a>Skapa länken

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller den Azure-prenumeration som du vill använda (*inte* den katalog som innehåller Azure AD B2C klienten).
1. Välj **skapa en resurs**, ange `Active Directory B2C` i fältet **Sök i Marketplace** och välj sedan **Azure Active Directory B2C**.
1. Välj **Skapa**
1. Välj **Länka en befintlig Azure AD B2C-klient till min Azure-prenumeration**.
1. Välj en **Azure AD B2C klient** i list rutan. Endast innehavare som du är global administratör för och som inte redan är länkade till en prenumeration visas. Fältet **Azure AD B2C resurs namn** fylls i med domän namnet för den Azure AD B2C klient som du väljer.
1. Välj en aktiv Azure- **prenumeration** som du är administratör för.
1. Under **resurs grupp**väljer du **Skapa ny**och anger sedan **resurs gruppens plats**. Resurs grupps inställningarna här påverkar inte Azure AD B2C klient plats, prestanda eller fakturerings status.
1. Välj **Skapa**.
    ![Sidan Azure AD B2C resurs skapande i Azure Portal](./media/billing/portal-01-create-b2c-resource-page.png)

När du har slutfört de här stegen för en Azure AD B2C klient faktureras din Azure-prenumeration i enlighet med dina Azure Direct-eller Enterprise-avtal-uppgifter, om så är tillämpligt.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Hantera dina Azure AD B2C klient resurser

När du har skapat Azure AD B2C resursen i en Azure-prenumeration bör du se en ny resurs av typen "B2C Tenant" med dina andra Azure-resurser.

Du kan använda den här resursen för att:

* Gå till prenumerationen för att granska fakturerings information
* Hämta Azure AD B2C innehavarens klient-ID i GUID-format
* Gå till din Azure AD B2C-klient
* Skicka en support förfrågan
* Flytta din Azure AD B2C klient resurs till en annan Azure-prenumeration eller resurs grupp

![Sidan B2C resurs inställningar i Azure Portal](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>Regionala begränsningar

Om du har fastställt regionala begränsningar för att skapa Azure-resurser i din prenumeration kan denna begränsning förhindra att du skapar den Azure AD B2C resursen.

Minska det här problemet genom att minska dina regionala begränsningar.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Prenumerationer på Azure Cloud solution providers (CSP)

Azure Cloud solution providers (CSP)-prenumerationer stöds i Azure AD B2C. Funktionerna är tillgängliga med API: er eller Azure Portal för Azure AD B2C och för alla Azure-resurser. Administratörer av CSP-prenumerationen kan länka, flytta och ta bort relationer med Azure AD B2C som gjorts med andra Azure-resurser.

Hanteringen av Azure AD B2C med hjälp av rollbaserad åtkomst kontroll påverkas inte av associationen mellan Azure AD B2C klienten och en Azure CSP-prenumeration. Rollbaserad åtkomst kontroll uppnås med hjälp av klientbaserade roller, inte prenumerations-baserade roller.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Ändra Azure AD B2C klient fakturerings prenumeration

### <a name="move-using-azure-resource-manager"></a>Flytta med Azure Resource Manager

Azure AD B2C klienter kan flyttas till en annan prenumeration med Azure Resource Manager om käll-och mål prenumerationer finns inom samma Azure Active Directory klient organisation.

Information om hur du flyttar Azure-resurser som din Azure AD B2C-klient till en annan prenumeration finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Innan du börjar flytta måste du läsa hela artikeln för att kunna förstå begränsningarna och kraven för en sådan flytt. Förutom instruktioner för att flytta resurser innehåller den viktig information, till exempel en check lista för förflyttning och hur du verifierar flytt åtgärden.

### <a name="move-by-un-linking-and-re-linking"></a>Flytta genom att ångra länkning och omlänka

Om käll-och mål prenumerationerna är kopplade till olika Azure Active Directory klienter kan du inte utföra flytten via Azure Resource Manager enligt beskrivningen ovan. Du kan dock fortfarande få samma resultat genom att avbryta länkningen av Azure AD B2C klient från käll prenumerationen och länka om den till mål prenumerationen. Den här metoden är säker eftersom det enda objekt du tar bort är *fakturerings länken*, inte själva Azure AD B2C själva klienten. Ingen av användarna, apparna, användar flödena, etc. kommer att påverkas.

1. I själva katalogen Azure AD B2C kan du [bjuda in en gäst användare](user-overview.md#guest-user) från Azure AD-klienten (det som är mål Azure-prenumerationen länkad till) och se till att användaren har rollen **Global administratör** i Azure AD B2C.
1. Gå till *Azure-resursen* som representerar Azure AD B2C i din käll Azure-prenumeration enligt beskrivningen i avsnittet [Hantera Azure AD B2C klient resurser](#manage-your-azure-ad-b2c-tenant-resources) ovan. Växla inte till den faktiska Azure AD B2C-klienten.
1. Klicka på knappen **ta bort** på sidan **Översikt** . Detta tar *inte* bort relaterade Azure AD B2C klientens användare eller program. Den tar bara bort fakturerings länken från käll prenumerationen.
1. Logga in på Azure Portal med det användar konto som har lagts till som administratör i Azure AD B2C i steg 1. Gå sedan till mål Azure-prenumerationen, som är länkad till mål Azure Active Directory klient organisationen. 
1. Återupprätta fakturerings länken i mål prenumerationen genom att följa anvisningarna för att [Skapa länken](#create-the-link) ovan.
1. Din Azure AD B2C-resurs har nu flyttats till mål Azure-prenumerationen (länkad till mål Azure Active Directory) och debiteras via den här prenumerationen.

## <a name="next-steps"></a>Nästa steg

Den senaste pris informationen finns i [Azure Active Directory B2C prissättning](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
