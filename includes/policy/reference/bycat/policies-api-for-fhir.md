---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ab5955517331744a3b3ea7d2e8929e2651e24373
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99215125"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure API för FHIR bör använda en kundhanterad nyckel (CMK) för att kryptera data i vila](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |Använd en kundhanterad nyckel för att kontrol lera krypteringen i resten av de data som lagras i Azure API för FHIR när detta är ett krav för regler eller efterlevnad. Kundhanterade nycklar levererar också Double Encryption genom att lägga till ett andra lager av kryptering ovanpå det som är standard med tjänst hanterade nycklar. |granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[Azure API för FHIR bör använda privat länk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API för FHIR måste ha minst en godkänd privat slut punkts anslutning. Klienter i ett virtuellt nätverk kan på ett säkert sätt få åtkomst till resurser som har privata slut punkts anslutningar via privata länkar. Mer information finns på: [https://aka.ms/fhir-privatelink](https://aka.ms/fhir-privatelink) . |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[CORS bör inte tillåta varje domän åtkomst till ditt API för FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |Resurs delning mellan ursprung (CORS) bör inte tillåta alla domäner att komma åt ditt API för FHIR. Om du vill skydda ditt API för FHIR ska du ta bort åtkomsten för alla domäner och uttryckligen definiera de domäner som tillåts att ansluta. |granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
