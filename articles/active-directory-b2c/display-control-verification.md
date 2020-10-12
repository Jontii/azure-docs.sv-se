---
title: Verifiera anspråk med visnings kontroller
titleSuffix: Azure AD B2C
description: Lär dig hur du använder Azure AD B2C Visa kontroller för att verifiera anspråk i användar resan som tillhandahålls av dina anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e42c889277f937a33e72eaf57819385166d6a409
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85202322"
---
# <a name="verification-display-control"></a>Verifierings kontroll

Använd en verifierings [visnings kontroll](display-controls.md) för att verifiera ett anspråk, till exempel en e-postadress eller ett telefonnummer, med en verifierings kod som skickas till användaren.

## <a name="verificationcontrol-actions"></a>VerificationControl-åtgärder

Verifierings visnings kontrollen består av två steg (åtgärder):

1. Begär ett mål från användaren, till exempel en e-postadress eller ett telefonnummer som verifierings koden ska skickas till. När användaren väljer knappen **Skicka kod** körs **SendCode-åtgärden** för verifierings kontrollens visnings kontroll. **SendCode-åtgärden** genererar en kod, konstruerar det innehåll som ska skickas och skickar det till användaren. Värdet för adressen kan vara ifyllt och fungera som en andra faktor.

    ![Exempel sida för åtgärden skicka kod](media/display-control-verification/display-control-verification-email-action-01.png)

1. När koden har skickats läser användaren meddelandet, anger verifierings koden i den kontroll som anges av visnings kontrollen och väljer **verifiera kod**. Genom att välja **verifiera kod**körs **VerifyCode-åtgärden** för att verifiera koden som är kopplad till adressen. Om användaren väljer **Skicka ny kod**utförs den första åtgärden igen.

    ![Exempel sida för att verifiera kod åtgärd](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>VerificationControl nödvändiga element

**VerificationControl** måste innehålla följande element:

- Typen för `DisplayControl` är `VerificationControl` .
- `DisplayClaims`
  - **Skicka till** en eller flera anspråk som anger var verifierings koden ska skickas till. Till exempel *e-post* , *landskod* och *telefonnummer*.
  - **Verifierings kod** – den verifierings kod som användaren anger efter att koden har skickats. Detta anspråk måste anges som obligatoriskt och `ControlClaimType` måste vara inställt på `VerificationCode` .
- Utgående anspråk (valfritt) som ska returneras till den självkontrollerade sidan när användaren har slutfört verifierings processen. Till exempel *e-post* , *landskod* och *telefonnummer*. Den självkontrollerade tekniska profilen använder anspråk för att bevara data eller bubblar över utgående anspråk till nästa Orchestration-steg.
- Två `Action` s med följande namn:
  - **SendCode** – skickar en kod till användaren. Den här åtgärden innehåller vanligt vis två validerings tekniska profiler, för att generera en kod och skicka den.
  - **VerifyCode** – verifierar koden. Den här åtgärden innehåller vanligt vis en teknisk profil med en validering.

I exemplet nedan visas en **e-** posttext ruta på sidan. När användaren anger sin e-postadress och väljer **SendCode**utlöses **SendCode** -åtgärden i Azure AD B2C Server del.

Sedan anger användaren **verificationCode** och väljer **VerifyCode** för att utlösa **VerifyCode** -åtgärden i Server delen. Om alla verifierings pass godkänns anses **VerificationControl** vara slutförd och användaren kan fortsätta till nästa steg.

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
