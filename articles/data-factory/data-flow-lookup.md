---
title: Söknings omvandling i data flöde för mappning
description: Referens data från en annan källa med hjälp av uppslags omvandlingen i mappnings data flödet.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/28/2020
ms.openlocfilehash: a4fcdad0efda1ab2a43be65865e3aac59f7ef3e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84187601"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Söknings omvandling i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd lookup-omvandlingen för att referera till data från en annan källa i en data flödes ström. Uppslags omvandlingen lägger till kolumner från matchade data till dina källdata.

En uppslags omvandling liknar en vänster yttre koppling. Alla rader från den primära data strömmen finns i utdataströmmen med ytterligare kolumner från Sök strömmen.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xsVT]

## <a name="configuration"></a>Konfiguration

![Sök omvandling](media/data-flow/lookup1.png "Sökning")

**Primär ström:** Inkommande data ström. Den här strömmen motsvarar vänster sida av en koppling.

**Sök ström:** De data som läggs till i den primära data strömmen. Vilka data som läggs till bestäms av Sök villkoren. Den här strömmen motsvarar den högra sidan i en koppling.

**Matcha flera rader:** Om den är aktive rad returnerar en rad med flera matchningar i den primära data strömmen flera rader. Annars returneras bara en rad som baseras på villkoret "matcha on".

**Matcha på:** Endast synlig om ' matcha flera rader ' inte är markerat. Välj om du vill matcha på alla rader, den första matchningen eller den sista matchningen. Alla rader rekommenderas eftersom de körs snabbast. Om den första raden eller sista raden är markerad, måste du ange sorterings villkor.

**Sök villkor:** Välj vilka kolumner som ska matchas. Om likhets villkoret är uppfyllt, kommer raderna att anses vara en matchning. Hovra och välj beräknad kolumn för att extrahera ett värde med hjälp av [data flödets uttrycks språk](data-flow-expression-functions.md).

Lookup-omvandlingen stöder bara likhets matchningar. Om du vill anpassa uppslags uttrycket så att det innehåller andra operatorer, till exempel större än, rekommenderar vi att du använder en [kors koppling i Join-omvandlingen](data-flow-join.md#custom-cross-join). En kors koppling gör att det inte går att kartesiska produkt fel vid körning.

Alla kolumner från båda strömmarna ingår i utdata. Om du vill släppa dubbletter eller oönskade kolumner lägger du till en [Select-omvandling](data-flow-select.md) efter sökningens omvandling. Kolumner kan också tas bort eller byta namn i en Sink-omvandling.

### <a name="non-equi-joins"></a>Icke-Equi kopplingar

Om du vill använda en villkorlig operator som inte är lika med (! =) eller större än (>) i Sök villkoren ändrar du List rutan operator mellan de två kolumnerna. Icke-Equi anslutningar kräver att minst en av de två strömmarna skickas via **fast** sändning på fliken **optimera** .

![Icke-Equi sökning](media/data-flow/non-equi-lookup.png "Icke-Equi sökning")

## <a name="analyzing-matched-rows"></a>Analysera matchade rader

Efter uppslags omvandlingen kan funktionen `isMatch()` användas för att se om sökningen matchar enskilda rader.

![Sök mönster](media/data-flow/lookup111.png "Sök mönster")

Ett exempel på det här mönstret är att använda den villkorliga delnings omvandlingen för att dela upp `isMatch()` funktionen. I exemplet ovan går matchande rader genom den översta strömmen och icke-matchande rader flödar genom ```NoMatch``` data strömmen.

## <a name="testing-lookup-conditions"></a>Testa Sök villkor

När du testar lookup-omvandlingen med data förhands granskning i fel söknings läge, använder du en liten uppsättning kända data. När du samplar rader från en stor data uppsättning kan du inte förutsäga vilka rader och nycklar som ska läsas för testning. Resultatet är icke-deterministiskt, vilket innebär att dina kopplings villkor inte kan returnera några träffar.

## <a name="broadcast-optimization"></a>Sändnings optimering

![Sändnings anslutning](media/data-flow/broadcast.png "Sändnings anslutning")

I kopplingar, sökningar och exists-omvandling, om en eller båda data strömmar får plats i arbetsnodens minne, kan du optimera prestandan genom att aktivera **sändning**. Som standard bestämmer Spark-motorn om en sida ska sändas automatiskt eller inte. Välj **fast**om du vill välja vilken sida som ska sändas manuellt.

Vi rekommenderar inte att du inaktiverar sändning via alternativet **inaktivera** om inte dina kopplingar körs i tids gräns fel.

## <a name="data-flow-script"></a>Dataflödesskript

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Exempel

![Sök omvandling](media/data-flow/lookup-dsl-example.png "Sökning")

Data flödes skriptet för ovanstående uppslags konfiguration finns i kodfragmentet nedan.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'auto')~> LookupKeys
```
## 
Nästa steg

* Transformeringarna [Join](data-flow-join.md) och [exists](data-flow-exists.md) tar båda i flera Stream-indata
* Använd en [villkorlig Split-omvandling](data-flow-conditional-split.md) med ```isMatch()``` för att dela upp rader för matchande och icke-matchande värden
