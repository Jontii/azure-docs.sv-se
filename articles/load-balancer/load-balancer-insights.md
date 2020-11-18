---
title: Insikter för Azure Load Balancer
description: Använd belastnings Utjämnings insikter för att uppnå snabb lokalisering av fel och välgrundade design beslut
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: errobin
ms.openlocfilehash: d57dfd0a496e71c1f0e6ddea839723da35bc5f76
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686250"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>Använda insikter för att övervaka och konfigurera din Azure Load Balancer

Med hjälp av Azure Monitor för nätverk får du funktionella beroende visualiseringar och förkonfigurerade mått instrument paneler för dina belastningsutjämnare. Med dessa visuella objekt får du hjälp att fatta välgrundade beslut om design och snabbt lokalisera, diagnostisera och åtgärda eventuella fel.

>[!NOTE] 
>Observera att den här funktionen är en för hands version och att den förinställda beroende vyn och den förkonfigurerade instrument panelen kan ändras för att förbättra den här upplevelsen

>[!IMPORTANT]
>Standard Load Balancer krävs för att se mått från namn området Load Balancer på instrument panelen förkonfigurerade mått. Du kommer fortfarande att kunna se mått från VM, skalnings uppsättning för virtuella datorer och namn områden för anslutnings övervakaren. Vi rekommenderar dock [att du uppgraderar till standard](https://docs.microsoft.com/azure/load-balancer/upgrade-basic-standard) för alla produktions arbets belastningar för att dra nytta av den robusta uppsättningen Load Balancer mått.

## <a name="functional-dependency-view"></a>Funktionell beroende vy

Med vyn för funktionella beroenden kan du skapa bilder även de mest komplexa inställningarna för belastningsutjämnare. Med visuell feedback på din senaste Load Balancer konfiguration kan du göra uppdateringar samtidigt som du håller din konfiguration i åtanke.

Du kan komma åt den här vyn genom att gå till bladet med insikter i din Load Balancer-resurs i Azure.

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="Depecition för den funktionella beroende vyn. Belastningsutjämnarens klient del kan ses som anslutning till Server delens medlemmar via de regler som kon figurer ATS. För Standard Load Balancer är raderna från belastnings Utjämnings regler till Server dels instanserna färgkodade baserat på status för hälso avsökning." border="true":::

För standardload-belastningsutjämnare, färgkodas dina resurser för backend-poolen med hälso avsöknings status som anger den aktuella tillgängligheten för din backend-pool för att betjäna trafik. Tillsammans med topologin ovan visas en tidsgrafisk graf med hälso status, vilket ger en ögonblicks bild av hälso tillståndet för programmet.

## <a name="metrics-dashboard"></a>Instrumentpanelen med mätvärden

Från bladet Insights i Load Balancer kan du välja mer detaljerade mått för att visa en förkonfigurerad [Azure Monitor arbets bok](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview) som innehåller mått som är relevanta för särskilda aspekter av din Load Balancer. På den här instrument panelen visas Load Balancer status och länkar till relevant dokumentation överst på sidan.

Först visas fliken Översikt. Du kan navigera bland de tillgängliga flikarna som innehåller visuella objekt som är relevanta för en speciell aspekt av din Load Balancer. Uttrycklig vägledning för var och en finns på instrument panelen längst ned på varje flik.

Flikarna för instrument panelen är tillgängliga:
* Översikt
* Tillgänglighet för klient- och serverdel
* Dataflöde
* Flödesfördelning
* Anslutningsövervakare
* Måttdefinitioner 

### <a name="overview-tab"></a>Fliken Översikt
Fliken Översikt innehåller ett sökbart rutnät med den övergripande statusen för tillgänglighet för data Sök vägar och hälso avsöknings status för varje klient del IP-adresser som är kopplade till din Load Balancer. Dessa mått anger om klient delens IP-adress svarar och beräknings instanserna i din backend-pool besvaras individuellt för inkommande anslutningar.

Du kan också visa det övergripande data flödet för varje klient dels-IP på den här sidan för att få en uppfattning om huruvida du skapar och tar emot förväntade trafik nivåer. Rikt linjerna längst ned på sidan leder till lämplig flik om du ser eventuella oregelbundna värden.

### <a name="frontend-and-backend-availability-tab"></a>Fliken klient dels-och backend-tillgänglighet
Flikarna klient del och backend-tillgänglighet visar data Sök vägens data flöde och status mått för hälso avsökningar som presenteras i några användbara vyer. Det första diagrammet visar det sammanlagda värdet så att du kan avgöra om det är ett problem. Resten av diagrammen visar dessa mått delas av olika dimensioner så att du kan felsöka och identifiera källorna till eventuella inkommande tillgänglighets problem.

Ett arbets flöde för att visa dessa grafer finns längst ned på sidan med vanliga orsaker till olika problem. 

### <a name="data-throughput-tab"></a>Fliken Data flöde
På fliken Data flöde kan du granska inkommande och utgående data flöde för att identifiera om dina trafik mönster är som förväntat. Den visar inkommande och utgående data genom strömning av klient delens IP-adress och frontend-port så att du kan identifiera om hur de tjänster som du kör utför individuellt.

### <a name="flow-distribution"></a>Flödesfördelning
Fliken flödes distribution hjälper dig att visualisera och hantera antalet flöden som dina Server dels instanser tar emot och skapar. Den visar skapande frekvensen för flödet och antalet flöden för inkommande och utgående trafik samt nätverks trafiken för varje virtuell dator och skalnings uppsättnings instans för virtuell dator tas emot. 

Dessa vyer kan ge dig feedback om din Load Balancer-konfiguration eller trafik mönster leder till Bal anse rad trafik. Om du till exempel har konfigurerat tillhörigheten för sessionen och en enskild klient gör ett oproportionerligt antal begär Anden. Du får också veta om du närmar dig [gränsen för antalet virtuella datorer per](https://docs.microsoft.com/azure/virtual-network/virtual-machine-network-throughput#flow-limits-and-recommendations) virtuell dator storlek.

### <a name="connection-monitors"></a>Anslutningsövervakare
På fliken anslutnings övervakare visas svars tiden på en global karta för alla [anslutnings övervakare](https://docs.microsoft.com/azure/network-watcher/connection-monitor)  som du har konfigurerat. Dessa visuella objekt ger värdefull information för tjänster med strikt latens krav. För att uppfylla dina krav kan du behöva lägga till ytterligare regionala distributioner eller flytta till en [belastnings Utjämnings modell över flera regioner](https://docs.microsoft.com/azure/load-balancer/cross-region-overview)

### <a name="metric-definitions"></a>Måttdefinitioner
Fliken mått definitioner innehåller all information som visas i artikeln om [flerdimensionella mått](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics).

## <a name="next-steps"></a>Nästa steg
* Granska instrument panelen och ge feedback med hjälp av länken nedan om det finns något som kan förbättras
* [Läs statistik dokumentationen för att se hur varje mått beräknas](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics)
* [Skapa anslutnings Övervakare för din Load Balancer](https://docs.microsoft.com/azure/network-watcher/connection-monitor)
* [Skapa egna arbets böcker](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview), du kan ta inspiration genom att klicka på knappen Redigera på instrument panelen för detaljerade mått
