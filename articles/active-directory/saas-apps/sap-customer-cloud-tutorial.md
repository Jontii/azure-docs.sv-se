---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SAP-moln för kund | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Cloud for Customer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: jeedes
ms.openlocfilehash: f9fd458ea19fa0dad2f630f94a67d5e1db96cee3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543320"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SAP-moln för kund

I den här självstudien får du lära dig hur du integrerar SAP Cloud för kunden med Azure Active Directory (Azure AD). När du integrerar SAP Cloud för kunden med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAP-molnet för kunden.
* Gör det möjligt för användarna att logga in automatiskt till SAP-molnet för kunder med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SAP-moln för en aktive rad prenumeration på kund enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SAP Cloud for Customer stöder **SP**-initierad enkel inloggning

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Lägga till SAP Cloud for Customer från galleriet

Om du vill konfigurera integreringen av SAP Cloud for Customer i Azure AD måste du lägga till SAP Cloud for Customer från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **SAP Cloud för kunden** i sökrutan.
1. Välj **SAP-moln för kund** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-for-customer"></a>Konfigurera och testa enkel inloggning med Azure AD för SAP-molnet för kunden

Konfigurera och testa Azure AD SSO med SAP-moln för kunder som använder en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SAP-molnet för kunden.

Om du vill konfigurera och testa Azure AD SSO med SAP-molnet för kunden slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SAP Cloud för Customer SSO](#configure-sap-cloud-for-customer-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa SAP-moln för kund test användare](#create-sap-cloud-for-customer-test-user)** – om du vill ha en motsvarighet till B. Simon i SAP-molnet för kunden som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **SAP-moln för** integrering av kund program och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<server name>.crm.ondemand.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Skaffa dess värden genom att kontakta [supportteamet för SAP Cloud for Customer-klienten](https://www.sap.com/about/agreements.sap-cloud-services-customers.html). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. SAP-moln för kund program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade attribut mappningar i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

1. Gör följande i avsnittet **Användarattribut** i dialogrutan **Användarattribut och anspråk**:

    a. Öppna dialogrutan **Hantera användaranspråk** genom att klicka på **redigeringsikonen**.

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Välj **Transformering** som **källa**.

    c. Välj **ExtractMailPrefix()** i listan **Transformering**.

    d. Välj det användarattribut som du vill använda för din implementering i listan **Parameter 1**.
    Om du t.ex. vill använda EmployeeID som unikt användar-ID och du har lagrat attributvärdet i ExtensionAttribute2 väljer du sedan user.extensionattribute2.

    e. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera SAP-moln för kund** , kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SAP-molnet för kunden.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **SAP-moln för kund**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-sap-cloud-for-customer-sso"></a>Konfigurera SAP-moln för kund-SSO

1. Öppna ett nytt webbläsarfönster och logga in på ditt SAP-moln för kund företags webbplats som administratör.

2. Från menyn till vänster klickar du på **identitet leverantörer**   >  **företags identitets leverantörer**  >  **Lägg till** och i popup-fönstret Lägg till ID-providern som **Azure AD**, klickar på **Spara** och sedan på **SAML 2,0-konfiguration**.

    ![SAP-konfiguration](./media/sap-customer-cloud-tutorial/configure01.png)

3. I **konfigurations avsnittet för SAML 2,0** utför du följande steg:

    ![SAP-konfiguration](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Klicka på **Bläddra** för att ladda upp XML-filen för federationsmetadata som du har laddat ned från Azure Portal.

    b. När XML-filen har laddats upp kommer värdena nedan automatiskt att fyllas i automatiskt och sedan klicka på **Spara**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Skapa en testanvändare för SAP Cloud for Customer

Om du vill att Azure AD-användare ska kunna logga in på SAP-molnet för kunden måste de tillhandahållas i SAP-molnet för kunden. I SAP Cloud för kunden är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på SAP Cloud för kunden som en säkerhets administratör.

2. På vänster sida av menyn klickar du på **användare & auktorisering**   >  **användar hantering**  >  **Lägg till användare**.

    ![SAP-konfiguration](./media/sap-customer-cloud-tutorial/configure03.png)

3. Utför följande steg i avsnittet **Lägg till ny användare** :

    ![SAP-konfiguration](./media/sap-customer-cloud-tutorial/configure04.png)

    a. I text rutan **förnamn** anger du namnet på användaren, t. ex. **B**.

    b. I text rutan **efter namn** anger du namnet på användaren som **Simon**.

    c. I text rutan **e-postadress** anger du e-postadressen till användaren `B.Simon@contoso.com` .

    d. Ange namnet på den användare som **B. Simon**i text rutan **inloggnings namn** .

    e. Välj **användar typ** enligt ditt krav.

    f. Välj alternativ för **konto aktivering** enligt ditt krav.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på SAP Cloud for Customer-panelen i åtkomstpanelen bör du automatiskt loggas in på SAP Cloud for Customer som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SAP Cloud för kunden med Azure AD](https://aad.portal.azure.com/)

