---
title: Log-baserade och föraggregerade mått i Azure Application Insights | Microsoft Docs
description: Varför ska man använda loggbaserade och föraggregerade mått i Azure Application Insights
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f7bfa15b12618715bf0d911e4b4927a1fa327107
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91539137"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Loggbaserade och föraggregerade mått i Application Insights

I den här artikeln förklaras skillnaden mellan "traditionella" Application Insights mått som baseras på loggar och föraggregerade mått. Båda typerna av mått är tillgängliga för användarna av Application Insights och var och en ger ett unikt värde för övervakning av program hälsa, diagnostik och analys. Utvecklare som instrumenterar program kan bestämma vilken typ av mått som passar bäst för ett visst scenario, beroende på programmets storlek, förväntade telemetri och affärs krav för mått precision och avisering.

## <a name="log-based-metrics"></a>Loggbaserade mått

Tidigare var data modellen för program övervakning av telemetri i Application Insights endast baserad på ett litet antal fördefinierade typer av händelser, till exempel begär Anden, undantag, beroende anrop, sid visningar osv. Utvecklare kan använda SDK: n för att antingen generera dessa händelser manuellt (genom att skriva kod som explicit anropar SDK) eller så kan de förlita sig på automatisk insamling av händelser från automatisk Instrumentation. I båda fallen lagrar Application Insights Server delen alla insamlade händelser som loggar och Application Insights blad i Azure Portal fungerar som ett analys-och diagnostikverktyg för visualisering av händelsebaserade data från loggar.

Genom att använda loggar för att behålla en fullständig uppsättning händelser kan du få bra analytiska och diagnostiska värden. Du kan till exempel få ett exakt antal förfrågningar till en viss URL med antalet distinkta användare som har gjort dessa anrop. Eller så kan du få detaljerade diagnostiska spårningar, inklusive undantag och beroende anrop för en användarsession. Den här typen av information kan avsevärt förbättra synligheten i program hälsan och användningen, så att du kan minska den tid som krävs för att diagnostisera problem med en app.

Samtidigt kan det vara opraktiskt att samla in en fullständig uppsättning händelser (eller till och med omöjligt) för program som genererar en stor mängd telemetri. För situationer när mängden händelser är för hög, Application Insights implementera flera metoder för att minska en telemetri, till exempel [sampling](./sampling.md) och [filtrering](./api-filtering-sampling.md) som minskar antalet insamlade och lagrade händelser. Om du sänker antalet lagrade händelser sänks också noggrannheten hos måtten som, i bakgrunden, måste utföra agg regeringar för de händelser som lagras i loggarna.

> [!NOTE]
> I Application Insights kallas måtten som baseras på tids agg regeringen av händelser och mått som lagras i loggar loggbaserade mått. Dessa mått har vanligt vis många dimensioner från händelse egenskaperna, vilket gör dem överlägsna för analyser, men precisionen för dessa mått påverkas negativt av sampling och filtrering.

## <a name="pre-aggregated-metrics"></a>Föraggregerade mått

Utöver loggbaserade mått, i slutet av 2018, levererade Application Insights-teamet en offentlig för hands version av mått som lagras i en specialiserad databas som är optimerad för Time Series. De nya måtten behålls inte längre som enskilda händelser med massor av egenskaper. De lagras i stället som församlad tids serie och bara med viktiga dimensioner. Detta gör de nya måtten överlägsna vid tidpunkten för frågan: hämtning av data sker mycket snabbare och kräver mindre beräknings kraft. Detta möjliggör också nya scenarier, till exempel [nära aviseringar i real tid i mått på mått](../platform/alerts-metric-near-real-time.md), mer svars [instrument paneler](./overview-dashboard.md)med mera.

> [!IMPORTANT]
> Både log-baserade och föraggregerade mått finns i Application Insights. För att skilja de två, i Application Insights UX, kallas de föraggregerade måtten nu "standard mått (förhands granskning)", medan traditionella mått från händelserna har bytt namn till "Loggbaserade mått".

De nyare SDK: erna ([Application Insights 2,7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK eller senare för .net) församlade mått under samlingen. Detta gäller  [standard mått som skickas som standard](../platform/metrics-supported.md#microsoftinsightscomponents) så att noggrannheten inte påverkas av sampling eller filtrering. Den gäller också för anpassade mått som skickas med [GetMetric](./api-custom-events-metrics.md#getmetric) , vilket resulterar i mindre data inmatning och lägre kostnader.

För SDK: er som inte implementerar för insamlingen (dvs. äldre versioner av Application Insights-SDK: er eller för webb läsar Instrumentation), fyller Application Insights-servern fortfarande de nya måtten genom att aggregera de händelser som tas emot av slut punkten för den Application Insights händelse samlingen. Det innebär att när du inte drar nytta av den minskade mängden data som överförs via kabeln, kan du fortfarande använda församlade mått och få bättre prestanda och stöd för den nära dimensions aviseringen i real tid med SDK: er som inte föraggregerar mått under samlingen.

Det är värt att nämna att samlings slut punkten föraggregerar händelser innan inmatnings samplingen, vilket innebär att [provtagnings samplingen](./sampling.md) aldrig påverkar noggrannheten i föraggregerade mått, oavsett vilken SDK-version du använder med ditt program.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Använda föragg regering med Application Insights anpassade mått

Du kan använda för insamling med anpassade mått. De två huvudsakliga fördelarna är möjligheten att konfigurera och Avisera en dimension av ett anpassat mått och minska mängden data som skickas från SDK till slut punkten för Application Insights samlingen.

Det finns flera [sätt att skicka anpassade mått från Application Insights SDK](./api-custom-events-metrics.md). Om din version av SDK erbjuder GetMetric- [och TrackValue](./api-custom-events-metrics.md#getmetric) -metoderna är detta det bästa sättet att skicka anpassade mått, eftersom i det här fallet sker i för insamlingen i SDK: n inte bara minskar mängden data som lagras i Azure, utan även mängden data som överförs från SDK till Application Insights. Annars använder du metoden [trackMetric](./api-custom-events-metrics.md#trackmetric)  , som kommer föraggregerade Mät händelser under data inmatningen.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Anpassade mått dimensioner och för-aggregering

Alla mått som du skickar med [trackMetric](./api-custom-events-metrics.md#trackmetric) -eller [GetMetric-och TrackValue](./api-custom-events-metrics.md#getmetric) -API-anrop lagras automatiskt i både loggar och statistik lager. Men även om den loggbaserade versionen av ditt anpassade mått alltid behåller alla dimensioner, lagras den föraggregerade versionen av måttet som standard utan några dimensioner. Du kan aktivera insamling av mått för anpassade mått på fliken [användning och uppskattad kostnad](./pricing.md) genom att markera Aktivera aviseringar för anpassade mått dimensioner: 

![Användning och uppskattad kostnad](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Varför är insamling av anpassade mått dimensioner inaktiverat som standard?

Insamlingen av anpassade mått dimensioner är inaktive rad som standard eftersom i framtiden att lagra anpassade mått med dimensioner debiteras separat från Application Insights, medan de icke-dimensionella anpassade måtten fortfarande är kostnads fria (upp till en kvot). Du kan lära dig om de kommande pris sättnings modellerna på vår officiella [prissättnings sida](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Skapa diagram och utforska loggbaserade och föraggregerade standard mått

Använd [Azure Monitor Metrics Explorer](../platform/metrics-getting-started.md) för att rita diagram från föraggregerade och loggbaserade mått och för att skapa instrument paneler med diagram. När du har valt önskad Application Insights resurs använder du namn områdes väljaren för att växla mellan standard (för hands version) och loggbaserade mått, eller så väljer du ett anpassat mått namn område:

![Mått namn område](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Pris modeller för Application Insights mått

Genom att mata in mått i Application Insights, oavsett om det är loggat eller församlat, genereras kostnader baserat på storleken på inmatade data, enligt beskrivningen [här](./pricing.md#pricing-model). Dina anpassade mått, inklusive alla dimensioner, lagras alltid i Application Insights log Store. Dessutom vidarebefordras en fördefinierad version av dina anpassade mått (utan dimensioner) till mått arkivet som standard.

Om du väljer alternativet [aktivera aviseringar för anpassade mått dimensioner](#custom-metrics-dimensions-and-pre-aggregation) för att lagra alla dimensioner av de föraggregerade måtten i mått lagret kan du generera **ytterligare** kostnader baserat på [anpassade mått](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="next-steps"></a>Nästa steg

* [Aviseringar i nära real tid](../platform/alerts-metric-near-real-time.md)
* [GetMetric och TrackValue](./api-custom-events-metrics.md#getmetric)
