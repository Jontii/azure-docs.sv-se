---
title: Definiera en OAuth2 teknisk profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en OAuth2 teknisk profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 35b1f57a2361c5a4360e2ff1944b93e767168799
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259398"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en OAuth2 teknisk profil i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) har stöd för OAuth2-protokollets identitets leverantör. OAuth2 är det primära protokollet för auktorisering och delegerad autentisering. Mer information finns i [RFC 6749 Authorization Framework för OAuth 2,0](https://tools.ietf.org/html/rfc6749). Med en OAuth2 teknisk profil kan du federera med en OAuth2-baserad identitets leverantör, t. ex. Facebook. Genom att federera med en identitets leverantör kan användare logga in med sina befintliga sociala identiteter eller företags identiteter.

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `OAuth2` . Till exempel är protokollet för den tekniska profilen för **Facebook-OAuth** `OAuth2` :

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Inmatade anspråk

Elementen **InputClaims** och **InputClaimsTransformations** krävs inte. Men du kanske vill skicka ytterligare parametrar till din identitets leverantör. I följande exempel lägger du till parametern **domain_hint** frågesträngparametern med värdet för `contoso.com` auktorisering av begäran.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Utgående anspråk

**OutputClaims** -elementet innehåller en lista över anspråk som returneras av OAuth2 Identity Provider. Du kan behöva mappa namnet på det anspråk som definierats i principen till det namn som definierats i identitets leverantören. Du kan också inkludera anspråk som inte returneras av identitets leverantören så länge som du ställer in `DefaultValue` attributet.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

I följande exempel visas de anspråk som returneras av Facebook Identity Provider:

- **First_name** -anspråk är mappat till **givenName** -anspråket.
- **Last_Name** -anspråk är mappat till anspråket efter **namn** .
- **DisplayName** -anspråket utan namn mappning.
- **E-** postanspråk utan namn mappning.

Den tekniska profilen returnerar även anspråk som inte returneras av identitets leverantören:

- **IdentityProvider** -anspråket som innehåller namnet på identitets leverantören.
- **AuthenticationSource** -anspråket med standardvärdet **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_id | Yes | Program identifieraren för identitets leverantören. |
| IdTokenAudience | No | Id_tokenens mål grupp. Om det här alternativet anges kontrollerar Azure AD B2C om token finns i ett anspråk som returneras av identitets leverantören och är lika med det som anges. |
| authorization_endpoint | Yes | URL: en för Authorization-slutpunkten enligt RFC 6749. |
| AccessTokenEndpoint | Yes | URL: en för token-slutpunkten enligt RFC 6749. |
| ClaimsEndpoint | Yes | URL: en för slut punkten för användar information enligt RFC 6749. |
| AccessTokenResponseFormat | No | Formatet på slut punkts anropet för åtkomsttoken. Facebook kräver till exempel en HTTP GET-metod, men svaret om åtkomsttoken är i JSON-format. |
| AdditionalRequestQueryParameters | No | Parametrar för ytterligare begär ande fråga. Du kanske till exempel vill skicka ytterligare parametrar till din identitets leverantör. Du kan inkludera flera parametrar med hjälp av komma-avgränsare. |
| ClaimsEndpointAccessTokenName | No | Namnet på frågesträngparametern för åtkomsttoken. Vissa identitets-providers anspråks slut punkter stöder HTTP-begäran. I det här fallet skickas Bearer-token med hjälp av en frågesträngparametern i stället för Authorization-huvudet. |
| ClaimsEndpointFormatName | No | Namnet på parametern format Query String. Du kan till exempel ange namnet som `format` i den här LinkedIn-anspråks slut punkten `https://api.linkedin.com/v1/people/~?format=json` . |
| ClaimsEndpointFormat | No | Värdet för parametern format Query String. Du kan till exempel ange värdet som `json` i den här LinkedIn-anspråks slut punkten `https://api.linkedin.com/v1/people/~?format=json` . |
| ProviderName | No | Namnet på identitets leverantören. |
| response_mode | No | Metoden som identitets leverantören använder för att skicka tillbaka resultatet till Azure AD B2C. Möjliga värden: `query` , `form_post` (standard) eller `fragment` . |
| omfång | No | Omfattningen av begäran som definieras enligt OAuth2 Identity Provider-specifikationen. Till exempel `openid` , `profile` och `email` . |
| HttpBinding | No | Den förväntade HTTP-bindningen till åtkomsttoken och slut punkter för anspråks-token. Möjliga värden: `GET` eller `POST` .  |
| ResponseErrorCodeParamName | No | Namnet på parametern som innehåller fel meddelandet som returnerades över HTTP 200 (OK). |
| ExtraParamsInAccessTokenEndpointResponse | No | Innehåller de extra parametrar som kan returneras i svaret från **AccessTokenEndpoint** av vissa identitets leverantörer. Svaret från **AccessTokenEndpoint** innehåller till exempel en extra parameter `openid` som, som är en obligatorisk parameter förutom access_token i en frågesträng för **ClaimsEndpoint** . Flera parameter namn ska undantas och avgränsas med kommatecken ",". |
| ExtraParamsInClaimsEndpointRequest | No | Innehåller de extra parametrar som kan returneras i **ClaimsEndpoint** -begäran av vissa identitets leverantörer. Flera parameter namn ska undantas och avgränsas med kommatecken ",". |
| IncludeClaimResolvingInClaimsHandling  | No | För indata-och utgående anspråk anges om [anspråks matchning](claim-resolver-overview.md) ingår i den tekniska profilen. Möjliga värden: `true` , eller `false`   (standard). Om du vill använda en anspråks lösare i den tekniska profilen ställer du in den på `true` . |
| ResolveJsonPathsInJsonTokens  | No | Anger om den tekniska profilen matchar JSON-sökvägar. Möjliga värden: `true` , eller `false` (standard). Använd dessa metadata för att läsa data från ett kapslat JSON-element. I en [OutputClaim](technicalprofiles.md#outputclaims)anger du det `PartnerClaimType` JSON-sökvägar som du vill mata ut. Till exempel: `firstName.localized` , eller `data.0.to.0.email` .|
|token_endpoint_auth_method| No| Anger hur Azure AD B2C skickar Authentication-huvudet till token-slutpunkten. Möjliga värden: `client_secret_post` (standard) och `client_secret_basic` (offentlig för hands version). Mer information finns i [avsnittet OpenID Connect client Authentication](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). |

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

**CryptographicKeys** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_secret | Yes | Klient hemligheten för Identity Provider-programmet. Krypterings nyckeln krävs endast om **response_types** metadata har angetts till `code` . I det här fallet gör Azure AD B2C ett annat anrop till Exchange-auktoriseringskod för en åtkomsttoken. Om metadata har angetts till `id_token` kan du utelämna den kryptografiska nyckeln. |

## <a name="redirect-uri"></a>Omdirigerings-URI

När du konfigurerar en omdirigerings-URI för din identitetsprovider anger du `https://{tenant-name}.b2clogin.com/{tenant-name}.onmicrosoft.com/oauth2/authresp` . Se till att ersätta `{tenant-name}` med klient organisationens namn (till exempel contosob2c). Omdirigerings-URI: n måste vara i gemener.

Exempel:

- [Lägg till Google + som en OAuth2-identitetsprovider med anpassade principer](identity-provider-google-custom.md)
