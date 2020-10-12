---
title: Konfigurera direkt inloggning med Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du fyller i inloggnings namnet eller omdirigerar direkt till en social identitetsprovider.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/18/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9e7c537e85039675f27fa3e276b6b964ce1679b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388603"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Konfigurera direkt inloggning med hjälp av Azure Active Directory B2C

När du konfigurerar inloggning för ditt program med hjälp av Azure Active Directory (AD) B2C, kan du fylla i inloggnings namnet eller dirigera inloggningen till en specifik social identitetsprovider, till exempel Facebook, LinkedIn eller en Microsoft-konto.

## <a name="prepopulate-the-sign-in-name"></a>Förkonfigurera inloggnings namnet

Under en inloggnings användare kan ett förlitande parts program riktas mot ett särskilt användar-eller domän namn. När du riktar en användare till en användare, kan ett program ange `login_hint` Frågeparametern med användarens inloggnings namn i auktoriseringsbegäran. Azure AD B2C fyller automatiskt i inloggnings namnet, medan användaren bara behöver ange lösen ordet.

![Registrera inloggnings sidan med login_hint frågeparametrar markerad i URL](./media/direct-signin/login-hint.png)

Användaren kan ändra värdet i text rutan för inloggning.

Om du använder en anpassad princip åsidosätter du den `SelfAsserted-LocalAccountSignin-Email` tekniska profilen. I `<InputClaims>` avsnittet anger du DefaultValue för signInName-anspråket till `{OIDC:LoginHint}` . `{OIDC:LoginHint}`Variabeln innehåller värdet för `login_hint` parametern. Azure AD B2C läser värdet för signInName-anspråket och fyller i text rutan signInName.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="redirect-sign-in-to-a-social-provider"></a>Omdirigera inloggning till en social leverantör

Om du konfigurerade inloggnings resan för ditt program att inkludera sociala konton, till exempel Facebook, LinkedIn eller Google, kan du ange `domain_hint` parametern. Den här Frågeparametern ger en ledtråd för Azure AD B2C om den sociala identitets leverantör som ska användas för inloggning. Om programmet till exempel anger `domain_hint=facebook.com` skickas inloggningen direkt till Facebook-inloggnings sidan.

![Registrera inloggnings sidan med domain_hint frågeparametrar markerad i URL](./media/direct-signin/domain-hint.png)

Om du använder en anpassad princip kan du konfigurera domän namnet med hjälp av `<Domain>domain name</Domain>` XML-elementet i any `<ClaimsProvider>` .

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


