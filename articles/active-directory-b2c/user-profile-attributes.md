---
title: Attribut för användar profil i Azure Active Directory B2C
description: Lär dig mer om de attribut för användar resurs typ som stöds av användar profilen för Azure AD B2C Directory. Lär dig mer om inbyggda attribut, tillägg och hur mappar mappas till Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1e6965e15b7482935148ae7fcd2edf0f3cc722b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "83738566"
---
# <a name="user-profile-attributes"></a>Attribut för användarprofil

Din Azure Active Directory (Azure AD) B2C-katalogens användar profil innehåller en inbyggd uppsättning attribut, till exempel namn, efter namn, stad, post nummer och telefonnummer. Du kan utöka användar profilen med dina egna program data utan att kräva ett externt data lager. De flesta av de attribut som kan användas med Azure AD B2C användar profiler stöds också av Microsoft Graph. I den här artikeln beskrivs stöd för Azure AD B2C användar profil. Den noterar också de attribut som inte stöds av Microsoft Graph, samt Microsoft Graph attribut som inte ska användas med Azure AD B2C.

> [!IMPORTANT]
> Du bör inte använda inbyggda eller tilläggsfiler för att lagra känsliga person uppgifter, t. ex. kontoautentiseringsuppgifter, myndighets identifierings nummer, kort data, ekonomiska konto data, hälso vård information eller känslig bakgrunds information.

Du kan också integrera med externa system. Du kan till exempel använda Azure AD B2C för autentisering, men delegera till en extern kund Relations hantering (CRM) eller kund förmåns databas som auktoritativ källa till kunddata. Mer information finns i [fjär profil](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) lösningen.

I tabellen nedan visas de egenskaper för [användar resurs typ](https://docs.microsoft.com/graph/api/resources/user) som stöds av användar profilen för Azure AD B2C Directory. Den innehåller följande information om varje attribut:

- Attributnamn som används av Azure AD B2C (följt av Microsoft Graph namn inom parentes, om annat)
- Attributets datatyp
- Beskrivning av attribut
- Om attributet är tillgängligt i Azure Portal
- Om attributet kan användas i ett användar flöde
- Om attributet kan användas i en anpassad princip för [Azure AD-teknisk profil](active-directory-technical-profile.md) och i vilket avsnitt ( &lt; InputClaims &gt; , &lt; OutputClaims &gt; eller &lt; PersistedClaims &gt; )

|Namn     |Typ     |Beskrivning|Azure Portal|Användarflöden|Anpassad princip|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolesk|Om användar kontot är aktiverat eller inaktiverat: **Sant** om kontot är aktiverat, annars **falskt**.|Ja|Inga|Bestående, utdata|
|ageGroup        |Sträng|Användarens ålders grupp. Möjliga värden: null, odefinierad, minor, vuxen, NotAdult.|Ja|Inga|Bestående, utdata|
|alternativeSecurityId ([identiteter](manage-user-accounts-graph-api.md#identities-property))|Sträng|En enskild användar identitet från den externa identitets leverantören.|Inga|Inga|Indata, bestående, utdata|
|alternativeSecurityIds ([identiteter](manage-user-accounts-graph-api.md#identities-property))|alternativ securityId-samling|En samling användar identiteter från externa identitets leverantörer.|Inga|Inga|Bestående, utdata|
|city            |Sträng|Den ort där användaren befinner sig. Maxlängd 128.|Ja|Ja|Bestående, utdata|
|consentProvidedForMinor|Sträng|Om medgivande har angetts för en mindre. Tillåtna värden: null, beviljat, nekat eller notRequired.|Ja|Inga|Bestående, utdata|
|land         |Sträng|Landet/regionen där användaren finns. Exempel: "oss" eller "Storbritannien". Maxlängd 128.|Ja|Ja|Bestående, utdata|
|createdDateTime|DateTime|Det datum då användarobjektet skapades. Skrivskyddad.|Inga|Inga|Bestående, utdata|
|creationType    |Sträng|Om användar kontot har skapats som ett lokalt konto för en Azure Active Directory B2C klient, är värdet LocalAccount eller nameCoexistence. Skrivskyddad.|Inga|Inga|Bestående, utdata|
|dateOfBirth     |Datum|Födelsedatum.|Inga|Inga|Bestående, utdata|
|avdelning      |Sträng|Namnet på den avdelning där användaren arbetar. Maxlängd 64.|Ja|Inga|Bestående, utdata|
|displayName     |Sträng|Användarens visnings namn. Maxlängd 256.|Ja|Ja|Bestående, utdata|
|facsimileTelephoneNumber<sup>1</sup>|Sträng|Telefonnumret till användarens företags Fax maskin.|Ja|Inga|Bestående, utdata|
|förnamn       |Sträng|Användarens förnamn (förnamn). Maxlängd 64.|Ja|Ja|Bestående, utdata|
|Befattning        |Sträng|Användarens jobb titel. Maxlängd 128.|Ja|Ja|Bestående, utdata|
|immutableId     |Sträng|En identifierare som vanligt vis används för användare som migrerats från lokala Active Directory.|Inga|Inga|Bestående, utdata|
|legalAgeGroupClassification|Sträng|Juridisk ålders grupps klassificering. Skrivskyddad och beräknas utifrån egenskaperna ageGroup och consentProvidedForMinor. Tillåtna värden: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult och vuxen.|Ja|Inga|Bestående, utdata|
|legalCountry<sup>1</sup>  |Sträng|Land/region för juridiskt syfte.|Inga|Inga|Bestående, utdata|
|e-post            |Sträng|SMTP-adressen till användaren, till exempel " bob@contoso.com ". Skrivskyddad.|Inga|Inga|Bestående, utdata|
|smeknamn för e-post    |Sträng|Användarens e-postalias. Maxlängd 64.|Inga|Inga|Bestående, utdata|
|mobil (mobilePhone) |Sträng|Det primära mobiltelefon telefonnumret för användaren. Maxlängd 64.|Ja|Inga|Bestående, utdata|
|netId           |Sträng|NET ID.|Inga|Inga|Bestående, utdata|
|objectId        |Sträng|En globalt unik identifierare (GUID) som är den unika identifieraren för användaren. Exempel: 12345678-9ABC-def0-1234-56789abcde. Skrivskyddad, oföränderlig.|Skrivskyddad|Ja|Indata, bestående, utdata|
|otherMails      |Sträng samling|En lista med ytterligare e-postadresser för användaren. Exempel: [" bob@contoso.com ", " Robert@fabrikam.com "].|Ja (alternativ e-post)|Inga|Bestående, utdata|
|password        |Sträng|Lösen ordet för det lokala kontot när användaren skapas.|Inga|Inga|Beständiga|
|passwordPolicies     |Sträng|Princip för lösen ordet. Det är en sträng som består av ett annat princip namn, avgränsat med kommatecken. t. ex. "DisablePasswordExpiration, DisableStrongPassword".|Inga|Inga|Bestående, utdata|
|physicalDeliveryOfficeName (officeLocation)|Sträng|Arbets platsen på användarens arbets plats. Maxlängd 128.|Ja|Inga|Bestående, utdata|
|Post nummer      |Sträng|Post numret för användarens post adress. Post numret är speciellt för användarens land/region. I USA i Amerika innehåller det här attributet post numret. Maxlängd 40.|Ja|Inga|Bestående, utdata|
|preferredLanguage    |Sträng|Det föredragna språket för användaren. Bör följa ISO 639-1-koden. Exempel: "en-US".|Inga|Inga|Bestående, utdata|
|refreshTokensValidFromDateTime|DateTime|Alla uppdateringstoken som utfärdats före den här tiden är ogiltiga och program får ett fel meddelande när en ogiltig uppdateringstoken används för att hämta en ny åtkomsttoken. Om detta händer måste programmet skaffa en ny uppdateringstoken genom att göra en begäran till behörighets slut punkten. Skrivskyddad.|Inga|Inga|Utdata|
|signInNames ([identiteter](manage-user-accounts-graph-api.md#identities-property)) |Sträng|Det unika inloggnings namnet för den lokala konto användaren av valfri typ i katalogen. Använd detta för att få en användare med inloggnings värde utan att ange den lokala konto typen.|Inga|Inga|Indata|
|signInNames. userName ([identiteter](manage-user-accounts-graph-api.md#identities-property)) |Sträng|Unikt användar namn för den lokala konto användaren i katalogen. Använd detta för att skapa eller hämta en användare med ett särskilt inloggnings användar namn. Om du anger detta i PersistedClaims under korrigerings åtgärden tas andra typer av signInNames bort. Om du vill lägga till en ny typ av signInNames måste du också behålla befintlig signInNames.|Inga|Inga|Indata, bestående, utdata|
|signInNames. telefonnummer ([identiteter](manage-user-accounts-graph-api.md#identities-property)) |Sträng|Det unika telefonnumret för den lokala konto användaren i katalogen. Använd detta för att skapa eller hämta en användare med ett särskilt inloggnings telefonnummer. Om du anger detta i PersistedClaims under korrigerings åtgärden tas andra typer av signInNames bort. Om du vill lägga till en ny typ av signInNames måste du också behålla befintlig signInNames.|Inga|Inga|Indata, bestående, utdata|
|signInNames. emailAddress ([identiteter](manage-user-accounts-graph-api.md#identities-property))|Sträng|Den unika e-postadressen för den lokala konto användaren i katalogen. Använd detta för att skapa eller hämta en användare med en speciell e-postadress för inloggning. Om du anger detta i PersistedClaims under korrigerings åtgärden tas andra typer av signInNames bort. Om du vill lägga till en ny typ av signInNames måste du också behålla befintlig signInNames.|Inga|Inga|Indata, bestående, utdata|
|state           |Sträng|Region i användarens adress. Maxlängd 128.|Ja|Ja|Bestående, utdata|
|streetAddress   |Sträng|Gatuadressen till användarens arbets plats. Maxlängd 1024.|Ja|Ja|Bestående, utdata|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|Sträng|Användarens sekundära telefonnummer, som används för Multi-Factor Authentication.|Ja|Inga|Bestående, utdata|
|strongAuthenticationEmailAddress<sup>1</sup>|Sträng|SMTP-adressen för användaren. Exempel: " bob@contoso.com " Detta attribut används för inloggning med användar namn princip för att lagra användarens e-postadress. E-postadressen används sedan i ett flöde för återställning av lösen ord.|Ja|Inga|Bestående, utdata|
|strongAuthenticationPhoneNumber<sup>1</sup>|Sträng|Användarens primära telefonnummer, som används för Multi-Factor Authentication.|Ja|Inga|Bestående, utdata|
|surname         |Sträng|Användarens efter namn (familje namn eller efter namn). Maxlängd 64.|Ja|Ja|Bestående, utdata|
|telephoneNumber (första posten i businessPhones)|Sträng|Det primära telefonnumret till användarens arbets plats.|Ja|Inga|Bestående, utdata|
|userPrincipalName    |Sträng|Användarens huvudnamn (UPN). UPN är ett inloggnings namn för användaren som baseras på Internet standard RFC 822. Domänen måste finnas i klientens samling med verifierade domäner. Den här egenskapen krävs när ett konto skapas. Inte kan ändras.|Inga|Inga|Indata, bestående, utdata|
|usageLocation   |Sträng|Krävs för användare som ska tilldelas licenser på grund av juridiskt krav för att kontrol lera tillgängligheten för tjänster i länder/regioner. Kan inte ha värdet null. En lands-/regionkod i två bokstäver (ISO standard 3166). Exempel: "US", "JP" och "GB".|Ja|Inga|Bestående, utdata|
|userType        |Sträng|Ett sträng värde som kan användas för att klassificera användar typer i din katalog. Värdet måste vara medlem. Skrivskyddad.|Skrivskyddad|Inga|Bestående, utdata|
|userState (externalUserState)<sup>2</sup>|Sträng|För Azure AD B2B-konto anger du om inbjudan är PendingAcceptance eller accepterad.|Inga|Inga|Bestående, utdata|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Visar tidsstämpeln för den senaste ändringen av egenskapen UserState.|Inga|Inga|Bestående, utdata|
|<sup>1 </sup> Stöds inte av Microsoft Graph<br><sup>2 </sup> Ska inte användas med Azure AD B2C||||||


## <a name="extension-attributes"></a>Attribut för tillägg

Du behöver ofta skapa egna attribut, som i följande fall:

- Ett kundriktat program måste vara beständigt för ett attribut som **LoyaltyNumber**.
- En identitetsprovider har ett unikt användar-ID, t. ex. **uniqueUserGUID** , som måste sparas.
- En anpassad användar resa måste vara bestående för ett tillstånd för en användare, t. ex. **migrationStatus**.

Azure AD B2C utökar uppsättningen attribut som lagras för varje användar konto. Utökade attribut [utökar schemat](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) för användar objekt i katalogen. Attributen för tillägg kan bara registreras på ett program objekt, även om de kan innehålla data för en användare. Attributet Extension är kopplat till programmet som kallas B2C-Extensions-app. Ändra inte det här programmet eftersom det används av Azure AD B2C för att lagra användar data. Du kan hitta det här programmet under Azure Active Directory Appregistreringar.

> [!NOTE]
> - Attributen upp till 100 kan skrivas till alla användar konton.
> - Om programmet B2C-Extensions-app tas bort, tas dessa tilläggsfiler bort från alla användare tillsammans med alla data som de innehåller.
> - Om ett attribut för tillägg tas bort av programmet tas det bort från alla användar konton och värdena tas bort.
> - Det underliggande namnet på attributet Extension genereras i formatet "Extension_" + program-ID + "_" + attributnamn. Om du till exempel skapar ett attribut för LoyaltyNumber och B2C-tillägg-app-ID: t är 831374b3-bd50-41bf-aa54-263ec9e050fc, kommer det underliggande tillägget att vara: extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber. Du använder det underliggande namnet när du kör Graph API frågor för att skapa eller uppdatera användar konton.

Följande data typer stöds när du definierar en egenskap i ett schema tillägg:

|Egenskaps typ |Kommentarer  |
|--------------|---------|
|Boolesk    | Möjliga värden: **True** eller **false**. |
|DateTime   | Måste anges i formatet ISO 8601. Kommer att lagras i UTC.   |
|Heltal    | 32-bitars värde.               |
|Sträng     | högst 256 tecken.     |

## <a name="next-steps"></a>Nästa steg
Läs mer om attribut för tillägg:
- [Schemautökningar](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [Definiera anpassade attribut med användar flöde](user-flow-custom-attributes.md)
- [Definiera anpassade attribut med anpassad princip](custom-policy-custom-attributes.md)
