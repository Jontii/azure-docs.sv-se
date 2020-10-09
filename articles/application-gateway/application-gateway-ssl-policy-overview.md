---
title: Översikt över TLS-princip för Azure Application Gateway
description: Lär dig hur du konfigurerar TLS-princip för Azure Application Gateway och minskar belastningen på kryptering och dekryptering från en Server grupp på Server sidan.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 16c6dd28d47573c2ad5b0d5a331b0dc48e7aacef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85253638"
---
# <a name="application-gateway-tls-policy-overview"></a>Översikt över Application Gateway TLS-policy

Du kan använda Azure Application Gateway för att centralisera TLS/SSL-certifikat hantering och minska belastningen på kryptering och dekryptering från en Server grupp på Server sidan. Med den här centraliserade TLS-hanteringen kan du också ange en central TLS-princip som passar organisationens säkerhets krav. Detta hjälper dig att uppfylla kraven för efterlevnad samt säkerhets rikt linjer och rekommenderade metoder.

TLS-principen inkluderar kontrollen av TLS-protokollets version samt chiffersviter och i vilken ordning chiffer används under en TLS-handskakning. Application Gateway erbjuder två mekanismer för att styra TLS-principen. Du kan använda antingen en fördefinierad princip eller en anpassad princip.

## <a name="predefined-tls-policy"></a>Fördefinierad TLS-princip

Application Gateway har tre fördefinierade säkerhets principer. Du kan konfigurera din gateway med någon av dessa principer för att få rätt säkerhets nivå. Princip namnen är kommenterade efter det år och den månad då de konfigurerades. Varje princip erbjuder olika TLS-protokoll och chiffersviter. Vi rekommenderar att du använder de senaste TLS-principerna för att säkerställa den bästa TLS-säkerheten.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Egenskap  |Värde  |
|---|---|
|Namn     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Default| Sant (om ingen fördefinierad princip har angetts) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Egenskap  |Värde  |
|   ---      |  ---       |
|Namn     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Default| Falskt |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Egenskap  |Värde  |
|---|---|
|Namn     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Default| Falskt |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>Anpassad TLS-princip

Om en fördefinierad TLS-princip måste konfigureras för dina krav måste du definiera en egen anpassad TLS-princip. Med en anpassad TLS-princip har du fullständig kontroll över den lägsta TLS-protokollversion som stöds, samt de chiffersviter som stöds och deras prioritetsordning.
 
### <a name="tlsssl-protocol-versions"></a>TLS/SSL-protokoll versioner

* SSL 2,0 och 3,0 är inaktiverat som standard för alla programgatewayer. Dessa protokoll versioner kan inte konfigureras.
* En anpassad TLS-princip ger dig möjlighet att välja något av följande tre protokoll som lägsta TLS-protokollversion för din Gateway: TLSv1_0, TLSv1_1 och TLSv1_2.
* Om ingen TLS-princip har definierats är alla tre protokollen (TLSv1_0, TLSv1_1 och TLSv1_2) aktiverade.

### <a name="cipher-suites"></a>Chiffersviter

Application Gateway stöder följande chiffersviter som du kan använda för att välja en anpassad princip. Ordningen för chiffersviter avgör prioritetsordningen under TLS-förhandlingen.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> TLS-chiffersviter som används för anslutningen baseras också på typen av certifikat som används. I klient till Application Gateway-anslutningar baseras de chiffersviter som används på typen av Server certifikat i Application Gateway-lyssnaren. De chiffersviter som används i Application Gateway till backend-anslutningspoolen baseras på typen av Server certifikat på backend-poolens servrar.

## <a name="known-issue"></a>Kända problem
Application Gateway v2 stöder för närvarande inte följande chiffer:
- DHE-RSA-AES128-GCM-SHA256
- DHE-RSA-AES128-SHA
- DHE-RSA-AES256-GCM-SHA384
- DHE-RSA-AES256-SHA
- DHE-DSS-AES128-SHA256
- DHE-DSS-AES128-SHA
- DHE-DSS-AES256-SHA256
- DHE-DSS-AES256-SHA

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du konfigurerar en TLS-princip kan du läsa [Konfigurera versioner av TLS-principer och cipher-paket på Application Gateway](application-gateway-configure-ssl-policy-powershell.md).
