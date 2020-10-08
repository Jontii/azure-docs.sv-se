---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Freshworks | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Freshworks.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.openlocfilehash: c953297d4e66f737250451b9a5f42ce7f45dd2e4
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91821261"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Freshworks

I den här självstudien får du lära dig hur du integrerar Freshworks med Azure Active Directory (Azure AD). När du integrerar Freshworks med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Freshworks.
* Gör det möjligt för användarna att logga in automatiskt till Freshworks med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Freshworks för enkel inloggning (SSO) aktive rad.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Freshworks stöder **SP** -INITIERAd SSO

## <a name="adding-freshworks-from-the-gallery"></a>Lägga till Freshworks från galleriet

Om du vill konfigurera integreringen av Freshworks i Azure AD måste du lägga till Freshworks från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Freshworks** i sökrutan.
1. Välj **Freshworks** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshworks"></a>Konfigurera och testa enkel inloggning med Azure AD för Freshworks

Konfigurera och testa Azure AD SSO med Freshworks med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Freshworks.

Om du vill konfigurera och testa Azure AD SSO med Freshworks, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera FRESHWORKS SSO](#configure-freshworks-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Freshworks test User](#create-freshworks-test-user)** -om du vill ha en motsvarighet till B. Simon i Freshworks som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Freshworks** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.freshworks.com/login`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Freshworks client support team](mailto:support@freshworks.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Om du vill ändra **signerings**   alternativen enligt ditt krav klickar **Edit**   du på knappen Redigera för att öppna dialog rutan **certifikat för SAML-signering**   .

     ![image](common/edit-certificate.png)

     ![Skärm bild som visar dialog rutan "S A M L signerings certifikat" med knappen "redigera" markerad.](./media/freshworks-tutorial/response.png)

    a. Välj **signera SAML-svar** som **signerings alternativ**.

    b. Klicka på **Spara**.

1. I avsnittet **Konfigurera Freshworks** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Exempelvis `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Freshworks.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Freshworks**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-freshworks-sso"></a>Konfigurera Freshworks SSO

1. Öppna ett nytt webbläsarfönster och logga in på din Freshworks-företags webbplats som administratör och utför följande steg:

2. På menyn till vänster på menyn klickar du på **säkerhets**   ikon och kontrollerar sedan alternativet **enkel inloggning** och väljer **SAML SSO** under **autentiseringsmetoder**.

    ![Skärm bild som visar avsnittet "metoder för säkerhets autentisering" med alternativet "enkel inloggning" aktiverat och "S A M L s O" vald.](./media/freshworks-tutorial/configure01.png)

3. I avsnittet **enkel inloggning** utför du följande steg:

    ![Freshworks-konfiguration](./media/freshworks-tutorial/configure02.png)

    a. Klicka på **Kopiera** för att kopiera **enhets-ID: t för service providern (SP)** för din instans och klistra in den i **ID (enhets-ID)** text ruta i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    b. I det **entitets-ID som tillhandahålls av** text rutan IDP klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    c. I text rutan **URL för SAML SSO** klistrar du in värdet för **inloggnings-URL** : en som du har kopierat från Azure Portal.

    d. Öppna det Base64-kodade certifikatet i anteckningar, kopiera dess innehåll och klistra in det i text rutan **säkerhetscertifikat** .

    e. Klicka på **Spara**.

### <a name="create-freshworks-test-user"></a>Skapa Freshworks test användare

I det här avsnittet skapar du en användare som heter B. Simon i Freshworks. Arbeta med [Freshworks-klientens support team](mailto:support@freshworks.com) för att lägga till användare i Freshworks-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Freshworks på åtkomst panelen, bör du loggas in automatiskt på den Freshworks som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Freshworks med Azure AD](https://aad.portal.azure.com/)

