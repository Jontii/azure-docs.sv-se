---
title: Vanliga frågor och svar om Azure Private Link
description: Lär dig mer om Azures privata länk.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: 91823ff0d324cd30566948fecd86cc441342f14e
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91757052"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Vanliga frågor och svar om Azure Private Link

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Vad är Azure Private-slutpunkt och Azure Private Link service?

- **[Privat Azure-slutpunkt](private-endpoint-overview.md)**: Azure Private-slutpunkt är ett nätverks gränssnitt som ansluter dig privat och säkert till en tjänst som drivs av en privat Azure-länk. Du kan använda privata slut punkter för att ansluta till en Azure PaaS-tjänst som stöder privat länk eller till en egen privat länk tjänst.
- **[Azure Private Link-tjänsten](private-link-service-overview.md)**: Azure Private Link service är en tjänst som skapats av en tjänst leverantör. För närvarande kan en privat länk tjänst kopplas till klient delens IP-konfiguration för en Standard Load Balancer. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Hur skickas trafik när du använder en privat länk?
Trafiken skickas privat med Microsoft stamnätet. Den passerar inte Internet. Azures privata länk lagrar inte kund information.
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>Vad är skillnaden mellan en tjänst slut punkt och en privat slut punkt?
- Privata slut punkter ger nätverks åtkomst till vissa resurser bakom en angiven tjänst som tillhandahåller detaljerad segmentering. Trafiken kan komma åt tjänst resursen från lokalt utan att använda offentliga slut punkter.
- En tjänst slut punkt förblir en offentligt dirigerad IP-adress.  En privat slut punkt är en privat IP i adress utrymmet för det virtuella nätverk där den privata slut punkten har kon figurer ATS.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Vad är förhållandet mellan privat länk tjänst och privat slut punkt?
Flera typer av privata länk resurser stöder åtkomst via privat slut punkt. Resurser är bland annat Azure PaaS Services och din egen privata länk tjänst. Det är en en-till-många-relation. 

En privat länk tjänst tar emot anslutningar från flera privata slut punkter. En privat slut punkt ansluter till en privat länk tjänst.    

## <a name="private-endpoint"></a>Privat slutpunkt 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Kan jag skapa flera privata slut punkter i samma VNet? Kan de ansluta till olika tjänster? 
Ja. Du kan ha flera privata slut punkter i samma VNet eller undernät. De kan ansluta till olika tjänster.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Behöver jag ett dedikerat undernät för privata slut punkter? 
Nej. Du behöver inte ett dedikerat undernät för privata slut punkter. Du kan välja en privat slut punkts-IP från valfritt undernät från det virtuella nätverk där tjänsten distribueras.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>Kan privat slut punkt ansluta till Private Link service mellan Azure Active Directory klienter? 
Ja. Privata slut punkter kan ansluta till privata länk tjänster eller Azure-PaaS över AD-klienter.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Kan privat slut punkt ansluta till Azure PaaS-resurser i Azure-regioner?
Ja. Privata slut punkter kan ansluta till Azure PaaS-resurser i Azure-regioner.

## <a name="private-link-service"></a>Private Link-tjänst
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Vilka är kraven för att skapa en privat länk tjänst? 
Tjänstens Server delar bör finnas i ett virtuellt nätverk och bakom en Standard Load Balancer.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Hur kan jag skala min privata länk tjänst? 
Du kan skala din privata länk tjänst på ett par olika sätt: 
- Lägg till virtuella server delar till poolen bakom Standard Load Balancer 
- Lägg till en IP-adress i den privata länk tjänsten. Vi tillåter upp till 8 IP-adresser per privat länk-tjänst.  
- Lägg till en ny privat länk-tjänst i Standard Load Balancer. Vi tillåter upp till åtta privata länk tjänster per belastningsutjämnare.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Kan jag ansluta min tjänst till flera privata slut punkter?
Ja. En privat länk-tjänst kan ta emot anslutningar från flera privata slut punkter. En privat slut punkt kan dock bara ansluta till en privat länk tjänst.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Hur styr jag exponeringen för min privata länk tjänst?
Du kan styra exponeringen med hjälp av Synlighets konfigurationen i Private Link-tjänsten. Synligheten stöder tre inställningar:

- **Ingen** – endast prenumerationer med RBAC-åtkomst kan hitta tjänsten. 
- Endast **begränsade** prenumerationer som godkänns och RBAC-åtkomst kan hitta tjänsten. 
- **Alla** – alla kan hitta tjänsten. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Kan jag skapa en privat länk-tjänst med Basic Load Balancer? 
Nej. Tjänsten för privata länkar över en Basic Load Balancer stöds inte.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Krävs ett dedikerat undernät för tjänsten för privat länk? 
Nej. Tjänsten för privata länkar kräver inte ett dedikerat undernät. Du kan välja valfritt undernät i ditt VNet där tjänsten distribueras.   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Jag är en tjänste leverantör som använder Azures privata länk. Måste jag se till att alla mina kunder har ett unikt IP-utrymme och inte överlappar mitt IP-utrymme? 
Nej. Azures privata länk tillhandahåller den här funktionen åt dig. Du behöver inte ha överlappande adress utrymme med kundens adress utrymme. 

##  <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azures privata länk](private-link-overview.md)
