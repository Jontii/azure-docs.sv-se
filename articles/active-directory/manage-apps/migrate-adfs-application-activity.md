---
title: Använd aktivitets rapporten för att flytta AD FS appar till Azure Active Directory | Microsoft Docs
description: I rapporten Active Directory Federation Services (AD FS) (AD FS) program aktivitet kan du snabbt migrera program från AD FS till Azure Active Directory (Azure AD). Det här migrations verktyget för AD FS identifierar kompatibilitet med Azure AD och ger vägledning om migrering.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59502e01a96b603067bd80b92bcf49136f8cef4e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85339171"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Använda rapporten AD FS program aktivitet (för hands version) för att migrera program till Azure AD

Många organisationer använder Active Directory Federation Services (AD FS) (AD FS) för att tillhandahålla enkel inloggning till moln program. Det finns stora fördelar med att flytta AD FS-program till Azure AD för autentisering, särskilt vad gäller kostnads hantering, riskhantering, produktivitet, efterlevnad och styrning. Men det kan ta lång tid att förstå vilka program som är kompatibla med Azure AD och identifiera särskilda migrerings steg.

I rapporten AD FS program aktivitet (för hands version) i Azure Portal kan du snabbt identifiera vilka av dina program som kan migreras till Azure AD. Den utvärderar alla AD FS program för kompatibilitet med Azure AD, söker efter eventuella problem och ger vägledning om att förbereda enskilda program för migrering. Med rapporten AD FS program aktivitet kan du:

* **Identifiera AD FS program och omfånget för migreringen.** Rapporten AD FS program aktivitet visar alla AD FS program i din organisation och visar att de är kompatibla med att migrera till Azure AD.
* **Prioritera program för migrering.** Hämta antalet unika användare som har loggat in på programmet under de senaste 1, 7 eller 30 dagarna för att avgöra den kritiska risken för migrering av programmet.
* **Kör migrerings test och åtgärda problem.** Rapporterings tjänsten kör automatiskt tester för att avgöra om ett program är redo att migrera. Resultaten visas i rapporten AD FS program aktivitet som en migrerings status. Om potentiella problem med migreringen identifieras får du specifika anvisningar om hur du löser problemen.

AD FS programmets aktivitets data är tillgängliga för användare som har tilldelats någon av dessa administratörs roller: global administratör, rapport läsare, säkerhets läsare, program administratör eller moln program administratör.

## <a name="prerequisites"></a>Krav

* Din organisation måste för närvarande använda AD FS för att få åtkomst till program.
* Azure AD Connect Health måste vara aktiverat i din Azure AD-klient.
* Azure AD Connect Health för AD FS agent måste vara installerad.
   * [Läs mer om Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Kom igång med att konfigurera Azure AD Connect Health och installera AD FS-agenten](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Identifiera AD FS program som kan migreras 

Rapporten AD FS program aktivitet är tillgänglig i Azure Portal under Azure AD- **användning & Insights** -rapportering. Rapporten AD FS program aktivitet analyserar varje AD FS program för att avgöra om den kan migreras i befintligt skick eller om ytterligare granskning krävs. 

1. Logga in på [Azure Portal](https://portal.azure.com) med en administratörs roll som har åtkomst till AD FS program aktivitets data (global administratör, rapport läsare, säkerhets läsare, program administratör eller moln program administratör).

2. Välj **Azure Active Directory**och välj sedan **företags program**.

3. Under **aktivitet**väljer du **användning & insikter (för hands version)** och väljer sedan **AD FS program aktivitet** för att öppna en lista över alla AD FS program i din organisation.

   ![AD FS program aktivitet](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Visa **status för migrering**för varje program i listan AD FS program aktivitet:

   * **Redo för migrering** innebär att AD FS program konfigurationen stöds fullt ut i Azure AD och kan migreras i befintligt skick.

   * **Kräver granskning** innebär att vissa av programmets inställningar kan migreras till Azure AD, men du måste granska de inställningar som inte kan migreras i befintligt skick.

   * **Ytterligare steg som krävs** innebär att Azure AD inte stöder vissa av programmets inställningar, så att programmet inte kan migreras i det aktuella läget.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Utvärdera beredskap för ett program för migrering 

1. I listan AD FS program aktivitet klickar du på status i kolumnen Status för **migrering** för att öppna migrerings information. Du ser en sammanfattning av de konfigurations test som godkänts, tillsammans med eventuella eventuella problem vid migreringen.

   ![Information om migrering](media/migrate-adfs-application-activity/migration-details.png)

2. Klicka på ett meddelande för att öppna ytterligare information om migrations regler. En fullständig lista över de egenskaper som har testats finns i tabellen [AD FS program konfigurations test](#ad-fs-application-configuration-tests) nedan.

   ![Information om migrations regel](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS program konfigurations test

I följande tabell visas alla konfigurations test som utförs på AD FS program.

|Resultat  |Pass/varning/fel  |Beskrivning  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> Minst en icke-går migrera regel identifierades för AdditionalAuthentication.       | Pass/varning          | Den förlitande parten har regler för att begära Multi-Factor Authentication (MFA). Om du vill flytta till Azure AD översätter du reglerna till principer för villkorlig åtkomst. Om du använder en lokal MFA-MFA rekommenderar vi att du flyttar till Azure MFA. [Läs mer om villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> Förlitande part har AdditionalWSFedEndpoint inställt på sant.       | Godkänn/Underkänn          | Den förlitande parten i AD FS tillåter flera WS-Fed försäkrade slut punkter.Azure AD har för närvarande endast stöd för en.Om du har ett scenario där det här resultatet blockerar migreringen kan du berätta för [oss](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints).     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> Förlitande part har angett AllowedAuthenticationClassReferences.       | Godkänn/Underkänn          | Med den här inställningen i AD FS kan du ange om programmet har kon figurer ATS för att endast tillåta vissa typer av autentisering. Vi rekommenderar att du använder villkorlig åtkomst för att uppnå den här funktionen. Om du har ett scenario där det här resultatet blockerar migreringen kan du berätta för [oss](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication).  [Läs mer om villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | Godkänn/Underkänn          | Med den här inställningen i AD FS kan du ange om programmet är konfigurerat att ignorera SSO-cookies och **alltid fråga efter autentisering**. I Azure AD kan du hantera Autentiseringstjänsten med hjälp av principer för villkorlig åtkomst för att uppnå liknande beteende. [Läs mer om hur du konfigurerar hantering av autentisering med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Test-ADFSRPAutoUpdateEnabled <br> Förlitande part har AutoUpdateEnabled inställt på Sant       | Pass/varning          | Med den här inställningen i AD FS kan du ange om AD FS har kon figurer ATS för automatisk uppdatering av programmet baserat på ändringar inom federationens metadata. Azure AD stöder inte detta idag men ska inte blockera migreringen av programmet till Azure AD.           |
|Test-ADFSRPClaimsProviderName <br> Flera ClaimsProviders har Aktiver ATS för förlitande part       | Godkänn/Underkänn          | Den här inställningen i AD FS anropar de identitets leverantörer från vilka den förlitande parten accepterar anspråk. I Azure AD kan du aktivera externt samarbete med Azure AD B2B. [Läs mer om Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b).          |
|Test-ADFSRPDelegationAuthorizationRules      | Godkänn/Underkänn          | Det finns definierade egna regler för delegering för programmet. Detta är ett WS-Trust koncept som Azure AD stöder med moderna autentiseringsprotokoll, till exempel OpenID Connect och OAuth 2,0. [Läs mer om Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Test-ADFSRPImpersonationAuthorizationRules       | Pass/varning          | Programmet har anpassade auktoriseringsregler definierade.Detta är ett WS-Trust koncept som Azure AD stöder med moderna autentiseringsprotokoll, till exempel OpenID Connect och OAuth 2,0. [Läs mer om Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Test-ADFSRPIssuanceAuthorizationRules <br> Minst en icke-går migrera regel identifierades för IssuanceAuthorization.       | Pass/varning          | Programmet har anpassade regler för utfärdande auktoriseringsregler definierade i AD FS.Azure AD har stöd för den här funktionen med villkorlig åtkomst för Azure AD. [Läs mer om villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). <br> Du kan också begränsa åtkomsten till ett program efter användare eller grupper som tilldelats programmet. [Lär dig mer om att tilldela användare och grupper åtkomst till program](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups).            |
|Test-ADFSRPIssuanceTransformRules <br> Minst en icke-går migrera regel identifierades för IssuanceTransform.       | Pass/varning          | Programmet har anpassade omvandlings regler för utfärdande som definierats i AD FS. Azure AD stöder anpassning av anspråk som utfärdats i token. Mer information finns i [Anpassa anspråk som utfärdats i SAML-token för företags program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).           |
|Test-ADFSRPMonitoringEnabled <br> Förlitande part har MonitoringEnabled inställt på sant.       | Pass/varning          | Med den här inställningen i AD FS kan du ange om AD FS har kon figurer ATS för automatisk uppdatering av programmet baserat på ändringar inom federationens metadata. Azure AD stöder inte detta idag men ska inte blockera migreringen av programmet till Azure AD.           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | Pass/varning          | AD FS tillåter en tids skevning baserat på NotBefore och NotOnOrAfter gånger i SAML-token. Azure AD hanterar detta automatiskt som standard.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> Förlitande part har RequestMFAFromClaimsProviders inställt på sant.       | Pass/varning          | Den här inställningen i AD FS bestämmer beteendet för MFA när användaren kommer från en annan anspråks leverantör. I Azure AD kan du aktivera externt samarbete med Azure AD B2B. Sedan kan du tillämpa principer för villkorlig åtkomst för att skydda gäst åtkomst. Läs mer om [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) och [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).          |
|Test-ADFSRPSignedSamlRequestsRequired <br> Förlitande part har SignedSamlRequestsRequired inställt på Sant       | Godkänn/Underkänn          | Programmet konfigureras i AD FS för att verifiera signaturen i SAML-begäran. Azure AD accepterar en signerad SAML-begäran; signaturen kommer dock inte att verifieras. Azure AD har olika metoder för att skydda mot skadliga samtal. Till exempel använder Azure AD svars-URL: er som kon figurer ATS i programmet för att validera SAML-begäran. Azure AD skickar bara en token till svars-URL: er som har kon figurer ATS för programmet. Om du har ett scenario där det här resultatet blockerar migreringen kan du berätta för [oss](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature).          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Pass/varning         | Programmet har kon figurer ATS för en anpassad token för token. Standardvärdet för AD FS är en timme.Azure AD stöder den här funktionen med villkorlig åtkomst. Mer information finns i [Konfigurera hantering av autentisering med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Förlitande part är inställd på att kryptera anspråk. Detta stöds av Azure AD       | Pass          | Med Azure AD kan du kryptera den token som skickas till programmet. Mer information finns i [Konfigurera Azure AD SAML token Encryption](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).          |
|EncryptedNameIdRequiredCheckResult      | Godkänn/Underkänn          | Programmet är konfigurerat för att kryptera nameID-anspråket i SAML-token.Med Azure AD kan du kryptera hela token som skickas till programmet.Kryptering av vissa anspråk stöds inte ännu. Mer information finns i [Konfigurera Azure AD SAML token Encryption](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).         |

## <a name="check-the-results-of-claim-rule-tests"></a>Kontrol lera resultaten av anspråks regel test

Om du har konfigurerat en anspråks regel för programmet i AD FS får du en detaljerad analys av alla anspråks regler. Du kommer att se vilka anspråks regler som kan flyttas till Azure AD och vilka som behövs för att granska dem ytterligare.

1. I listan AD FS program aktivitet klickar du på status i kolumnen Status för **migrering** för att öppna migrerings information. Du ser en sammanfattning av de konfigurations test som godkänts, tillsammans med eventuella eventuella problem vid migreringen.

2. På sidan **information om migrations regel** expanderar du resultaten för att visa information om potentiella problem med migreringen och få ytterligare vägledning. En detaljerad lista över alla anspråks regler som har testats finns i tabellen [kontrol lera resultaten av anspråks regel test](#check-the-results-of-claim-rule-tests) nedan.

   Exemplet nedan visar information om migrations regler för IssuanceTransform-regeln. Den listar de specifika delarna av anspråket som måste granskas och åtgärdas innan du kan migrera programmet till Azure AD.

   ![Migrations regel information ytterligare vägledning](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Test av anspråks regler

I följande tabell visas alla test av anspråks regler som utförs på AD FS program.

|Egenskap  |Beskrivning  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | Villkors satsen använder reguljära uttryck för att utvärdera om anspråket matchar ett visst mönster.För att uppnå en liknande funktion i Azure AD kan du använda fördefinierade omvandlingar som IfEmpty (), StartWith (), innehåller (), bland annat. Mer information finns i [Anpassa anspråk som utfärdats i SAML-token för företags program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_CONDITION_CLASS      | Villkors satsen har flera villkor som måste utvärderas innan utfärdande-instruktionen körs.Azure AD kan använda den här funktionen med anspråkets omvandlings funktioner där du kan utvärdera flera anspråks värden.Mer information finns i [Anpassa anspråk som utfärdats i SAML-token för företags program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_RULE_TYPE      | Det gick inte att identifiera anspråks regeln. Mer information om hur du konfigurerar anspråk i Azure AD finns i [Anpassa anspråk som utfärdats i SAML-token för företags program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | Villkors satsen använder en utfärdare som inte stöds i Azure AD.För närvarande är Azure AD inte käll anspråk från butiker som Active Directory eller Azure AD. Om det här hindrar dig från att migrera program till Azure AD kan du berätta för [oss](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | Villkors satsen använder en mängd funktion för att utfärda eller lägga till ett enda anspråk oavsett antalet matchningar.I Azure AD kan du utvärdera attributet för en användare för att avgöra vilket värde som ska användas för anspråket med funktioner som IfEmpty (), StartWith (), innehåller (), bland annat.Mer information finns i [Anpassa anspråk som utfärdats i SAML-token för företags program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|RESTRICTED_CLAIM_ISSUED      | Villkors satsen använder ett anspråk som är begränsat i Azure AD. Du kanske kan utfärda ett begränsat anspråk, men du kan inte ändra källan eller använda någon omvandling. Mer information finns i [Anpassa anspråk som släpps i token för en särskild app i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).          |
|EXTERNAL_ATTRIBUTE_STORE      | Utfärdande-instruktionen använder ett attributarkiv som är ett annat Active Directory. För närvarande är Azure AD inte käll anspråk från butiker som Active Directory eller Azure AD. Om det här resultatet hindrar dig från att migrera program till Azure AD kan du berätta för [oss](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | Utfärdande-instruktionen använder Lägg till för att lägga till anspråk i den inkommande anspråks uppsättningen. I Azure AD kan detta konfigureras som flera anspråks omvandlingar.Mer information finns i [Anpassa anspråk som utfärdats i SAML-token för företags program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | Utfärdande-instruktionen använder reguljära uttryck för att transformera värdet för det anspråk som ska genereras.För att uppnå liknande funktioner i Azure AD kan du använda fördefinierade omvandlingar som Extract (), trim (), ToLower, bland annat. Mer information finns i [Anpassa anspråk som utfärdats i SAML-token för företags program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |


## <a name="next-steps"></a>Nästa steg

- [Video: så här använder du rapporten AD FS aktivitet för att migrera ett program](https://www.youtube.com/watch?v=OThlTA239lU)
- [Hantera program med Azure Active Directory](what-is-application-management.md)
- [Hantera åtkomst till appar](what-is-access-management.md)
- [Azure AD Connect Federation](../hybrid/how-to-connect-fed-whatis.md)
