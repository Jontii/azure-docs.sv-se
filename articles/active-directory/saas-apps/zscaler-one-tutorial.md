---
title: 'Självstudie: Azure Active Directory integration med Zscaler One | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: 3a9279189e3767b035256726a1da53b9edc8392f
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608994"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Självstudie: Azure Active Directory integrering med Zscaler One

I den här självstudien får du lära dig att integrera Zscaler en med Azure Active Directory (Azure AD).
Genom att integrera Zscaler en med Azure AD får du följande fördelar:

* Du kan styra Azure AD som har åtkomst till Zscaler.
* Du kan göra det möjligt för användarna att logga in automatiskt för att Zscaler en (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Zscaler ett behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Zscaler en aktive rad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zscaler One stöder **SP** -INITIERAd SSO

* Zscaler en stöder **just-in-Time** User-etablering

## <a name="adding-zscaler-one-from-the-gallery"></a>Lägga till Zscaler från galleriet

Om du vill konfigurera integreringen av Zscaler i Azure AD måste du lägga till Zscaler från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Zscaler från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Zscaler ett**, väljer **Zscaler ett** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Zscaler en i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med Zscaler, baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Zscaler.

Om du vill konfigurera och testa enkel inloggning med Zscaler i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Zscaler en enkel inloggning](#configure-zscaler-one-single-sign-on)** för att konfigurera de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Zscaler en test användare](#create-zscaler-one-test-user)** – om du vill ha en motsvarighet till Britta Simon i Zscaler som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med Zscaler One:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Zscaler One** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Zscaler en enkel inloggnings information för en domän och URL](common/sp-signonurl.png)

    I text rutan **inloggnings-URL** anger du den URL som används av dina användare för att logga in på ditt Zscaler ett program.

    > [!NOTE]
    > Du uppdaterar värdet med den faktiska Sign-On-URL: en. Kontakta [Zscaler ett kund support team](https://www.zscaler.com/company/contact) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Zscaler ett program förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon för att öppna dialog rutan **användarattribut** .

    ![Skärm bild som visar användarattribut med redigerings ikonen vald.](common/edit-attribute.png)

6. Förutom över, förväntar Zscaler ett program att fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:
    
    | Name | Källattribut |
    | ---------| ------------ |
    | memberOf | user.assignedroles |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![Skärm bild som visar användar anspråk med alternativet att lägga till ett nytt anspråk.](common/new-save-attribute.png)

    ![Skärm bild som visar dialog rutan hantera användar anspråk där du kan ange de värden som beskrivs.](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.
    
    f. Klicka på **Spara**.

    > [!NOTE]
    > Klicka [här](../develop/active-directory-enterprise-app-role-management.md) för att få veta hur du konfigurerar en Roll i Azure AD

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

8. På sidan **Konfigurera Zscaler** , kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-zscaler-one-single-sign-on"></a>Konfigurera Zscaler en enda Sign-On

1. Om du vill automatisera konfigurationen i Zscaler måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **installations Zscaler** , som leder dig till Zscaler ett program. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Zscaler. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera SSO](common/setup-sso.png)

3. Om du vill konfigurera Zscaler en manuellt öppnar du ett nytt webbläsarfönster och loggar in på Zscaler en företags webbplats som administratör och utför följande steg:

4. Gå till **Administration > Autentisering > Autentiseringsinställningar** och utför följande steg:
   
    ![Skärm bild som visar Zscaler för en webbplats med steg som beskrivs.](./media/zscaler-one-tutorial/ic800206.png "Administration")

    a. Under autentiseringstyp väljer du **SAML**.

    b. Klicka på **Konfigurera SAML**.

5. I fönstret **Redigera SAML** utför du följande steg och klickar på Spara.  
            
    ![Hantera användare & autentisering](./media/zscaler-one-tutorial/ic800208.png "Hantera användare & autentisering")
    
    a. I textrutan för **URL:en för SAML-portalen** klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    b. I textrutan för **attribut för inloggningsnamn** anger du **NameID**.

    c. Klicka på **Ladda upp** för att ladda upp Azure SAML-signeringscertifikatet som du har laddat ned från Azure-portalen i det **offentliga SSL-certifikatet**.

    d. Reglera **Enable SAML Auto-Provisioning** (Aktivera automatisk etablering av SAML).

    e. I textrutan för **användarens visningsnamnattribut** anger du **displayName** om du vill aktivera automatisk SAML-etablering för attributet displayName.

    f. I textrutan **User Display Name Attribute** (Gruppnamnattribut) anger du **memberOf** om du vill aktivera automatisk SAML-etablering för attributet memberOf.

    ex. I textrutan för **avdelningsnamnattribut** anger du **department** om du vill aktivera automatisk SAML-etablering för department-attribut.

    h. Klicka på **Spara**.

6. I dialogrutan **Konfigurera användarautentisering** utför du följande steg:

    ![Skärm bild som visar dialog rutan Konfigurera användarautentisering med aktivera valt.](./media/zscaler-one-tutorial/ic800207.png)

    a. Hovra över menyn **Aktivering** längst ned till vänster.

    b. Klicka på **Aktivera**.

## <a name="configuring-proxy-settings"></a>Konfigurera proxyinställningar
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>För att konfigurera proxyinställningar i Internet Explorer

1. Starta **Internet Explorer**.

2. Välj **Internetalternativ** i menyn **Verktyg** för att öppna dialogrutan **Internetalternativ**.   

    ![Internet alternativ](./media/zscaler-one-tutorial/ic769492.png "Internet alternativ")

3. Klicka på fliken **Anslutningar**.   

    ![Anslutningar](./media/zscaler-one-tutorial/ic769493.png "Anslutningar")

4. Klicka på **LAN-inställningar** för att öppna dialogrutan **LAN-inställningar**.

5. I avsnittet Proxyserver utför du följande steg:   
   
    ![Proxyserver](./media/zscaler-one-tutorial/ic769494.png "Proxyserver")

    a. Välj **Use a proxy server for your LAN** (Använd en proxyserver för ditt lokala nätverk).

    b. I text rutan adress skriver du **Gateway. Zscaler-One.net**.

    c. I textrutan Port skriver du **80**.

    d. Välj **Bypass proxy server for local addresses** (Kringgå proxyservern för lokala adresser).

    e. Klicka på **OK** för att stänga dialogrutan **Local Area Network (LAN) Settings** (Inställningar för lokalt nätverk (LAN)).

7. Klicka på **OK** för att stänga dialogrutan **Internetalternativ**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** brittasimon@yourcompanydomain.extension . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Zscaler One.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **Zscaler ett**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Zscaler ett**.

    ![Zscaler en länk i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du användaren **Britta Simon** i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

    ![Skärm bild som visar dialog rutan användare och grupper där du kan välja en användare.](./media/zscaler-one-tutorial/tutorial_zscalerone_users.png)

6. I dialogrutan **Välj roll** väljer du lämplig användarroll i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

    ![Skärm bild som visar dialog rutan Välj roll där du kan välja en användar roll.](./media/zscaler-one-tutorial/tutorial_zscalerone_roles.png)

7. I dialogrutan **Lägg till tilldelning** väljer du knappen **Tilldela**.

    ![Skärm bild som visar dialog rutan Lägg till tilldelning där du kan välja tilldela.](./media/zscaler-one-tutorial/tutorial_zscalerone_assign.png)

### <a name="create-zscaler-one-test-user"></a>Skapa Zscaler en test användare

I det här avsnittet skapas en användare som heter Britta Simon i Zscaler One. Zscaler en stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Zscaler en, skapas en ny efter autentiseringen.

>[!Note]
>Om du behöver skapa en användare manuellt kontaktar du [Zscaler ett support team](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Zscaler en panel på åtkomst panelen, bör du loggas in automatiskt på den Zscaler som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)