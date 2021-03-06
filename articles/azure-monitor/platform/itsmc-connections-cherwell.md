---
title: Anslut Cherwell med Anslutningsprogram för hantering av IT-tjänster (ITSM)
description: Den här artikeln innehåller information om hur du Anslutningsprogram för hantering av IT-tjänster (ITSM) Cherwell (ITSMC) i Azure Monitor för att centralt övervaka och hantera ITSM arbets objekt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 73fc13cf2a49d7cacd7540d06c6d0afd9cea68e5
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729751"
---
# <a name="connect-cherwell-with-it-service-management-connector"></a>Anslut Cherwell med Anslutningsprogram för hantering av IT-tjänster (ITSM)

Den här artikeln innehåller information om hur du konfigurerar anslutningen mellan Cherwell-instansen och Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) i Log Analytics för att centralt hantera dina arbets uppgifter.

> [!NOTE]
> Vi föreslår våra Cherwell och förstyrker kunder att använda [webhook-åtgärder](./action-groups.md#webhook) för att Cherwell och bevisa slut punkt som en annan lösning på integrationen.

Följande avsnitt innehåller information om hur du ansluter din Cherwell-produkt till ITSMC i Azure.

## <a name="prerequisites"></a>Förutsättningar

Se till att följande krav är uppfyllda:

- ITSMC installerad. Mer information: [Lägg till anslutningsprogram för hantering av IT-tjänster (ITSM)-lösningen](./itsmc-definition.md#add-it-service-management-connector).
- Ett klient-ID genererades. Mer information: [generera klient-ID för Cherwell](#generate-client-id-for-cherwell).
- Användar roll: administratör.

## <a name="connection-procedure"></a>Anslutnings procedur

Använd följande procedur för att skapa en Cherwell-anslutning:

1. I Azure Portal går du till **alla resurser** och letar efter **Servicedesk (YourWorkspaceName)**

2. Under **arbets ytans data källor** klickar du på **ITSM-anslutningar**.
    ![Ny anslutning](media/itsmc-connections/add-new-itsm-connection.png)

3. Klicka på **Lägg till** längst upp i den högra rutan.

4. Ange informationen enligt beskrivningen i följande tabell och klicka på **OK** för att skapa anslutningen.

> [!NOTE]
> Alla dessa parametrar är obligatoriska.

| **Fält** | **Beskrivning** |
| --- | --- |
| **Anslutnings namn**   | Ange ett namn för den Cherwell-instans som du vill ansluta till ITSMC.  Du använder det här namnet senare när du konfigurerar arbets objekt i den här ITSM/Visa detaljerad Log Analytics. |
| **Partner typ**   | Välj **Cherwell.** |
| **Användarnamn**   | Ange det användar namn för Cherwell som kan ansluta till ITSMC. |
| **Lösenord**   | Ange lösen ordet som är kopplat till det här användar namnet. **Obs:** Användar namn och lösen ord används endast för att skapa autentiseringstoken och lagras inte var som helst i ITSMC-tjänsten.|
| **Server-URL**   | Ange URL: en för den Cherwell-instans som du vill ansluta till ITSMC. |
| **Klient-ID**   | Ange klient-ID för att autentisera anslutningen, som du skapade i din Cherwell-instans.   |
| **Omfång för data synkronisering**   | Välj de Cherwell arbets objekt som du vill synkronisera via ITSMC.  Dessa arbets uppgifter importeras till Log Analytics.   **Alternativ:**  Incidenter, ändrings begär Anden. |
| **Synkronisera data** | Ange antalet senaste dagar som du vill att data ska visas. **Maxgräns**: 120 dagar. |
| **Skapa nytt konfigurations objekt i ITSM-lösningen** | Välj det här alternativet om du vill skapa konfigurations objekt i ITSM-produkten. När det här alternativet är markerat skapar ITSMC det påverkade CIs-objektet som konfigurations objekt (om det inte finns något befintligt CIs) i det ITSM system som stöds. **Standard**: inaktiverat. |

![Cherwell-anslutning](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**När du har anslutit och synkroniserat**:

- De valda arbets objekten från den här Cherwell-instansen importeras till Azure **Log Analytics.** Du kan visa sammanfattningen av dessa arbets uppgifter på Anslutningsprogram för hantering av IT-tjänster (ITSM)s panelen.

- Du kan skapa incidenter från Log Analytics aviseringar eller från logg poster eller från Azure-aviseringar i den här Cherwell-instansen.

Läs mer: [skapa ITSM arbets objekt från Azure-aviseringar](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Generera klient-ID för Cherwell

Använd följande procedur för att generera klient-ID/nyckel för Cherwell:

1. Logga in på din Cherwell-instans som administratör.
2. Klicka på **säkerhets**  >  **Redigera REST API klient inställningar**.
3. Välj **skapa en ny klients** klient  >  **hemlighet**.

    ![Användar-ID för Cherwell](media/itsmc-connections/itsmc-cherwell-client-id.png)

## <a name="next-steps"></a>Nästa steg

* [Översikt över ITSM-anslutningsprogram](itsmc-overview.md)
* [Skapa ITSM arbets objekt från Azure-aviseringar](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Felsöka problem med anslutningsprogram för hantering av IT-tjänster (ITSM)](./itsmc-resync-servicenow.md)