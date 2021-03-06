---
title: 'Självstudie: Azure Active Directory integrering med TalentLMS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TalentLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 86ce076df076c990b8bdefa9695805d34674a03f
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063503"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Självstudie: Azure Active Directory integrering med TalentLMS

I den här självstudien får du lära dig hur du integrerar TalentLMS med Azure Active Directory (Azure AD). När du integrerar TalentLMS med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till TalentLMS.
* Gör det möjligt för användarna att logga in automatiskt till TalentLMS med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TalentLMS behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* TalentLMS-aktiverad prenumeration med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* TalentLMS stöder **SP** -INITIERAd SSO

## <a name="add-talentlms-from-the-gallery"></a>Lägg till TalentLMS från galleriet

Om du vill konfigurera integreringen av TalentLMS i Azure AD måste du lägga till TalentLMS från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **TalentLMS** i sökrutan.
1. Välj **TalentLMS** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-talentlms"></a>Konfigurera och testa Azure AD SSO för TalentLMS

Konfigurera och testa Azure AD SSO med TalentLMS med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i TalentLMS.

Utför följande steg för att konfigurera och testa Azure AD SSO med TalentLMS:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera TALENTLMS SSO](#configure-talentlms-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa TalentLMS test User](#create-talentlms-test-user)** -om du vill ha en motsvarighet till B. Simon i TalentLMS som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **TalentLMS** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för TalentLMS-domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenant-name>.TalentLMSapp.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [TalentLMS client support team](https://www.talentlms.com/contact) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. Kopiera **Tumavtryck** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

7. I avsnittet **Konfigurera TalentLMS** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till TalentLMS.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **TalentLMS**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-talentlms-sso"></a>Konfigurera TalentLMS SSO

1. Logga in på din TalentLMS-företags webbplats som administratör i ett annat webbläsarfönster.

1. I avsnittet **konto & inställningar** klickar du på fliken **användare** .

    ![Konto & inställningar](./media/talentlms-tutorial/IC777296.png "Konto & inställningar")

1. Klicka på **Single Sign-On (SSO)**,

1. Gör följande i avsnittet Enkel inloggning:

    ![Enkel inloggning](./media/talentlms-tutorial/saml.png "för Aha!")

    a. Välj **SAML 2,0** i listan **typ av SSO-integration** .

    b. I text rutan **identitets leverantör (IdP)** klistrar du in värdet för **Azure AD-identifieraren**, som du har kopierat från Azure Portal.

    c. Klistra in **tumavtryck** -värdet från Azure Portal i text rutan **finger avtryck för certifikat** .

    d.  I text rutan **fjärrinloggnings-URL** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    e. I text rutan **fjärrinloggnings-URL** klistrar du in värdet för **URL för utloggning** som du har kopierat från Azure Portal.

    f. Fyll i följande:

    * I text rutan **TargetedID** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * I text rutan **förnamn** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * I text rutan **efter namn** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * I text rutan **e-post** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Klicka på **Spara**.

### <a name="create-talentlms-test-user"></a>Skapa TalentLMS test användare

Om du vill att Azure AD-användare ska kunna logga in på TalentLMS måste de tillhandahållas i TalentLMS. När det gäller TalentLMS är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **TalentLMS** -klient.

1. Klicka på **användare** och sedan på **Lägg till användare**.

1. Utför följande steg på dialog sidan **Lägg till användare** :

    ![Lägg till användare](./media/talentlms-tutorial/IC777299.png "Lägg till användare")  

    a. I text rutan för det **första namnet** anger du det första namnet på användaren som **Britta**.

    b. I textrutan **Efternamn** skriver du efternamnet på användaren: **Simon**.
 
    c. I textrutan **E-postadress** skriver du e-postadressen för användaren: `brittasimon\@contoso.com`.

    d. Klicka på **Lägg till användare**.

> [!NOTE]
> Du kan använda andra verktyg för TalentLMS av användar konton eller API: er som tillhandahålls av TalentLMS för att etablera Azure AD-användarkonton.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till TalentLMS-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till TalentLMS-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen TalentLMS i Mina appar omdirigeras det till TalentLMS-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat TalentLMS kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
