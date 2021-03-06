---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b631f447a5c43fb1695b6e07ec387c83747a8613
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220550"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Behållar register ska krypteras med en kundhanterad nyckel (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Använd Kundhanterade nycklar för att hantera krypteringen i resten av innehållet i dina register. Som standard krypteras data i vila med tjänst hanterade nycklar, men Kundhanterade nycklar (CMK) krävs ofta för att uppfylla gällande regler för efterlevnad. CMKs gör det möjligt att kryptera data med en Azure Key Vault-nyckel som skapats och ägs av dig. Du har fullständig kontroll och ansvar för nyckel livs cykeln, inklusive rotation och hantering. Läs mer om CMK-kryptering på [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) . |Granska, neka, inaktive rad |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Behållar register ska inte tillåta obegränsad nätverks åtkomst](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Azure Container Registration som standard accepterar anslutningar via Internet från värdar i ett nätverk. För att skydda dina register från potentiella hot kan du tillåta åtkomst från enbart vissa offentliga IP-adresser eller adress intervall. Om registret inte har en IP-eller brand Väggs regel eller ett konfigurerat virtuellt nätverk, visas det i resurser som inte är felfria. Lär dig mer om att Container Registry nätverks regler här: [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) och här [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) . |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Behållar register ska använda privat länk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Med Azures privata länk kan du ansluta ditt virtuella nätverk till Azure-tjänster utan en offentlig IP-adress på källan eller målet. Den privata länk plattformen hanterar anslutningen mellan konsumenter och tjänster över Azures stamnät nätverk. Genom att mappa privata slut punkter till dina behållar register i stället för hela tjänsten, skyddas du också mot data läckage. Läs mer på: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) . |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
