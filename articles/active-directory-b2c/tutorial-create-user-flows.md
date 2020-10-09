---
title: Självstudie – Skapa användar flöden – Azure Active Directory B2C
description: Följ den här självstudien för att lära dig hur du skapar användar flöden i Azure Portal för att aktivera registrering, inloggning och användar profil redigering för dina program i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 041b1766ae6a64f51d922de128ef316cc0ed8260
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87922177"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Självstudie: skapa användar flöden i Azure Active Directory B2C

I dina program kan du ha [användar flöden](user-flow-overview.md) som gör det möjligt för användare att registrera, logga in eller hantera sina profiler. Du kan skapa flera användar flöden av olika typer i din Azure Active Directory B2C-klient (Azure AD B2C) och använda dem i dina program efter behov. Användar flöden kan återanvändas i olika program.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa ett användar flöde för registrering och inloggning
> * Skapa ett användarflöde för profilredigering
> * Skapa ett användarflöde för återställning av lösenord

Den här självstudien visar hur du skapar vissa rekommenderade användar flöden med hjälp av Azure Portal. Om du vill ha information om hur du konfigurerar ett ROPC-flöde (Resource Owner Password Credential) i programmet, se [Konfigurera flödet för autentiseringsuppgifter för resurs ägare i Azure AD B2C](configure-ropc.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!IMPORTANT]
> Vi har ändrat hur vi refererar till användar flödes versioner. Tidigare erbjöd vi v1 (produktions klara) versioner och V 1.1 och v2 (för hands version). Nu har vi konsoliderat användar flöden till **Rekommenderad** (nästa generations för hands version) och **standard** versioner (allmänt tillgängliga). Alla tidigare förhands gransknings användar flöden för V 1.1 och v2 finns på en sökväg till utfasningen den **1 augusti 2021**. Mer information finns [i användar flödes versioner i Azure AD B2C](user-flow-versions.md).

## <a name="prerequisites"></a>Krav

[Registrera dina program](tutorial-register-applications.md) som är en del av de användar flöden som du vill skapa.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Skapa ett användar flöde för registrering och inloggning

Användar flödet för registrering och inloggning hanterar både registrering och inloggnings upplevelser med en enda konfiguration. Användare av ditt program är i rätt sökväg beroende på kontexten.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.

    ![B2C-klient, katalog och prenumerations fönstret Azure Portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **principer**väljer du **användar flöden**och väljer sedan **nytt användar flöde**.

    ![Sidan användar flöden i portalen med knappen nytt användar flöde markerat](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. På sidan **skapa ett användar flöde** väljer du användar flödet **Registrera och logga** in.

    ![Välj sidan för användar flöde med registrering och inloggnings flöde markerat](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. Under **Välj en version**väljer du **rekommenderas**och väljer sedan **skapa**. ([Läs mer](user-flow-versions.md) om användar flödes versioner.)

    ![Sidan skapa användar flöde i Azure Portal med egenskaper markerat](./media/tutorial-create-user-flows/select-version.png)

1. Ange ett **Namn** för användarflödet. Till exempel *signupsignin1*.
1. För **identitets leverantörer**väljer du **e-postregistrering**.
1. För användarattribut **och anspråk**väljer du de anspråk och attribut som du vill samla in och skicka från användaren under registreringen. Välj till exempel **Visa mer**och välj sedan attribut och anspråk för **land/region**, **visnings namn**och **post nummer**. Klicka på **OK**.

    ![Sidan för attribut och val av anspråk med tre anspråk valda](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Klicka på **skapa** för att lägga till användar flödet. Ett prefix för *B2C_1* anpassningsprefix automatiskt till namnet.

### <a name="test-the-user-flow"></a>Testa användar flödet

1. Välj det användar flöde som du skapade för att öppna sidan Översikt och välj sedan **Kör användar flöde**.
1. För **program**väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde**och välj sedan **Registrera dig nu**.

    ![Sidan kör användar flöde i portalen med knappen Kör användar flöde markerat](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Ange en giltig e-postadress, klicka på **Skicka verifierings kod**, ange den verifierings kod som du får och välj sedan **verifiera kod**.
1. Ange ett nytt lösen ord och bekräfta lösen ordet.
1. Välj land och region, ange det namn som du vill visa, ange ett post nummer och klicka sedan på **skapa**. Token returneras till `https://jwt.ms` och ska visas för dig.
1. Nu kan du köra användar flödet igen och du bör kunna logga in med det konto som du skapade. Den returnerade token innehåller de anspråk som du har valt land/region, namn och post nummer.

## <a name="create-a-profile-editing-user-flow"></a>Skapa ett användarflöde för profilredigering

Om du vill att användarna ska kunna redigera sin profil i ditt program använder du en profil som redigerar användar flöde.

1. På menyn på sidan för Azure AD B2C klient översikt väljer du **användar flöden**och väljer sedan **nytt användar flöde**.
1. På sidan **skapa ett användar flöde** väljer du **profil redigering** användar flöde. 
1. Under **Välj en version**väljer du **rekommenderas**och väljer sedan **skapa**.
1. Ange ett **Namn** för användarflödet. Till exempel *profileediting1*.
1. För **identitets leverantörer**väljer du **lokal konto inloggning**.
2. För **användarattribut**väljer du de attribut som du vill att kunden ska kunna redigera i profilen. Välj till exempel **Visa mer**och välj sedan både attribut och anspråk för **visnings namn** och **befattning**. Klicka på **OK**.
3. Klicka på **skapa** för att lägga till användar flödet. Ett prefix för *B2C_1* läggs automatiskt till i namnet.

### <a name="test-the-user-flow"></a>Testa användar flödet

1. Välj det användar flöde som du skapade för att öppna sidan Översikt och välj sedan **Kör användar flöde**.
1. För **program**väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde**och logga sedan in med det konto som du skapade tidigare.
1. Nu har du möjlighet att ändra visnings namn och befattning för användaren. Klicka på **Fortsätt**. Token returneras till `https://jwt.ms` och ska visas för dig.

## <a name="create-a-password-reset-user-flow"></a>Skapa ett användarflöde för återställning av lösenord

Om du vill göra det möjligt för användare av ditt program att återställa sitt lösen ord använder du ett användar flöde för återställning av lösen ord.

1. I menyn Azure AD B2C klient översikt väljer du **användar flöden**och väljer sedan **nytt användar flöde**.
1. På sidan **skapa ett användar flöde** väljer du **lösen ords återställning** användar flöde. 
1. Under **Välj en version**väljer du **rekommenderas**och väljer sedan **skapa**.
1. Ange ett **Namn** för användarflödet. Till exempel *passwordreset1*.
1. För **identitets leverantörer**aktiverar du **Återställ lösen ord med e-postadress**.
2. Under program anspråk klickar du på **Visa fler** och väljer de anspråk som du vill ska returneras i de autentiseringstoken som skickas tillbaka till programmet. Välj till exempel **Användarobjekt-id**.
3. Klicka på **OK**.
4. Klicka på **skapa** för att lägga till användar flödet. Ett prefix för *B2C_1* läggs automatiskt till i namnet.

### <a name="test-the-user-flow"></a>Testa användar flödet

1. Välj det användar flöde som du skapade för att öppna sidan Översikt och välj sedan **Kör användar flöde**.
1. För **program**väljer du det webb program som heter *webapp1* som du tidigare har registrerat. **Svars-URL: en** ska visas `https://jwt.ms` .
1. Klicka på **Kör användar flöde**, verifiera e-postadressen för det konto som du skapade tidigare och välj **Fortsätt**.
1. Nu har du möjlighet att ändra lösen ordet för användaren. Ändra lösen ordet och välj **Fortsätt**. Token returneras till `https://jwt.ms` och ska visas för dig.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa ett användar flöde för registrering och inloggning
> * Skapa ett användarflöde för profilredigering
> * Skapa ett användarflöde för återställning av lösenord

Nu kan du läsa mer om att lägga till identitets leverantörer i dina program för att möjliggöra användar inloggning med leverantörer som Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft eller Twitter.

> [!div class="nextstepaction"]
> [Lägg till identitets leverantörer i dina program >](tutorial-add-identity-providers.md)
