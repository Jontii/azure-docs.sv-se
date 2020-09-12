---
title: Tillhandahålla valfria anspråk till Azure AD-appar
titleSuffix: Microsoft identity platform
description: Hur du lägger till anpassade eller ytterligare anspråk till SAML 2,0-och JSON Web token (JWT)-token som utfärdats av Microsoft Identity Platform.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 09/03/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 2d895a6703123d8725a375e29e2e26b64b621f23
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2020
ms.locfileid: "89436858"
---
# <a name="how-to-provide-optional-claims-to-your-app"></a>Gör så här: Ange valfria anspråk för din app

Programutvecklare kan använda valfria anspråk i sina Azure AD-program för att ange vilka anspråk de vill ha i token som skickas till deras program.

Du kan använda valfria anspråk för att:

- Välj ytterligare anspråk som ska inkluderas i token för ditt program.
- Ändra beteendet för vissa anspråk som Microsoft Identity Platform returnerar i tokens.
- Lägg till och få till gång till anpassade anspråk för ditt program.

Listor över standard anspråk finns i [åtkomsttoken](access-tokens.md) och [id_token](id-tokens.md) dokumentation om anspråk.

Även om det finns stöd för valfria anspråk i både v 1.0-och v 2.0-format-token, och även SAML-token, ger de mest av sitt värde när de flyttas från v 1.0 till v 2.0. Ett av målen för [v 2.0 Microsoft Identity Platform-slutpunkten](./v2-overview.md) är mindre token-storlekar för att säkerställa optimala prestanda av klienter. Det innebär att flera anspråk som tidigare inkluderats i åtkomst-och ID-token inte längre finns i v 2.0-token och måste tillfrågas specifikt för varje program.

**Tabell 1: tillämplighet**

| Kontotyp               | v 1.0-token | v 2.0-token |
|----------------------------|-------------|-------------|
| Personlig Microsoft-konto | E.t.         | Stöds   |
| Azure AD-konto           | Stöds   | Stöds   |

## <a name="v10-and-v20-optional-claims-set"></a>v 1.0 och v 2.0 valfria anspråks uppsättningar

Den uppsättning valfria anspråk som är tillgängliga som standard för program som ska användas visas nedan. Om du vill lägga till anpassade valfria anspråk för programmet, se [katalog tillägg](#configuring-directory-extension-optional-claims)nedan. När anspråk läggs till i **åtkomsttoken gäller**anspråken för de åtkomsttoken som begärs *för* programmet (ett webb-API), inte anspråk som begärts *av* programmet. Oavsett hur klienten har åtkomst till ditt API, finns rätt data i den åtkomsttoken som används för att autentisera mot ditt API.

> [!NOTE]
> Majoriteten av dessa anspråk kan inkluderas i JWTs för v 1.0-och v 2.0-token, men inte SAML-token, förutom vad som anges i kolumnen tokentyp. Konsument konton har stöd för en delmängd av dessa anspråk som marker ATS i kolumnen "användar typ".  Många av anspråken i listan gäller inte konsument användare (de har ingen innehavare, så `tenant_ctry` har inget värde).

**Tabell 2: v 1.0 och v 2.0 valfri anspråks uppsättning**

| Name                       |  Beskrivning   | Tokentyp | Användar typ | Kommentarer  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Tid när användaren senast autentiserades. Se OpenID Connect spec.| JWT        |           |  |
| `tenant_region_scope`      | Resurs innehavarens region | JWT        |           | |
| `sid`                      | Sessions-ID som används för användar utloggning per session. | JWT        |  Personliga och Azure AD-konton.   |         |
| `verified_primary_email`   | Källan från användarens PrimaryAuthoritativeEmail      | JWT        |           |         |
| `verified_secondary_email` | Källan från användarens SecondaryAuthoritativeEmail   | JWT        |           |        |
| `vnet`                     | Information om VNET-specifikation. | JWT        |           |      |
| `fwd`                      | IP-adress.| JWT    |   | Lägger till den ursprungliga IPv4-adressen för den begär ande klienten (i ett VNET) |
| `ctry`                     | Användarens land/region | JWT, SAML |  | Azure AD returnerar det `ctry` valfria anspråket om det finns och värdet för fältet är en vanlig lands-/regionkod i två bokstäver, till exempel fr, JP, sz och så vidare. |
| `tenant_ctry`              | Resurs innehavarens land | JWT | | Samma som `ctry` förutom set på klient nivå av en administratör.  Måste också vara ett standard värde på två bokstäver. |
| `xms_pdl`             | Önskad data plats   | JWT | | För flera geo-klienter är den önskade data platsen den tre bokstavs koden som visar den geografiska region som användaren är i. Mer information finns i Azure AD Connect- [dokumentationen om önskad data plats](../hybrid/how-to-connect-sync-feature-preferreddatalocation.md).<br/>Exempel: `APC` för Asien och Stillahavsområdet. |
| `xms_pl`                   | Användarens prioriterade språk  | JWT ||Användarens önskade språk, om det är inställt. Från hem klienten, i scenarier med gäst åtkomst. Formaterat lla-CC ("en-US"). |
| `xms_tpl`                  | Föredraget klient språk| JWT | | Resurs innehavarens föredragna språk, om det är inställt. Formaterat lla ("en"). |
| `ztdid`                    | ID för noll-Touch-distribution | JWT | | Enhets identiteten som används för [Windows autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Det adresser bara e-postmeddelandet för den här användaren, om användaren har ett.  | JWT, SAML | MSA, Azure AD | Det här värdet ingår som standard om användaren är en gäst i klienten.  För hanterade användare (användare i klienten) måste det begäras via detta valfria anspråk eller, endast v 2.0, med OpenID-omfånget.  För hanterade användare måste e-postadressen anges i [Office Admin-portalen](https://portal.office.com/adminportal/home#/users).|
| `acct`                | Användarens konto status i klient organisationen | JWT, SAML | | Om användaren är medlem i klienten är värdet `0` . Om de är en gäst är värdet `1` . |
| `groups`| Valfri formatering för grupp anspråk |JWT, SAML| |Används tillsammans med GroupMembershipClaims-inställningen i [applikations manifestet](reference-app-manifest.md), som även måste anges. Mer information finns i [grupp anspråk](#configuring-groups-optional-claims) nedan. Mer information om grupp anspråk finns i [så här konfigurerar du grupp anspråk](../hybrid/how-to-connect-fed-group-claims.md)
| `upn`                      | UserPrincipalName | JWT, SAML  |           | Även om det här anspråket ingår automatiskt kan du ange det som ett valfritt anspråk för att bifoga ytterligare egenskaper för att ändra dess beteende i gäst användarens ärende.  |
| `idtyp`                    | Tokentyp   | JWT-åtkomsttoken | Special: endast i endast app-åtkomsttoken |  Värdet är `app` när token är en app-only-token. Detta är det mest exakta sättet för ett API för att avgöra om en token är en app-token eller en app + User-token.|

## <a name="v20-specific-optional-claims-set"></a>v 2.0 – angivna valfria anspråks uppsättningar

De här anspråken ingår alltid i v 1.0 Azure AD-tokens, men ingår inte i v 2.0-token om det inte begärs. Dessa anspråk gäller endast för JWTs (ID-token och åtkomsttoken).

**Tabell 3: v 2.0 – endast valfria anspråk**

| JWT-anspråk     | Name                            | Beskrivning                                | Kommentarer |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP-adress                      | IP-adressen som klienten loggade in från.   |       |
| `onprem_sid`  | Lokal säkerhets identifierare |                                             |       |
| `pwd_exp`     | Förfallotid för lösenord        | Det datum/tid-värde som lösen ordet upphör att gälla. |       |
| `pwd_url`     | Ändra lösen ordets URL             | En URL som användaren kan besöka för att ändra sina lösen ord.   |   |
| `in_corp`     | Inifrån företagsnätverket        | Signalerar om klienten loggar in från företags nätverket. Om de inte är det inkluderas inte anspråket.   |  Baserat på de [betrodda IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) -inställningarna i MFA.    |
| `family_name` | Efternamn                       | Innehåller användarens efter namn, efter namn eller familj som definierats i användarobjektet. <br>"family_name": "Miller" | Stöds i MSA och Azure AD. Kräver `profile` omfånget.   |
| `given_name`  | Förnamn                      | Anger det första eller "tilldelade" namnet på användaren, enligt vad som anges på användarobjektet.<br>"given_name": "Frank"                   | Stöds i MSA och Azure AD.  Kräver `profile` omfånget. |
| `upn`         | UPN (User Principal Name) | En identifierare för den användare som kan användas med parametern username_hint.  Inte en varaktig identifierare för användaren och ska inte användas för nyckel data. | Se [Ytterligare egenskaper](#additional-properties-of-optional-claims) nedan för konfiguration av anspråket. Kräver `profile` omfånget.|

### <a name="additional-properties-of-optional-claims"></a>Ytterligare egenskaper för valfria anspråk

Vissa valfria anspråk kan konfigureras för att ändra hur anspråket returneras. Dessa ytterligare egenskaper används främst för att hjälpa migrering av lokala program med olika data förväntningar (till exempel för att hjälpa `include_externally_authenticated_upn_without_hash` klienter som inte kan hantera hash-tecken ( `#` ) i UPN)

**Tabell 4: värden för konfiguration av valfria anspråk**

| Egenskapsnamn  | Ytterligare egenskaps namn | Beskrivning |
|----------------|--------------------------|-------------|
| `upn`          |                          | Kan användas för både SAML-och JWT-svar och för v 1.0-och v 2.0-token. |
|                | `include_externally_authenticated_upn`  | Inkluderar gäst-UPN som lagrats i resurs klienten. Till exempel `foo_hometenant.com#EXT#@resourcetenant.com` |
|                | `include_externally_authenticated_upn_without_hash` | Samma som ovan, förutom att hash-tecknen ( `#` ) ersätts med under streck ( `_` ), till exempel `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Exempel på ytterligare egenskaper

```json
"optionalClaims": {
    "idToken": [
        {
            "name": "upn",
            "essential": false,
            "additionalProperties": [
                "include_externally_authenticated_upn"
            ]
        }
    ]
}
```

Detta OptionalClaims-objekt gör att ID-token som returnerades till klienten inkluderar ett UPN-anspråk med ytterligare information om hem klient och resurs klient. `upn`Anspråket ändras bara i token om användaren är en gäst i klienten (som använder en annan IDP för autentisering).

## <a name="configuring-optional-claims"></a>Konfigurera valfria anspråk

> [!IMPORTANT]
> Åtkomsttoken skapas **alltid** med hjälp av resurs manifestet, inte klienten.  Så i begäran `...scope=https://graph.microsoft.com/user.read...` är resursen Microsoft Graph-API.  Därför skapas åtkomsttoken med hjälp av Microsoft Graph API-manifestet, inte klientens manifest.  Att ändra manifestet för programmet kommer aldrig att orsaka att token för Microsoft Graph-API: et ser annorlunda ut.  För att verifiera att ändringarna gäller `accessToken` , begär du en token för ditt program, inte en annan app.

Du kan konfigurera valfria anspråk för ditt program via användar gränssnittet eller applikations manifestet.

1. Gå till [Azure-portalen](https://portal.azure.com). Sök efter och välj **Azure Active Directory**.
1. I avsnittet **Hantera** väljer du **Appregistreringar**.
1. Välj det program som du vill konfigurera valfria anspråk för i listan.

**Konfigurera valfria anspråk genom användar gränssnittet:**

[![Konfigurera valfria anspråk i användar gränssnittet](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. I avsnittet **Hantera** väljer du **token-konfiguration**.
1. Välj **Lägg till valfritt anspråk**.
1. Välj den tokentyp som du vill konfigurera.
1. Välj de valfria anspråk som ska läggas till.
1. Välj **Lägg till**.

**Konfigurera valfria anspråk via applikations manifestet:**

[![Visar hur du konfigurerar valfria anspråk med hjälp av app-manifestet](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. I avsnittet **Hantera** väljer du **manifest**. En webbaserad manifest redigerare öppnas, så att du kan redigera manifestet. Du kan också välja **Ladda ned** och redigera manifestet lokalt, och sedan använda **Ladda upp** för att tillämpa det på appen igen. Mer information om applikations manifestet finns i [artikeln förstå program manifestet för Azure AD](reference-app-manifest.md).

    Följande program manifest post lägger till de auth_time, ipaddr och UPN valfria anspråk till ID, Access och SAML-token.

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "auth_time",
                "essential": false
            }
        ],
        "accessToken": [
            {
                "name": "ipaddr",
                "essential": false
            }
        ],
        "saml2Token": [
            {
                "name": "upn",
                "essential": false
            },
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": false
            }
        ]
    }
    ```

2. När du är färdig väljer du **Spara**. Nu kommer de angivna valfria anspråken att inkluderas i token för ditt program.

### <a name="optionalclaims-type"></a>Typ av OptionalClaims

Deklarerar de valfria anspråk som begärs av ett program. Ett program kan konfigurera valfria anspråk som ska returneras i var och en av tre typer av tokens (ID-token, åtkomsttoken, SAML 2-token) som den kan ta emot från säkerhetstokentjänsten. Programmet kan konfigurera en annan uppsättning valfria anspråk som ska returneras i varje tokentyp. Egenskapen OptionalClaims för programentiteten är ett OptionalClaims-objekt.

**Tabell 5: egenskaper för OptionalClaims-typ**

| Namn          | Typ                       | Beskrivning                                           |
|---------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Samling (OptionalClaim) | De valfria anspråk som returneras i JWT ID-token.     |
| `accessToken` | Samling (OptionalClaim) | De valfria anspråk som returneras i JWT-åtkomsttoken. |
| `saml2Token`  | Samling (OptionalClaim) | De valfria anspråk som returneras i SAML-token.       |

### <a name="optionalclaim-type"></a>Typ av OptionalClaim

Innehåller ett valfritt anspråk som associeras med ett program eller ett huvud namn för tjänsten. Egenskaperna idToken, accessToken och saml2Token för [OptionalClaims](/graph/api/resources/optionalclaims?view=graph-rest-1.0) -typen är en samling OptionalClaim.
Om det stöds av ett angivet anspråk kan du också ändra beteendet för OptionalClaim med hjälp av fältet AdditionalProperties.

**Tabell 6: egenskaper för OptionalClaim-typ**

| Namn                   | Typ                    | Beskrivning                                                                                                                                                                                                                                                                                                   |
|------------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Namnet på det valfria anspråket.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | Källa (katalog objekt) för anspråket. Det finns fördefinierade anspråk och användardefinierade anspråk från tilläggs egenskaper. Om source-värdet är null är anspråket ett fördefinierat valfritt anspråk. Om source-värdet är User är värdet i egenskapen name egenskapen Extension från objektet User. |
| `essential`            | Edm.Boolean             | Om värdet är True är det anspråk som anges av klienten nödvändigt för att säkerställa en smidig auktorisering för den specifika uppgift som användaren begärt. Standardvärdet är false.                                                                                                                 |
| `additionalProperties` | Samling (EDM. String) | Ytterligare egenskaper för anspråket. Om det finns en egenskap i den här samlingen ändras beteendet för det valfria anspråket som anges i egenskapen Name.                                                                                                                                                   |

## <a name="configuring-directory-extension-optional-claims"></a>Konfigurerar valfria anspråk för katalog tillägg

Förutom de vanliga valfria anspråks uppsättningarna kan du också konfigurera tokens för att inkludera tillägg. Mer information finns i [dokumentationen för Microsoft Graph extensionProperty](/graph/api/resources/extensionproperty?view=graph-rest-1.0).

Schema-och öppna tillägg stöds inte av valfria anspråk, bara katalog tilläggen AAD-Graph. Den här funktionen är användbar för att bifoga ytterligare användar information som din app kan använda – till exempel en ytterligare identifierare eller ett viktigt konfigurations alternativ som användaren har angett. Se slutet på den här sidan för ett exempel.

> [!NOTE]
> Katalog schema utökningar är en Azure AD-funktion. Om ditt program manifest begär ett anpassat tillägg och en MSA-användare loggar in i din app, returneras inte dessa tillägg.

### <a name="directory-extension-formatting"></a>Formatering av katalog tillägg

När du konfigurerar alternativ anspråk för katalog tillägg med hjälp av applikations manifestet använder du det fullständiga namnet på tillägget (i formatet: `extension_<appid>_<attributename>` ). `<appid>`Måste matcha ID för programmet som begär anspråket.

I JWT genereras dessa anspråk med följande namn format:  `extn.<attributename>` .

I SAML-token genereras dessa anspråk med följande URI-format: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Konfigurera grupper valfria anspråk

   > [!NOTE]
   > Möjligheten att generera grupp namn för användare och grupper som synkroniseras från lokal är offentlig för hands version.

Det här avsnittet beskriver konfigurations alternativen under valfria anspråk för att ändra de Gruppattribut som används i grupp anspråk från standard gruppen objectID till attribut som synkroniseras från lokala Windows-Active Directory. Du kan konfigurera grupper valfria anspråk för ditt program via användar gränssnittet eller applikations manifestet.

> [!IMPORTANT]
> Mer information, inklusive viktiga varningar om den offentliga för hands versionen av grupp anspråk från lokala attribut, finns i [Konfigurera grupp anspråk för program med Azure AD](../hybrid/how-to-connect-fed-group-claims.md).

**Konfigurera grupper valfria anspråk genom användar gränssnittet:**

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. När du har autentiserat väljer du din Azure AD-klient genom att välja den från det övre högra hörnet på sidan
1. Välj **Azure Active Directory** på menyn till vänster
1. Under avsnittet **Hantera** väljer du **Appregistreringar**
1. Välj det program som du vill konfigurera valfria anspråk för i listan
1. Under avsnittet **Hantera** väljer du **token-konfiguration**
1. Välj **Lägg till grupp anspråk**
1. Välj de grupp typer som ska returneras (**säkerhets grupper**eller **katalog roller**, **alla grupper**och/eller **grupper som har tilldelats till programmet**). De **grupper som har tilldelats program** alternativet inkluderar endast grupper som tilldelats programmet. Alternativet **alla grupper** innehåller **SecurityGroup**, **DirectoryRole**och **DistributionList**, men inte **grupper som är kopplade till programmet**. 
1. Valfritt: Välj egenskaperna för den speciella tokentypen för att ändra värdet för grupp anspråk till att innehålla lokala Gruppattribut eller ändra anspråks typen till en roll
1. Välj **Spara**

**Konfigurera grupper valfria anspråk via applikations manifestet:**

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. När du har autentiserat väljer du din Azure AD-klient genom att välja den från det övre högra hörnet på sidan
1. Välj **Azure Active Directory** på menyn till vänster
1. Välj det program som du vill konfigurera valfria anspråk för i listan
1. Under avsnittet **Hantera** väljer du **manifest**
1. Lägg till följande post med hjälp av manifest redigeraren:

   Giltiga värden är:

   - "Alla" (det här alternativet omfattar SecurityGroup, DirectoryRole och DistributionList)
   - "SecurityGroup"
   - "DirectoryRole"
   - "Variabeln applicationgroup" (det här alternativet inkluderar endast grupper som är kopplade till programmet)

   Exempel:

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

   Som standard genereras grupp ObjectIDs i grupp anspråks värdet.  Om du vill ändra anspråks värdet så att det innehåller lokala Gruppattribut, eller ändra anspråks typen till roll, använder du OptionalClaims-konfiguration enligt följande:

1. Ange alternativ för grupp namns konfiguration valfria anspråk.

   Om du vill att grupper i token ska innehålla attributen för lokal AD-grupp i det valfria anspråks avsnittet anger du vilken tokentyp som valfritt anspråk ska tillämpas på, namnet på valfritt begärt anspråk och eventuella ytterligare egenskaper som önskas.  Flera typer av token kan visas:

   - idToken för OIDC-ID-token
   - accessToken för OAuth-åtkomsttoken
   - Saml2Token för SAML-token.

   > [!NOTE]
   > Saml2Token-typen gäller för både SAML 1.1 och SAML 2.0-format-token

   För varje relevant tokentyp ändrar du grupp anspråk till att använda OptionalClaims-avsnittet i manifestet. OptionalClaims-schemat ser ut så här:

    ```json
    {
        "name": "groups",
        "source": null,
        "essential": false,
        "additionalProperties": []
    }
    ```

   | Valfritt anspråks schema | Värde |
   |----------|-------------|
   | **Namn:** | Måste vara "grupper" |
   | **källicensservern** | Används inte. Utelämna eller ange null |
   | **största** | Används inte. Utelämna eller ange falskt |
   | **additionalProperties:** | Lista över ytterligare egenskaper.  Giltiga alternativ är "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   I additionalProperties krävs bara en av "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Om det finns fler än en används den första och andra ignoreras.

   Vissa program kräver grupp information om användaren i roll anspråket.  Om du vill ändra anspråks typen till från ett grupp anspråk till ett roll anspråk lägger du till "emit_as_roles" i ytterligare egenskaper.  Grupp värden genereras i roll anspråket.

   > [!NOTE]
   > Om emit_as_roles används alla program roller som kon figurer ATS som användaren är tilldelad visas inte i roll anspråket

**Exempel:**

1) Generera grupper som grupp namn i OAuth-åtkomsttoken i dnsDomainName\sAMAccountName-format

    **GRÄNSSNITTs konfiguration:**

    [![Konfigurera valfria anspråk](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)

    **Post för program manifest:**

    ```json
    "optionalClaims": {
        "accessToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "dns_domain_and_sam_account_name"
                ]
            }
        ]
    }
    ```

2) Genererar grupp namn som ska returneras i netbiosDomain\sAMAccountName-format som roll anspråk i SAML-och OIDC-ID-token

    **GRÄNSSNITTs konfiguration:**

    [![Valfria anspråk i manifestet](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Post för program manifest:**

    ```json
    "optionalClaims": {
        "saml2Token": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ],
        "idToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ]
    }
    ```

## <a name="optional-claims-example"></a>Exempel på valfria anspråk

I det här avsnittet kan du gå igenom ett scenario för att se hur du kan använda funktionen valfria anspråk för ditt program.
Det finns flera tillgängliga alternativ för att uppdatera egenskaperna för ett programs identitets konfiguration för att aktivera och konfigurera valfria anspråk:

- Du kan använda användar gränssnittet för **token-konfiguration** (se exemplet nedan)
- Du kan använda **manifestet** (se exemplet nedan). Läs [förstå Azure AD Application manifest-dokumentet](./reference-app-manifest.md) först för en introduktion till manifestet.
- Det är också möjligt att skriva ett program som använder [Microsoft Graph API](/graph/use-the-api?context=graph%2fapi%2f1.0&view=graph-rest-1.0) för att uppdatera ditt program. [OptionalClaims](/graph/api/resources/optionalclaims?view=graph-rest-1.0) -typen i referens hand boken för Microsoft Graph API kan hjälpa dig att konfigurera de valfria anspråken.

**Exempel:**

I exemplet nedan använder du användar gränssnittet för **token-konfiguration** och **manifestet** för att lägga till valfria anspråk till åtkomst, ID och SAML-token som är avsedda för ditt program. Olika valfria anspråk kommer att läggas till i varje tokentyp som programmet kan ta emot:

- ID-token kommer nu att innehålla UPN för federerade användare i fullständig form ( `<upn>_<homedomain>#EXT#@<resourcedomain>` ).
- De åtkomsttoken som andra klienter begär för det här programmet kommer nu att innehålla auth_time-anspråk
- SAML-token kommer nu att innehålla skypeId Directory schema-tillägget (i det här exemplet är app-ID: t för den här appen ab603c56068041afb2f6832e2a17e237). SAML-token kommer att exponera Skype-ID: t som `extension_skypeId` .

**GRÄNSSNITTs konfiguration:**

1. Logga in på [Azure-portalen](https://portal.azure.com)

1. När du har autentiserat väljer du din Azure AD-klient genom att välja den från det övre högra hörnet på sidan.

1. Välj **Azure Active Directory** på menyn till vänster.

1. Under avsnittet **Hantera** väljer du **Appregistreringar**.

1. Hitta det program som du vill konfigurera valfria anspråk för i listan och markera det.

1. Under avsnittet **Hantera** väljer du **token-konfiguration**.

1. Välj **Lägg till valfritt anspråk**, Välj typ av **ID-** token, Välj **UPN** i listan över anspråk och välj sedan **Lägg till**.

1. Välj **Lägg till valfritt anspråk**, Välj **åtkomsttoken,** Välj **auth_time** i listan över anspråk och välj sedan **Lägg till**.

1. På sidan Översikt över token konfiguration väljer du Penn ikonen bredvid **UPN**, väljer den **externt autentiserade** växlingen och väljer sedan **Spara**.

1. Välj **Lägg till valfritt anspråk**, Välj **SAML** -tokentyp och välj **EXTn. skypeID** i listan över anspråk (gäller endast om du har skapat ett Azure AD-användarobjektet som heter SkypeID) och väljer sedan **Lägg till**.

    [![Valfria anspråk för SAML-token](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Manifest konfiguration:**

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. När du har autentiserat väljer du din Azure AD-klient genom att välja den från det övre högra hörnet på sidan.
1. Välj **Azure Active Directory** på menyn till vänster.
1. Hitta det program som du vill konfigurera valfria anspråk för i listan och markera det.
1. Under avsnittet **Hantera** väljer du **manifest** för att öppna den infogade manifest redigeraren.
1. Du kan redigera manifestet direkt med hjälp av den här redigeraren. Manifestet följer schemat för [programmets entitet](./reference-app-manifest.md)och formaterar automatiskt manifestet när det har sparats. Nya element kommer att läggas till i `OptionalClaims` egenskapen.

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "upn",
                "essential": false,
                "additionalProperties": [
                    "include_externally_authenticated_upn"
                ]
            }
        ],
        "accessToken": [
            {
                "name": "auth_time",
                "essential": false
            }
        ],
        "saml2Token": [
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": true
            }
        ]
    }
    ```

1. När du är klar med uppdateringen av manifestet väljer du **Spara** för att spara manifestet.

## <a name="next-steps"></a>Nästa steg

Läs mer om de standard anspråk som tillhandahålls av Azure AD.

- [ID-token](id-tokens.md)
- [Åtkomsttokens](access-tokens.md)
