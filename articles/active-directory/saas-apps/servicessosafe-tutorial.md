---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Services@SoSafe | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Services@SoSafe .
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: jeedes
ms.openlocfilehash: 3fe42dd37bda3bef549570c32018179b88d06957
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93364998"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicessosafe"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Services@SoSafe

I den här självstudien får du lära dig att integrera Services@SoSafe med Azure Active Directory (Azure AD). När du integrerar Services@SoSafe med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Services@SoSafe .
* Gör det möjligt för användarna att logga in automatiskt till Services@SoSafe med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Services@SoSafe enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Services@SoSafe stöder **SP-och IDP** -INITIERAd SSO
* Services@SoSafe stöder **just-in-Time** User-etablering

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.


## <a name="adding-servicessosafe-from-the-gallery"></a>Lägga till Services@SoSafe från galleriet

Om du vill konfigurera integreringen av Services@SoSafe i Azure AD måste du lägga till Services@SoSafe från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver Services@SoSafe du i sökrutan.
1. Välj Services@SoSafe från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-servicessosafe"></a>Konfigurera och testa Azure AD SSO för Services@SoSafe

Konfigurera och testa Azure AD SSO med Services@SoSafe hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Services@SoSafe .

Utför följande steg för att konfigurera och testa Azure AD SSO med Services@SoSafe :

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SERVICESSOSAFE SSO](#configure-servicessosafe-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa ServicesSoSafe test User](#create-servicessosafe-test-user)** -om du vill ha en motsvarighet till B. Simon i Services@SoSafe som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På Services@SoSafe sidan program integration på sidan Azure Portal letar du reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP** -initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://api.sosafe.de/v1/auth/saml/login/<TENANT_ID>`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL: en. Kontakta [ Services@SoSafe klient support teamet](mailto:support@sosafe.de) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I Services@SoSafe avsnittet Konfigurera, kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory** , väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn** -fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Exempelvis `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Services@SoSafe .

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du Services@SoSafe .
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-servicessosafe-sso"></a>Konfigurera ServicesSoSafe SSO

1. Logga in på webbplatsen som administratör i ett annat webbläsarfönster Services@SoSafe .

1. Klicka på **utökade data** och utför följande steg på följande sida.

    ![SAML-sida för utökade data](./media/servicessosafe-tutorial/saml-settings.png)

    a. Klistra in värdet för klient-ID-värdet i text rutan för **Azure-klient-ID** från Azure Portal.

    b. Öppna den nedladdade **certifikat (base64)** från Azure Portal i anteckningar och klistra in innehållet i text rutan **certifikat** .

    c. I rutan **inloggnings webb adress** klistrar du in URL-värdet för **inloggning** som du kopierade från Azure Portal.

    d. I rutan **Azure AD-identifierare** klistrar du in det **entitets-ID-** värde som du kopierade från Azure Portal.

    e. I rutan **Logga in URL** klistrar du in URL-värdet för **utloggning** som du kopierade från Azure Portal.

    f. Klicka på **Spara**

### <a name="create-servicessosafe-test-user"></a>Skapa ServicesSoSafe test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Services@SoSafe . Services@SoSafe stöder just-in-Time User-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Services@SoSafe skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

1. Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Services@SoSafe inloggnings-URL där du kan starta inloggnings flödet.  

1. Gå till Services@SoSafe inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till den Services@SoSafe för vilken du konfigurerar SSO 

Du kan också använda Microsoft Access-panelen för att testa programmet i vilket läge som helst. När du klickar på panelen Services@SoSafe i åtkomst panelen, om det kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du loggas in automatiskt på den Services@SoSafe för vilken du konfigurerar SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du Services@SoSafe har konfigurerat kan du framtvinga kontroll över sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


