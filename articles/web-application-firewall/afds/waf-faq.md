---
title: Azure Web Application-brandvägg i Azures frontend-tjänst – vanliga frågor och svar
description: Den här artikeln innehåller svar på vanliga frågor om brand vägg för webbaserade program på Azures front dörr
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 5b60082db53b458adc53ac23d98731ad1c97b52b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563655"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Vanliga frågor och svar om Azure Web Application-brandvägg i Azure-tjänsten för front dörr

I den här artikeln besvaras vanliga frågor om Azure Web Application Firewall (WAF) på Azures tjänst funktioner i front dörr. 

## <a name="what-is-azure-waf"></a>Vad är Azure WAF?

Azure WAF är en brand vägg för webbaserade program som hjälper till att skydda dina webb program mot vanliga hot som SQL-inmatning, skript körning över flera webbplatser och andra webbplatser. Du kan definiera en WAF-princip som består av en kombination av anpassade och hanterade regler för att kontrol lera åtkomsten till dina webb program.

En Azure WAF-princip kan tillämpas på webb program som finns på Application Gateway eller Azure-frontend-dörrar.

## <a name="what-is-waf-on-azure-front-door"></a>Vad är WAF på Azures frontend-dörr? 

Azures front dörr är ett mycket skalbart, globalt distribuerat program och Content Delivery Network. Azure WAF, när det är integrerat med front dörren, stoppar denial-of-service-och mål program attacker på Azures nätverks gräns, nära angrepps källor innan de går in i ditt virtuella nätverk, ger skydd utan att offra prestanda.

## <a name="does-azure-waf-support-https"></a>Stöder Azure WAF HTTPS?

Front dörren erbjuder TLS-avlastning. WAF är inbyggt integrerat med en front dörr och kan inspektera en begäran när den har dekrypterats.

## <a name="does-azure-waf-support-ipv6"></a>Stöder Azure WAF IPv6?

Ja. Du kan konfigurera IP-begränsning för IPv4 och IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Hur aktuella är de hanterade regel uppsättningarna?

Vi gör våra bästa för att hålla oss på att ändra hotets landskap. När en ny regel har uppdaterats läggs den till i standard regel uppsättningen med ett nytt versions nummer.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Vad är spridnings tiden om jag gör en ändring i WAF-principen?

Att distribuera en WAF-princip globalt tar vanligt vis cirka 5 minuter och slutförs ofta tidigare.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Kan WAF-principer vara olika för olika regioner?

WAF är en global resurs när den integreras med frontend-dörren. Samma konfiguration gäller för alla platser i front dörren.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Hur gör jag för att begränsa åtkomsten till min backend-sida till enbart från Front dörren?

Du kan konfigurera IP-Access Control listan i din server del så att den endast tillåter utgående IP-adressintervall för front luckan och nekar direkt åtkomst från Internet. Service märken stöds för användning i det virtuella nätverket. Du kan också kontrol lera att fältet X-vidarebefordrad-värd-HTTP-rubrik är giltigt för ditt webb program.

## <a name="which-azure-waf-options-should-i-choose"></a>Vilka alternativ för Azure-WAF ska jag välja?

Det finns två alternativ när du tillämpar WAF-principer i Azure. WAF med Azures frontend är en globalt distribuerad lösning för Edge-säkerhet. WAF med Application Gateway är en regional, dedikerad lösning. Vi rekommenderar att du väljer en lösning som baseras på dina övergripande prestanda-och säkerhets krav. Mer information finns i [belastnings utjämning med Azures program leverans Suite](../../frontdoor/front-door-lb-with-azure-app-delivery-suite.md).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Stöder du samma WAF-funktioner på alla integrerade plattformar?

För närvarande stöds inte ModSec BOKNINGs-2.2.9, DATORISERAde boknings regler 3,0 och reglerna för BOKNINGs-3,1 med WAF på Application Gateway. Hastighets begränsning, geo-filtrering och Azure-hanterade standard regel uppsättnings regler stöds bara med WAF på Azures front dörr.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Är DDoS-skydd integrerat med front dörren? 

I globalt distribuerat i Azures nätverks gränser kan Azures front dörr absorbera och geografiskt isolera stora volym attacker. Du kan skapa en anpassad WAF-princip för automatisk blockering och hastighets begränsning av http (s)-attacker som har kända signaturer. Fler fler kan du aktivera DDoS Protection standard på det virtuella nätverk där dina Server delar distribueras. Azure DDoS Protection standard kunder får ytterligare fördelar, inklusive kostnads skydd, SLA-garanti och åtkomst till experter från DDoS Rapid Response-teamet för omedelbar hjälp vid ett angrepp. Mer information finns i [DDoS Protection på front dörren](../../frontdoor/front-door-ddos.md).

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Varför skickas ytterligare begär Anden utöver tröskelvärdet som kon figurer ATS för min hastighets begränsnings regel till min backend-server?

En hastighets begränsnings regel kan begränsa onormalt hög trafik från alla klient-IP-adresser. Du kan konfigurera ett tröskelvärde för antalet webb förfrågningar som tillåts från en klients IP-adress under en varaktighet på en minut eller fem minuter. För detaljerad frekvens kontroll kan hastighets begränsning kombineras med ytterligare matchnings villkor, t. ex. HTTP (S)-parameter matchning. 

Förfrågningar från samma klient når ofta samma frontend-Server. I så fall visas ytterligare begär Anden ovanför tröskelvärdet blockeras omedelbart. 

Det är dock möjligt att förfrågningar från samma klient kan komma från en annan frontend-server som inte har uppdaterat frekvens begränsnings räknaren ännu. Klienten kan till exempel öppna en ny anslutning för varje begäran och tröskelvärdet är låg. I det här fallet skickar den första begäran till den nya frontend-servern den hastighets begränsnings kontrollen. Ett tröskelvärde för hastighets begränsning är vanligt vis högt för att försvara mot denial of Service-attacker från vilken klient-IP-adress som helst. För ett mycket lågt tröskelvärde kan du se fler begär Anden ovanför tröskelvärdet.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure WebApplication-brandväggen](../overview.md).
- Lär dig mer om [Azures front dörr](../../frontdoor/front-door-overview.md).