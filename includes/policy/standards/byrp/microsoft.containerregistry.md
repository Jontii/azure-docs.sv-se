---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 90f707f9bdf3b3e67e9774315c45f45d23a552d9
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016832"
---
## <a name="azure-security-benchmark"></a>Benchmark för Azure-säkerhet

[Azures säkerhets benchmark](../../../../articles/security/benchmarks/overview.md) ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Om du vill se hur den här tjänsten är fullständigt mappad till Azures säkerhets benchmark kan du läsa mer i [Azure-mappningen för säkerhets benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regelefterlevnad – säkerhet i Azure](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domän |Kontroll-ID |Kontroll rubrik |Policy<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Nätverkssäkerhet |1,1 |Skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandväggen på din Virtual Network |[Container Registry bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regler för regelefterlevnad-HIPAA HITRUST 9,2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Mer information om den här standarden för efterlevnad finns i [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Domän |Kontroll-ID |Kontroll rubrik |Policy<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Uppdelning i nätverk |0805.01 m1Organizational. 12 – 01. m |Organisationens säkerhetsgatewayer (t. ex. brand väggar) tillämpar säkerhets principer och är konfigurerade för att filtrera trafik mellan domäner, blockera obehörig åtkomst och används för att upprätthålla uppdelning mellan interna tråd bundna, interna trådlösa och externa nätverks segment (t. ex. Internet), inklusive DMZs och tvingande principer för åtkomst kontroll för varje domän. |[Container Registry bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Uppdelning i nätverk |0806.01 m2Organizational. 12356-01. m |Organisationernas nätverk är logiskt och fysiskt segmenterade med en definierad säkerhets perimeter och en graderad uppsättning kontroller, inklusive under nätverk för offentligt tillgängliga system komponenter som är logiskt åtskilda från det interna nätverket, baserat på organisationens krav. trafiken styrs baserat på funktioner som krävs och klassificeringen av data/system baserat på en riskbedömning och deras respektive säkerhets krav. |[Container Registry bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Uppdelning i nätverk |0894.01 m2Organizational. 7 – 01. m |Nätverk är åtskiljda från nätverk på produktions nivå när du migrerar fysiska servrar, program eller data till virtualiserade servrar. |[Container Registry bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Nätverks kontroller |0868.09 m3Organizational. 18-09 m |Organisationen skapar en brand Väggs konfiguration för att begränsa inkommande och utgående trafik till den som krävs för data miljön som omfattas. |[Container Registry bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Nätverks kontroller |0869.09 m3Organizational. 19-09. m |Filerna för routerdatorn skyddas och synkroniseras. |[Container Registry bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Nätverks kontroller |0870.09 m3Organizational. 20-09. m |Åtkomst till alla proxyservrar nekas, förutom de värdar, portar och tjänster som uttryckligen krävs. |[Container Registry bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Nätverks kontroller |0871.09 m3Organizational. 22-09. m |Auktoritativa DNS-servrar är åtskilda till interna och externa roller. |[Container Registry bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 – för hands version](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

