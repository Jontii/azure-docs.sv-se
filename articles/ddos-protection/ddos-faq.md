---
title: Azure DDoS Protection vanliga vanliga frågor och svar
description: Lär dig hur Azure DDoS Protection Standard tillhandahåller skydd mot DDOS-attacker när du kombinerar det med bästa praxis för dina program.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: yitoh
ms.openlocfilehash: 0873705e105710873be5d024269d40deb1c85e2b
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94505435"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Azure DDoS Protection vanliga vanliga frågor och svar

I den här artikeln besvaras vanliga frågor om Azure DDoS Protection standard. 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>Vad är en DDoS-attack (distributed denial of Service)?
Distribuerad denial of service eller DDoS är en typ av attack där en angripare skickar fler förfrågningar till ett program än vad programmet kan hantera. Den resulterande inverkan är resurser som släpps, vilket påverkar programmets tillgänglighet och förmåga att betjäna sina kunder. Under de senaste åren har branschen sett en skarp ökning av attacker, med attacker som blir mer sofistikerade och större. DDoS-attacker kan riktas mot vilken slut punkt som helst som är offentligt tillgänglig via Internet.

## <a name="what-is-azure-ddos-protection-standard-service"></a>Vad är Azure DDoS Protection standard tjänsten?
Azure DDoS Protection standard, kombinerat med bästa praxis för program design, innehåller förbättrade DDoS-funktioner för att skydda mot DDoS-attacker. Den justeras automatiskt för att hjälpa till att skydda dina Azure-resurser i ett virtuellt nätverk. Det är enkelt att aktivera skydd på nya eller befintliga virtuella nätverk och det krävs inga program-eller resurs ändringar. Det har flera fördelar jämfört med den grundläggande tjänsten, inklusive loggning, avisering och telemetri. Se [Azure DDoS Protection standard-översikt](ddos-protection-overview.md). 

## <a name="what-about-protection-at-the-service-layer-layer-7"></a>Vad gäller skydd på tjänst skiktet (Layer 7)?
Kunder kan använda Azure DDoS Protection-tjänsten i kombination med [Application Gateway WAF SKU](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview) för skydd både på nätverks nivå (Layer 3 och 4, som erbjuds av Azure DDoS Protection-tjänst) och på program nivå (Layer 7 som erbjuds av Application Gateway WAF SKU).

## <a name="are-services-unsafe-in-azure-without-the-service"></a>Är tjänsterna osäkra i Azure utan tjänsten?
Tjänster som körs på Azure skyddas av Azure DDoS Protection Basic som är på plats för att skydda Azures infrastruktur. Skyddet som skyddar infrastrukturen har dock ett mycket högre tröskelvärde än de flesta program har kapacitet att hantera och ger inte telemetri eller avisering, så medan en trafik volym kan uppfattas som oskadlig av plattformen, kan den vara förödande till programmet som tar emot den. 

Genom att integrera med Azure DDoS Protection standard tjänsten får programmet särskild övervakning för att identifiera attacker och programspecifika tröskelvärden. En tjänst skyddas med en profil som är justerad till den förväntade trafik volymen, vilket ger ett mycket tätt skydd mot DDoS-attacker.

## <a name="what-are-the-supported-protected-resource-types"></a>Vilka är de skyddade resurs typerna som stöds?
Offentliga IP-adresser i ARM-baserade virtuella nätverk är för närvarande den enda typen av skyddad resurs. PaaS Services (Multiklient) stöds inte vid uppvisad. Se [Azure DDoS Protection standard referens arkitekturer](ddos-protection-reference-architectures.md).

## <a name="are-classicrdfe-protected-resources-supported"></a>Stöds klassiska/RDFE-skyddade resurser?
Endast ARM-baserade skyddade resurser stöds i för hands versionen. Virtuella datorer i klassiska/RDFE-distributioner stöds inte. Stöd är för närvarande inte planerat för klassiska/RDFE-resurser. Se [Azure DDoS Protection standard referens arkitekturer](ddos-protection-reference-architectures.md).

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>Kan jag skydda lokala resurser med DDoS Protection?
Du måste ha de offentliga slut punkterna för din tjänst som är kopplade till ett VNet i Azure aktiverade för DDoS-skydd. Exempel design är:
- Web Sites (IaaS) i Azure-och backend-databaser i Data Center på lokal. 
- Application Gateway i Azure (DDoS-skydd aktiverat på App Gateway/WAF) och webbplatser i lokal data Center.

Se [Azure DDoS Protection standard referens arkitekturer](ddos-protection-reference-architectures.md).

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>Kan jag registrera en domän utanför Azure och koppla den till en skyddad resurs som VM eller ELB?
För offentliga IP-scenarier stöder DDoS Protection tjänsten alla program oavsett var den associerade domänen är registrerad eller värdbaserad så länge som den associerade offentliga IP-adressen finns på Azure. 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>Kan jag manuellt konfigurera DDoS-principen som tillämpas på virtuella nätverk/offentliga IP-adresser?
Nej, princip anpassning är inte tillgänglig just nu.

## <a name="can-i-allowlistbloclist-specific-ip-addresses"></a>Kan jag tillåten/bloclist vissa IP-adresser?
Nej, manuell konfiguration är inte tillgänglig just nu.

## <a name="how-can-i-test-ddos-protection"></a>Hur kan jag testa DDoS Protection?
Se [testa genom simuleringar](test-through-simulations.md).

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>Hur lång tid tar det för måtten att läsas in på portalen?
Måtten bör visas på portalen inom 5 minuter. Om din resurs är utsatt för en attack börjar andra mått visas på portalen inom 5-7 minuter. 
    



