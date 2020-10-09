---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 3b42d5fbcfb19f08b46241dbe92e6a300bec1df6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "81274997"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Så svarar APIM-proxyservern med SSL-certifikat i TLS-handskakning

### <a name="clients-calling-with-sni-header"></a>Klienter som anropar med SNI-huvud
Om kunden har en eller flera anpassade domäner som kon figurer ATS för proxy kan APIM svara på HTTPS-begäranden från den eller de anpassade domänerna (till exempel contoso.com) och standard domän (till exempel apim-service-name.azure-api.net). APIM svarar med lämpligt Server certifikat baserat på informationen i Servernamnindikator (SNI)-huvudet.

### <a name="clients-calling-without-sni-header"></a>Klienter som anropar utan SNI-huvud
Om kunden använder en-klient, som inte skickar [SNI](https://tools.ietf.org/html/rfc6066#section-3) -huvudet, skapar APIM svar baserat på följande logik:

* Om tjänsten bara har en anpassad domän som kon figurer ATS för proxy, är standard certifikatet det certifikat som utfärdades till den anpassade proxy-domänen.
* Om tjänsten har konfigurerat flera anpassade domäner för proxy (stöds i **Developer** -och **Premium** -nivån) kan kunden ange vilket certifikat som ska vara standard certifikat. Om du vill ange standard certifikatet ska egenskapen [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/apimanagementservice/createorupdate#hostnameconfiguration) vara inställd på True ("defaultSslBinding": "true"). Om kunden inte anger egenskapen, är standard certifikatet det certifikat som utfärdas till en standard-proxyvärd som finns på *. azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Stöd för begäran om att skicka/publicera med stor nytto Last

APIM proxyserver stöder begäran med stor nytto last vid användning av certifikat på klient sidan i HTTPS (till exempel nytto Last > 40 KB). För att förhindra serverns begäran från frysning kan kunderna ange egenskapen ["negotiateClientCertificate": "true"](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) på proxy-värdnamnet. Om egenskapen har angetts till True begärs klient certifikatet vid SSL/TLS-anslutning, innan ett utbyte av HTTP-förfrågningar skickas. Eftersom inställningen gäller för proxy- **värdnamnet** , begär alla anslutnings begär Anden om klient certifikatet. Kunder kan konfigurera upp till 20 anpassade domäner för proxy (stöds bara på **Premium** -nivån) och kringgå den här begränsningen.

