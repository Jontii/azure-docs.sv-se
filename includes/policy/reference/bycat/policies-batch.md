---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2950129d50e17ce0d2cd928a897e2aaf7d5d2270
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220457"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Batch konto ska använda Kundhanterade nycklar för att kryptera data](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99e9ccd8-3db9-4592-b0d1-14b1715a4d8a) |Använd Kundhanterade nycklar (CMKs) för att hantera krypteringen i resten av batch-kontots data. Som standard krypteras kund information med tjänst hanterade nycklar, men CMKs krävs ofta för att uppfylla gällande regler för efterlevnad. CMKs gör det möjligt att kryptera data med en Azure Key Vault-nyckel som skapats och ägs av dig. Du har fullständig kontroll och ansvar för nyckel livs cykeln, inklusive rotation och hantering. Läs mer om CMK-kryptering på [https://aka.ms/Batch-CMK](https://aka.ms/Batch-CMK) . |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Batch/Batch_CustomerManagedKey_Audit.json) |
|[Diagnostikloggar i batch-konton måste vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Granska aktivering av diagnostikloggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Mått varnings regler ska konfigureras för batch-konton](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26ee67a2-f81a-4ba8-b9ce-8550bd5ee1a7) |Granska konfigurationen av mått varnings regler för batch-kontot för att aktivera nödvändigt mått |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json) |
|[Åtkomst till offentligt nätverk ska inaktive ras för batch-konton](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c5a0ae-5e48-4738-b093-65e23a060488) |Att inaktivera offentlig nätverks åtkomst på ett batch-konto förbättrar säkerheten genom att se till att ditt batch-konto bara kan nås från en privat slut punkt. Läs mer om hur du inaktiverar offentlig nätverks åtkomst på [https://docs.microsoft.com/azure/batch/private-connectivity](https://docs.microsoft.com/azure/batch/private-connectivity) . |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_DisablePublicNetworkAccess_Audit.json) |
