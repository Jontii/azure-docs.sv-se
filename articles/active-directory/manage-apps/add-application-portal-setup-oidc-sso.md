---
title: 'Snabb start: Konfigurera OIDC-baserad enkel inloggning (SSO) för ett program i din Azure Active Directory (Azure AD)-klient'
description: Den här snabb starten vägleder dig genom processen att konfigurera OIDC enkel inloggning (SSO) för ett program i din Azure Active Directory (Azure AD)-klient.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 0f3073214fb47de006c6d9ebb07f443f3e63a4f3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91349101"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Snabb start: Konfigurera OIDC-baserad enkel inloggning (SSO) för ett program i din Azure Active Directory (Azure AD)-klient

Kom igång med förenklade användar inloggningar genom att konfigurera enkel inloggning (SSO) för ett program som du har lagt till i din Azure Active Directory (Azure AD)-klient. När du har konfigurerat SSO kan användarna logga in i ett program genom att använda sina autentiseringsuppgifter för Azure AD. SSO ingår i den kostnads fria versionen av Azure AD.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera SSO för ett program som du har lagt till i din Azure AD-klient behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En av följande roller: global administratör, moln program administratör, program administratör eller ägare av tjänstens huvud namn.
- Ett program som stöder SSO och som redan har förkonfigurerats och lagts till i Azure AD-galleriet. De flesta appar kan använda Azure AD för SSO. Apparna i Azure AD-galleriet är förkonfigurerade. Om din app inte finns med i listan eller är en anpassad-utvecklad app kan du fortfarande använda den med Azure AD. Titta närmare på självstudierna och annan dokumentation i innehålls förteckningen. Den här snabb starten fokuserar på appar som har förkonfigurerats för enkel inloggning och lagts till i Azure AD-galleriet av apps-utvecklare.
- Valfritt: Slutför [visningen av Visa dina appar](view-applications-portal.md).
- Valfritt: slut för ande av [Lägg till en app](add-application-portal.md).
- Valfritt: Slutför [konfiguration av en app](add-application-portal-configure.md).
- Valfritt: slut för ande av [tilldela användare till en app](add-application-portal-assign-users.md).

>[!IMPORTANT]
>Använd en icke-produktions miljö för att testa stegen i den här snabb starten.

## <a name="enable-single-sign-on-for-an-app"></a>Aktivera enkel inloggning för en app

När du lägger till en app som använder OIDC-standarden för SSO har du en installations knapp. När du väljer knappen går du till program platsen och slutför registrerings processen för appen. Processen för att lägga till en app beskrivs i Lägg till en app-snabb start tidigare i den här serien. Om du konfigurerar ett program som redan har lagts till tittar du på den första snabb starten. Den vägleder dig genom att visa de program som redan finns i din klient organisation. 

Så här konfigurerar du enkel inloggning för ett program:

1. I snabb starten tidigare i den här serien har du lärt dig hur du lägger till en app som använder din Azure AD-klient för identitets hantering. Om appens utvecklare använde OIDC-standarden för att implementera SSO visas en registrerings knapp när du lägger till appen. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="Skärm bild som visar alternativet enkel inloggning och registrerings knappen." lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::


2. Välj **registrering** så kommer du att få inloggnings sidan för appens utvecklare. Logga in med Azure Active Directory inloggnings uppgifter. 

   > [!IMPORTANT]
    > Om du redan har en prenumeration på programmet kommer verifieringen av användar information och klient-och katalog information att ske. Om programmet inte kan verifiera användaren kommer du att omdirigera dig för att registrera dig för program tjänsten eller på fel sidan.

3. Efter en lyckad autentisering visas en dialog ruta där du tillfrågas om administrativt medgivande. Välj **medgivande för din organisations räkning** och välj sedan **Godkänn**. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="Skärm bild som visar medgivande skärmen för en app." lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. Programmet läggs till i din klient organisation och start sidan för programmet visas.


> [!TIP]
> Du kan automatisera hantering av appar med hjälp av Graph API, se [Automatisera program hantering med Microsoft Graph API](https://docs.microsoft.com/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabb starts serien bör du ta bort appen för att rensa test klienten. Borttagning av appen beskrivs i den senaste snabb starten i den här serien. se [ta bort en app](delete-application-portal.md).

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du tar bort en app.
> [!div class="nextstepaction"]
> [Ta bort en app](delete-application-portal.md)
