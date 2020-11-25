---
title: 'Självstudie: kontroll av regelefterlevnad – Azure Security Center'
description: 'Självstudie: Lär dig hur du kan förbättra din efterlevnad med hjälp av Azure Security Center.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2020
ms.author: memildin
ms.openlocfilehash: 304604b649381daa887b4d313ffd0f773f015a0b
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122223"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Självstudie: Förbättra regelefterlevnaden

Azure Security Center hjälper till att effektivisera processen för att uppfylla krav på efterlevnad av efterlevnad, med hjälp av **instrument panelen för kontroll av efterlevnad**. I instrumentpanelen ger dig Security Center insikter om din efterlevnadsstatus baserat på kontinuerliga utvärderingar av Azure-miljön. Security Center analyserar riskfaktorer i din hybrid moln miljö enligt rekommenderade säkerhets metoder. Dessa utvärderingar mappas till kompatibilitetskontroller från en uppsättning standarder som stöds. På instrument panelen för kontroll av efterlevnad kan du se status för alla utvärderingar i din miljö i samband med en viss standard eller regel. När du arbetar med rekommendationerna och minskar riskfaktorerna i din miljö förbättras position.

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Utvärdera efterlevnaden av efterlevnaden med hjälp av instrument panelen för regelefterlevnad
> * Förbättra din efterlevnadsstatus genom att vidta åtgärder enligt rekommendationer

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att gå igenom de funktioner som beskrivs i den här självstudien måste [Azure Defender](azure-defender.md) vara aktiverat. Du kan prova Azure Defender kostnads fritt i 30 dagar.

##  <a name="assess-your-regulatory-compliance"></a>Utvärdera din regelefterlevnad

Security Center utvärderar kontinuerligt konfigurationen av dina resurser för att identifiera säkerhetsproblem. Dessa utvärderingar presenteras som rekommendationer som fokuserar på att förbättra din säkerhetshygien. På instrument panelen för övervakning av efterlevnad kan du Visa en uppsättning kompatibla standarder med alla krav, där kraven som stöds är mappade till tillämpliga säkerhets utvärderingar. Det gör att du kan visa din efterlevnadsstatus med avseende på standard, baserat på statusen för dessa utvärderingar.

Instrumentpanelens vy för efterlevnad hjälper dig att fokusera din uppmärksamhet på efterlevnadsluckor i enlighet med en standard eller förordning som är viktig för dig. Med den här fokuserade vyn kan du övervaka din kompatibilitetspoäng över tid i dynamiska moln- och hybridmiljöer.

>[!NOTE]
> Som standard stöder Security Center följande reglerande standarder: Azure CIS, PCI DSS 3,2, ISO 27001 och SOC TSP. 
>
> Med funktionen [Dynamic Compliance packages (för hands version)](update-regulatory-compliance-packages.md) kan du uppgradera de standarder som visas på instrument panelen för kontroll av efterlevnad till de nya *dynamiska* paketen. Du kan också använda samma förhands gransknings funktion för att lägga till nya efterlevnadsprinciper och övervaka efterlevnaden av ytterligare standarder. 

1. Från Security Center menyn väljer du regelefterlevnad **.** <br>
Överst på skärmen visas en instrumentpanel med en översikt över din efterlevnadsstatus med en uppsättning efterlevnadsföreskrifter som stöds. Du kan se din övergripande kompatibilitetspoäng och antalet godkända och icke godkända utvärderingar som är associerade med varje standard.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Instrument panel för regler för efterlevnad":::

1. Välj en flik för en efterlevnadsprincip som är relevant för dig (1). Du ser vilka prenumerationer som standarden tillämpas på (2) och listan över alla kontroller för den standarden (3). För tillämpliga kontroller kan du Visa information om att skicka och stoppa utvärderingar som är associerade med kontrollen (4) samt antalet resurser som påverkas (5). Vissa kontroller är nedtonade. De här kontrollerna har inga associerade Security Center-utvärderingar. Kontrol lera kraven för dessa och utvärdera dem i din miljö på egen hand. Vissa av dessa kan vara processbaserade och inte tekniska.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Utforska informationen om kompatibilitet med en speciell standard":::

1. Klicka på **Hämta rapport** om du vill skapa och ladda ned en PDF-rapport som sammanfattar din aktuella kompatibilitetsstatus för en viss standard.

    Rapporten ger en översikt över din kompatibilitetsstatus för den valda standarden baserat på Security Center utvärderings data och organiseras enligt kontrollerna i denna specifika standard. Rapporten kan delas med relevanta intressenter och kan ge bevis på interna och externa revisorer.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Hämta Kompatibilitetsrapport":::

## <a name="improve-your-compliance-posture"></a>Förbättra din efterlevnadsstatus

Utifrån informationen i instrument panelen för kontroll av efterlevnad kan du förbättra position genom att lösa rekommendationer direkt i instrument panelen.

1.  Klicka dig igenom några icke godkända utvärderingar som visas i instrumentpanelen för att visa information om den här rekommendationen. Varje rekommendation innehåller en uppsättning steg som ska följas för att lösa problemet.

1.  Du kan välja en viss resurs för att visa mer information och lösa rekommendationerna för den resursen. <br>I till exempel **Azure CIS-1.1.0 (ny)** kan du välja rekommendations **disk kryptering ska tillämpas på virtuella datorer**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Välja en rekommendation från en standard leads direkt till rekommendations informations Sidan":::

1. I det här exemplet kommer du att **Take action** komma åt den virtuella Azure-datorns sidor i Azure Portal, där du kan öppna fliken **säkerhet** och aktivera kryptering:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Knappen för att vidta åtgärder på sidan med rekommendations information leder till reparations alternativen":::

    Mer information om hur du utför rekommendationerna finns i artikeln [Utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).

1.  När du vidtar åtgärder för att lösa rekommendationerna visas din påverkan i rapporten i instrumentpanelen för efterlevnad eftersom din kompatibilitetspoäng ökar.

    > [!NOTE]
    > Utvärderingarna körs ungefär var 12: e timme, så du kommer att se påverkan på dina efterlevnadsprinciper endast efter nästa körning av den relevanta utvärderingen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att använda Security Centers instrument panel för kontroll av efterlevnad för att:

-   Visa och övervaka din efterlevnadsstatus i förhållande till standarder och föreskrifter som är viktiga för dig.
-   Förbättra din kompatibilitetsstatus genom att utföra relevanta rekommendationer och se dina kompatibilitetspoäng öka.

Instrument panelen för kontroll av efterlevnad kan avsevärt förenkla processen för regelefterlevnad och avsevärt minska den tid som krävs för att samla in regelefterlevnad för din Azure-och hybrid miljö.

Mer information finns i följande relaterade artiklar:

-   [Uppdatera till dynamiska efterlevnadsprinciper i din instrument panel för efterlevnad (för hands version)](update-regulatory-compliance-packages.md) – Läs mer om den här förhands gransknings funktionen som gör att du kan uppdatera de standarder som visas på instrument panelen för regelefterlevnad till de nya *dynamiska* paketen. Du kan också använda samma förhands gransknings funktion för att lägga till nya efterlevnadsprinciper och övervaka efterlevnaden av ytterligare standarder. 
-   [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.
-   [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md) – lär dig hur du använder rekommendationer i Azure Security Center för att skydda dina Azure-resurser.
-   [Förbättra dina säkra poäng i Azure Security Center](secure-score-security-controls.md) – lär dig hur du prioriterar sårbarheter och säkerhets rekommendationer för att förbättra din säkerhets position.
