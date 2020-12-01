---
title: 'Självstudie: Konfigurera Templafy för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Templafy.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: dc4ef937a032a5156164eaee59908c9ee0319fdb
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350168"
---
# <a name="tutorial-configure-templafy-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Templafy för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Templafy och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Templafy.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En Templafy-klient](https://www.templafy.com/pricing/).
* Ett användar konto i Templafy med administratörs behörighet.

## <a name="assigning-users-to-templafy"></a>Tilldela användare till Templafy

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Templafy. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Templafy genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy"></a>Viktiga tips för att tilldela användare till Templafy

* Vi rekommenderar att en enda Azure AD-användare tilldelas Templafy för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Templafy måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-templafy-for-provisioning"></a>Konfigurera Templafy för etablering

Innan du konfigurerar Templafy för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på Templafy.

1. Logga in på din Templafy-administratörs konsol. Klicka på **Administration**.

    ![Templafy-administratörskonsolen](media/templafy-provisioning-tutorial/image00.png)

2. Klicka på **autentiseringsmetod**.

    ![Skärm bild av avsnittet Templafy administration med alternativet autentiseringsmetod inringat.](media/templafy-provisioning-tutorial/image01.png)

3. Kopiera värdet för **scim API-nyckel** . Det här värdet anges i fältet **hemlig token** på fliken etablering i ditt Templafy-program i Azure Portal.

    ![En skärm bild av S-tangenten i M A P I s.](media/templafy-provisioning-tutorial/image02.png)

## <a name="add-templafy-from-the-gallery"></a>Lägg till Templafy från galleriet

Om du vill konfigurera Templafy för automatisk användar etablering med Azure AD måste du lägga till Templafy från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till Templafy från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory** i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program** och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Templafy**, väljer **Templafy** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Templafy i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-templafy"></a>Konfigurera automatisk användar etablering till Templafy 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Templafy baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Templafy genom att följa anvisningarna i [självstudien om enkel inloggning med Templafy](templafy-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-templafy-in-azure-ad"></a>Konfigurera automatisk användar etablering för Templafy i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Templafy**.

    ![Templafy-länken i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://scim.templafy.com/scim` i **klient-URL**. Mata in **scim API-nyckelvärdet** som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Templafy. Om anslutningen Miss lyckas kontrollerar du att Templafy-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Templafy**.

    ![Templafy användar mappningar](media/templafy-provisioning-tutorial/usermapping.png)

9. Granska de användarattribut som synkroniseras från Azure AD till Templafy i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Templafy för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Templafy-användarattribut](media/templafy-provisioning-tutorial/userattribute.png)

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Templafy**.

    ![Templafy grupp mappningar](media/templafy-provisioning-tutorial/groupmapping.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Templafy i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Templafy för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Templafy grupp-attribut](media/templafy-provisioning-tutorial/groupattribute.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Templafy ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Templafy genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

    Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Templafy.

    Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
