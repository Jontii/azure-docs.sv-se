---
title: Azure Security Center och Azure Container Registry
description: Lär dig mer om att skanna behållar register med Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 1335b1034304b7efe2b113f7ff2d2927fea41638
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977355"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>Azure Container Registry avbildnings genomsökning av Security Center

Azure Container Registry (ACR) är en hanterad privat Docker-registerpost som lagrar och hanterar behållar avbildningar för Azure-distributioner i ett centralt register. Den är baserad på Docker-registret 2,0 med öppen källkod.

Aktivera **Azure Defender för behållar register** för djupare insyn i säkerhets riskerna för avbildningarna i dina Azure Resource Managerbaserade register. Aktivera eller inaktivera planen på prenumerations nivå för att ta med alla register i en prenumeration. Den här funktionen debiteras per avbildning, som visas på [sidan med priser](security-center-pricing.md). Genom att aktivera Azure Defender ser du till att Security Center är redo att skanna bilder som skickas till registret. 


## <a name="when-are-images-scanned"></a>När skannas bilder?

När en bild skickas till ditt register, Security Center skannar automatiskt avbildningen. Om du vill utlösa en genomsökning av en avbildning kan du skicka den till din lagrings plats.

När genomsökningen är klar (vanligt vis efter cirka 2 minuter, men kan vara upp till 15 minuter), är avgöranden tillgängliga som Security Center rekommendationer som detta:

[![Exempel på Azure Security Center rekommendation om sårbarheter som identifierats i en Azure Container Registry (ACR) värd avbildning](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Fördelar med integrering

Security Center identifierar Azure Resource Manager baserade ACR-register i din prenumeration och erbjuder sömlöst:

* **Azure – ursprunglig sårbarhets sökning** för alla publicerade Linux-avbildningar. Security Center skannar avbildningen med hjälp av en skanner från den branschledande sårbara sårbarheten hos leverantören, Qualys. Den här interna lösningen integreras sömlöst som standard.

* **Säkerhets rekommendationer** för Linux-avbildningar med kända sårbarheter. Security Center innehåller information om varje rapporterat sårbarhets-och allvarlighets GRADS klassificering. Dessutom ger den vägledning för hur du kan åtgärda de enskilda säkerhets risker som finns på varje avbildning som skickas till registret.

![Azure Security Center och Azure Container Registry (ACR) Översikt på hög nivå](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Vanliga frågor och svar om Azure Container Registry avbildnings genomsökning

### <a name="how-does-security-center-scan-an-image"></a>Hur skannar Security Center en avbildning?
Avbildningen hämtas från registret. Den körs sedan i en isolerad sandbox med Qualys-skannern som extraherar en lista över kända sårbarheter.

Security Center filtrerar och klassificerar resultat från skannern. När en bild är felfri, Security Center Markera den som sådan. Security Center skapar endast säkerhets rekommendationer för avbildningar som har problem att lösa. Genom att meddela om det uppstår problem kan Security Center minska risken för oönskade informations aviseringar.

### <a name="how-often-does-security-center-scan-my-images"></a>Hur ofta Security Center Skanna mina bilder?
Avbildnings genomsökningar utlöses vid varje push-överföring.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Kan jag få Sök resultatet via REST API?
Ja. Resultaten är under [Underbedömningar REST API](/rest/api/securitycenter/subassessments/list/). Du kan också använda Azure Resource Graph (ARG), Kusto API för alla resurser: en fråga kan hämta en speciell sökning.
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Vilka register typer genomsöks? Vilka typer faktureras?
I avsnittet tillgänglighet visas de typer av behållar register som stöds av Azure Defender för behållar register. 

Om register som inte stöds är anslutna till din Azure-prenumeration genomsöks de inte och du debiteras inte för dem.


## <a name="next-steps"></a>Nästa steg

Mer information om Security Center behållar säkerhetsfunktioner finns i:

* [Azure Security Center-och behållar säkerhet](container-security.md)

* [Integration med Azure Kubernetes Service](azure-kubernetes-service-integration.md)


