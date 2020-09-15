---
title: Översikt över Azure Monitor | Microsoft Docs
description: Översikt över Microsoft-tjänster och funktioner som bidrar till en fullständig övervakningsstrategi för dina Azure-tjänster och program.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: 005068c8e81adb9a79a4e6dc7e86a9bfb39902a1
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90088654"
---
# <a name="azure-monitor-overview"></a>Översikt över Azure Monitor

Azure Monitor maximerar tillgängligheten och prestandan för dina program och tjänster genom att leverera en omfattande lösning för att samla in, analysera och agera på telemetri från molnet och lokala miljöer. Det hjälper dig att förstå hur dina program fungerar och identifierar proaktivt problem som påverkar dem och de resurser som de är beroende av.

Några exempel på vad du kan göra med Azure Monitor är:

- Identifiera och diagnostisera problem mellan program och beroenden med [Application Insights](app/app-insights-overview.md).
- Korrelera infrastruktur problem med [Azure Monitor for VMS](insights/vminsights-overview.md) och [Azure Monitor för behållare](insights/container-insights-overview.md).
- Öka detalj nivån i dina övervaknings data med [Log Analytics](log-query/log-query-overview.md) för fel sökning och djup diagnostik.
- Stöd åtgärder i stor skala med [smarta aviseringar](platform/alerts-smartgroups-overview.md) och [automatiserade åtgärder](platform/alerts-action-rules.md).
- Skapa visualiseringar med Azure- [instrumentpaneler](learn/tutorial-logs-dashboards.md) och [arbets böcker](platform/workbooks-overview.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Översikt

I följande diagram ges en översikt över Azure Monitor på hög nivå. I diagrammets centrum finns data lager för mått och loggar, som är de två grundläggande typer av data som används av Azure Monitor. Till vänster visas [källorna till de övervaknings data](platform/data-sources.md) som finns i dessa [data lager](platform/data-platform.md). Till höger finns olika funktioner som Azure Monitor utför med dessa insamlade data, till exempel analys, aviseringar och strömning i externa system.

![Översikt över Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Övervaknings data plattform

Alla data som samlas in av Azure Monitor passar in i en av två grundläggande typer, [mått och loggar](platform/data-platform.md). [Mått](platform/data-platform-metrics.md) är numeriska värden som beskriver någon aspekt av ett system vid en viss tidpunkt. De är lätta och stöder nästan real tids scenarier. [Loggar](platform/data-platform-logs.md) innehåller olika typer av data som är ordnade i poster med olika uppsättningar egenskaper för varje typ. Telemetri som händelser och spår lagras som loggar förutom prestanda data så att alla kan kombineras för analys.

För många Azure-resurser kommer du att se data som samlas in av Azure Monitor direkt på sidan Översikt i Azure Portal. Ta en titt på vilken virtuell dator som helst, så ser du till exempel flera diagram som visar prestanda mått. Klicka på något av diagrammen för att öppna data i [mått Utforskaren](platform/metrics-charts.md) i Azure Portal, vilket gör att du kan skapa diagram över värdena för flera mått över tid.  Du kan visa diagrammen interaktivt eller fästa dem på en instrument panel för att visa dem med andra visualiseringar.

![Diagrammet visar Mät data som flödar till Metrics Explorer som ska användas i visualiseringar.](media/overview/metrics.png)

Loggdata som samlas in av Azure Monitor kan analyseras med [frågor](log-query/log-query-overview.md) för att snabbt hämta, konsolidera och analysera insamlade data.  Du kan skapa och testa frågor med [Log Analytics](./log-query/log-query-overview.md) i Azure Portal och sedan antingen analysera data direkt med olika verktyg eller spara frågor för användning med [visualiseringar](visualizations.md) eller [varnings regler](platform/alerts-overview.md).

Azure Monitor använder en version av [Kusto-frågespråket](/azure/kusto/query/) som används av Azure datautforskaren som är lämplig för enkla logg frågor, men även avancerade funktioner som agg regeringar, kopplingar och smart analys. Du kan snabbt lära dig frågespråket med [flera lektioner](log-query/get-started-queries.md).  Viss vägledning erbjuds användare som redan är bekanta med [SQL](log-query/sql-cheatsheet.md) och [Splunk](log-query/splunk-cheatsheet.md).

![Diagrammet visar loggar data som flödar till Log Analytics för analys.](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Vilka data samlar Azure Monitor in?

Azure Monitor kan samla in data från olika källor. Övervakningen av data för dina program sker i nivåer – från ditt program, operativsystemet och de tjänster som det använder, till själva plattformen. Azure Monitor samlar in data från följande nivåer:

- **Program övervaknings data**: data om prestanda och funktioner i den kod som du har skrivit, oavsett plattform.
- **Övervaknings data för gäst operativ**system: data om det operativ system som programmet körs på. Det kan köras i Azure, i ett annat moln eller lokalt. 
- **Övervakningsdata för Azure-resurser**: Data om hur en Azure-resurs fungerar.
- **Övervaknings data för Azure-prenumeration**: data om drift och hantering av en Azure-prenumeration, samt data om hälso tillståndet och driften av Azure. 
- **Azure-klient övervaknings data**: data om driften av Azure-tjänster på klient nivå, till exempel Azure Active Directory.

När du skapar en Azure-prenumeration och börjar lägga till resurser, till exempel virtuella datorer och webbappar, börjar Azure Monitor samla in data.  [Aktivitets loggar](platform/platform-logs-overview.md) poster när resurser skapas eller ändras. [Mått](platform/data-platform.md) visar hur resursen presterar och vilka resurser den använder. 

Utöka de data som du samlar in i den faktiska åtgärden för resurserna genom att [Aktivera diagnostik](platform/platform-logs-overview.md) och [lägga till en agent](platform/agent-windows.md) för att beräkna resurser. Detta samlar in telemetri för den interna driften av resursen och låter dig konfigurera olika [data källor](platform/agent-data-sources.md) för att samla in loggar och mått från Windows och Linux gäst operativ system. 

Aktivera övervakning för ditt [app Services program](app/azure-web-apps.md) eller en [virtuell dator och ett program för skalnings uppsättning för virtuella datorer](app/azure-vm-vmss-apps.md), så att Application Insights kan samla in detaljerad information om ditt program, inklusive sid visningar, program begär Anden och undantag. Kontrol lera att programmet är tillgängligt genom att konfigurera ett [tillgänglighets test](app/monitor-web-app-availability.md) för att simulera användar trafik.

### <a name="custom-sources"></a>Anpassade källor

Azure Monitor kan samla in loggdata från alla REST-klienter med hjälp av [API: et för data insamling](platform/data-collector-api.md). På så sätt kan du skapa anpassade övervaknings scenarier och utöka övervakningen till resurser som inte visar telemetri via andra källor.

## <a name="insights"></a>Insikter
Övervaknings data är bara användbara om det kan öka din insyn i driften av din dator miljö. Azure Monitor innehåller flera funktioner och verktyg som ger värdefulla insikter om dina program och andra resurser som de är beroende av. [Övervakning av lösningar](insights/solutions.md) och funktioner som [Application Insights](app/app-insights-overview.md) och [Azure Monitor för behållare](insights/container-insights-overview.md) ger djupgående insikter om olika aspekter av programmet och specifika Azure-tjänster. 

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) övervakar tillgänglighet, prestanda och användning av dina webb program oavsett om de finns i molnet eller lokalt. Den använder den kraftfulla data analys plattformen i Azure Monitor för att ge dig djupgående insikter om programmets åtgärder och diagnostisera fel utan att vänta på att en användare rapporterar dem. Application Insights innehåller anslutnings punkter till olika utvecklingsverktyg och integreras med Visual Studio för att stödja dina DevOps-processer.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor för containrar
[Azure Monitor för behållare](insights/container-insights-overview.md) är en funktion som har utformats för att övervaka prestanda för behållar arbets belastningar som distribueras till hanterade Kubernetes-kluster som finns i Azure Kubernetes service (AKS). Det ger dig prestanda synlighet genom att samla in minne och processor mått från styrenheter, noder och behållare som är tillgängliga i Kubernetes via Metrics-API: et. Containerloggar samlas också in.  När du har aktiverat övervakning från Kubernetes-kluster samlas dessa mått och loggar in automatiskt åt dig via en behållar version av Log Analytics agent för Linux.

![Hälso tillstånd för behållare](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor för virtuella datorer
[Azure Monitor for VMS](insights/vminsights-overview.md) övervakar dina virtuella Azure-datorer (VM) i stor skala genom att analysera prestanda och hälsa för dina virtuella Windows-och Linux-datorer, inklusive deras olika processer och sammankopplade beroenden för andra resurser och externa processer. Lösningen innehåller stöd för övervakning av prestanda-och program beroenden för virtuella datorer som finns lokalt eller i en annan moln leverantör.  


![VM-insikter](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Övervakningslösningar
[Övervaknings lösningar](insights/solutions.md) i Azure Monitor är paketerade uppsättningar av logik som ger insikter om ett visst program eller en viss tjänst. De innehåller logik för insamling av övervaknings data för programmet eller tjänsten, [frågor](log-query/log-query-overview.md) för att analysera data och [vyer](./platform/view-designer.md) för visualisering. Övervaknings lösningar är [tillgängliga från Microsoft](./monitor-reference.md) och partners för att tillhandahålla övervakning för olika Azure-tjänster och andra program.

![Övervakningslösningar](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Svara på kritiska situationer
Förutom att göra det möjligt att interaktivt analysera övervaknings data, måste en effektiv övervaknings lösning kunna reagera proaktivt på kritiska villkor som identifieras i de data som samlas in. Detta kan skicka en text eller e-post till en administratör som ansvarar för att undersöka ett problem. Eller så kan du starta en automatiserad process som försöker åtgärda ett fel tillstånd.


### <a name="alerts"></a>Aviseringar
[Aviseringar i Azure Monitor](platform/alerts-overview.md) proaktivt meddela dig om kritiska villkor och eventuellt försök att vidta lämpliga åtgärder. Varnings regler som baseras på mått ger nära real tids aviseringar baserat på numeriska värden, medan regler som baseras på loggar tillåter komplex logik över data från flera källor.

Varnings regler i Azure Monitor använda [Åtgärds grupper](platform/action-groups.md)som innehåller unika uppsättningar av mottagare och åtgärder som kan delas över flera regler. Utifrån dina krav kan åtgärds grupper utföra sådana åtgärder som att använda Webhooks för att få aviseringar att starta externa åtgärder eller integrera med dina ITSM-verktyg.

![Skärm bild som visar aviseringar i Azure Monitor med allvarlighets grad, totala aviseringar och annan information.](media/overview/alerts.png)

### <a name="autoscale"></a>Automatisk skalning
Med autoskalning kan du använda rätt mängd resurser för att hantera belastningen på ditt program. Det gör att du kan skapa regler som använder mått som samlas in av Azure Monitor för att avgöra när du ska lägga till resurser automatiskt för att hantera belastningen och även spara pengar genom att ta bort resurser som är inaktiva. Du anger ett minsta och högsta antal instanser och logiken för när du vill öka eller minska resurserna.

![Diagrammet visar autoskalning, med flera servrar på en rad med etiketten processor tid > 80% och två servrar som har marker ATS som minst tre servrar som aktuell kapacitet och fem som Max.](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Visualiserar övervaknings data
[Visualiseringar](visualizations.md) som diagram och tabeller är effektiva verktyg för att sammanfatta övervaknings data och presentera dem för olika mål grupper. Azure Monitor har egna funktioner för visualisering av övervaknings data och utnyttjar andra Azure-tjänster för att publicera den till olika mål grupper.

### <a name="dashboards"></a>Instrumentpaneler
Med [Azure-instrumentpaneler](../azure-portal/azure-portal-dashboards.md) kan du kombinera olika typer av data, inklusive både mått och loggar, i ett enda fönster i [Azure Portal](https://portal.azure.com). Du kan välja att dela instrument panelen med andra Azure-användare. Element i hela Azure Monitor kan läggas till i en Azure-instrumentpanel förutom utdata från eventuella logg frågor eller mått diagram. Du kan till exempel skapa en instrument panel som kombinerar paneler som visar ett diagram över mått, en tabell med aktivitets loggar, ett användnings diagram från Application Insights och utdata från en logg fråga.

![Skärm bild som visar en Azure-instrumentpanel, som innehåller program-och säkerhets paneler, tillsammans med annan anpassningsbar information.](media/overview/dashboard.png)

### <a name="views"></a>Vyer
[Vyer](./platform/view-designer.md) visar visuellt logg data i Azure Monitor.  Varje vy innehåller en enda panel som går nedåt till en kombination av visualiseringar som stapel-och linje diagram förutom listor som sammanfattar viktiga data.  Övervaknings lösningar innehåller vyer som sammanfattar data för ett visst program och du kan skapa egna vyer för att presentera data från alla logg frågor. Precis som andra element i Azure Monitor kan vyer läggas till i Azure-instrumentpaneler.

![Skärm bild som visar en panel för övervakning av behållar övervakning och den detaljerade vyn som öppnas om du väljer panelen.](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) är en Business Analytics-tjänst som tillhandahåller interaktiva visualiseringar över flera olika data källor och är ett effektivt sätt att göra data tillgängliga för andra inom och utanför din organisation. Du kan konfigurera Power BI att [automatiskt importera logg data från Azure Monitor](./platform/powerbi.md) för att dra nytta av dessa ytterligare visualiseringar.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrera och exportera data
Du har ofta behov av att integrera Azure Monitor med andra system och att bygga anpassade lösningar som använder dina övervaknings data. Andra Azure-tjänster fungerar med Azure Monitor för att tillhandahålla denna integrering.

### <a name="event-hub"></a>Händelsehubb
[Azure Event Hubs](../event-hubs/index.yml) är en strömmande plattform och händelse inmatnings tjänst som kan transformera och lagra data med hjälp av en analys av real tids analys eller batch-/minnes kort. Använd Event Hubs för att [strömma Azure Monitor data](platform/stream-monitoring-data-event-hubs.md) till partner Siem och övervaknings verktyg.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) är en tjänst som gör att du kan automatisera uppgifter och affärs processer med hjälp av arbets flöden som integreras med olika system och tjänster. Det finns aktiviteter som läser och skriver mått och loggar i Azure Monitor, vilket gör att du kan skapa arbets flöden som integreras med en mängd olika system.


### <a name="api"></a>API
Flera API: er är tillgängliga för att läsa och skriva mått och loggar till och från Azure Monitor utöver åtkomst till genererade aviseringar. Du kan också konfigurera och hämta aviseringar. Det ger dig i stort sett obegränsade möjligheter att bygga anpassade lösningar som integreras med Azure Monitor.

## <a name="next-steps"></a>Nästa steg
Läs mer om:

* [Mått och loggar](platform/data-platform.md) för data som samlas in av Azure Monitor.
* [Data källor](platform/data-sources.md) för hur de olika komponenterna i ditt program skickar telemetri.
* [Logg frågor](log-query/log-query-overview.md) för att analysera insamlade data.
* [Metod tips](/azure/architecture/best-practices/monitoring) för övervakning av moln program och-tjänster.
