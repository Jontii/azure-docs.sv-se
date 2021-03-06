---
title: Anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure Monitor
description: Den här artikeln innehåller information om hur du ansluter dina ITSM-produkter/-tjänster med Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Azure Monitor för att centralt övervaka och hantera ITSM arbets objekt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: a372cdcd05267f3bdb093f676948a79c473ad955
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/29/2020
ms.locfileid: "97804028"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Ansluta ITSM-produkter/-tjänster med anslutningsprogrammet för hantering av IT-tjänster (ITSM)
Den här artikeln innehåller information om hur du konfigurerar anslutningen mellan din ITSM-produkt/-tjänst och Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Log Analytics för att centralt hantera dina arbets uppgifter. Mer information om ITSMC finns i [Översikt](./itsmc-overview.md).

Följande ITSM-produkter/-tjänster stöds. Välj produkten om du vill visa detaljerad information om hur du ansluter produkten till ITSMC.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Styrka](./itsmc-connections-provance.md)

> [!NOTE]
> Vi föreslår våra Cherwell och förstyrker kunder att använda [webhook-åtgärder](./action-groups.md#webhook) för att Cherwell och bevisa slut punkt som en annan lösning på integrationen.

## <a name="next-steps"></a>Nästa steg

* [Felsöka problem med anslutningsprogram för hantering av IT-tjänster (ITSM)](./itsmc-resync-servicenow.md)