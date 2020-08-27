---
title: Justera kapaciteten för frågor och index-arbetsbelastningar
titleSuffix: Azure Cognitive Search
description: Justera partitions-och replik dator resurser i Azure Kognitiv sökning, där varje resurs priss ätts i de fakturerbara Sök enheterna.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 476af7dd40cd1f31d03f3bd80affac0ce10ef900
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927212"
---
# <a name="adjust-capacity-in-azure-cognitive-search"></a>Justera kapaciteten i Azure Kognitiv sökning

Innan du [konfigurerar en Sök tjänst](search-create-service-portal.md) och låser på en viss pris nivå kan du ta några minuter för att förstå rollen som repliker och partitioner i en tjänst och hur du kan anpassa en tjänst för att hantera toppar och DIP i resurs behovet.

Kapaciteten är en funktion för den [nivå du väljer](search-sku-tier.md) (nivåer bestämmer maskin varu egenskaper) och den kombination av replik och partition som krävs för projekt arbets belastningar. Beroende på nivån och storleken på justeringen kan tillägg eller minskning av kapaciteten ta var som helst från 15 minuter till flera timmar. 

När du ändrar tilldelningen av repliker och partitioner rekommenderar vi att du använder Azure Portal. Portalen tillämpar gränser på tillåtna kombinationer som ligger under de maximala gränserna för en nivå. Men om du behöver en skript-eller kod baserad etablerings metod är [Azure PowerShell](search-manage-powershell.md) eller [hanterings REST API](/rest/api/searchmanagement/services) alternativa lösningar.

## <a name="terminology-replicas-and-partitions"></a>Terminologi: repliker och partitioner

|||
|-|-|
|*Partitioner* | Tillhandahåller index lagring och I/O för Läs-och skriv åtgärder (till exempel när du återskapar eller uppdaterar ett index). Varje partition har en resurs av det totala indexet. Om du tilldelar tre partitioner är ditt index indelat i tredje. |
|*Repliker* | Instanserna av Sök tjänsten används främst för att belastningsutjämna frågor. Varje replik är en kopia av ett index. Om du tilldelar tre repliker har du tre kopior av ett index som är tillgängligt för att betjäna fråge förfrågningar.|

## <a name="when-to-add-nodes"></a>När du ska lägga till noder

En tjänst har inlednings vis tilldelats en minimal resurs nivå som består av en partition och en replik. 

En enskild tjänst måste ha tillräckligt med resurser för att hantera alla arbets belastningar (indexering och frågor). Ingen arbets belastning körs i bakgrunden. Du kan schemalägga indexeringen för tider när fråge förfrågningar är mycket mindre frekventa, men tjänsten kan inte på annat sätt prioritera en aktivitet över en annan. En viss mängd redundans utjämnar dessutom frågans prestanda när tjänster eller noder uppdateras internt.

Som en allmän regel kräver Sök program att du behöver fler repliker än partitioner, särskilt när tjänst åtgärderna prioriteras mot frågor till arbets belastningar. Avsnittet om [hög tillgänglighet](#HA) förklarar varför.

> [!NOTE]
> Om du lägger till fler repliker eller partitioner ökar kostnaden för att köra tjänsten och det kan leda till små variationer i hur resultaten sorteras. Se till att kontrol lera [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att förstå fakturerings konsekvenserna för att lägga till fler noder. [Diagrammet nedan](#chart) kan hjälpa dig att korsa referenser till antalet Sök enheter som krävs för en bestämd konfiguration. Mer information om hur ytterligare repliker påverkar bearbetning av frågor finns i [sortera resultat](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Så här allokerar du repliker och partitioner

1. Logga in på [Azure Portal](https://portal.azure.com/) och välj Sök tjänsten.

1. I **Inställningar**öppnar du sidan **skala** för att ändra repliker och partitioner. 

   Följande skärm bild visar en standard tjänst som tillhandahålls med en replik och partition. Formeln längst ned anger hur många Sök enheter som används (1). Om enhets priset var $100 (inte ett verkligt pris) blir månads kostnaden för att köra tjänsten $100 i genomsnitt.

   ![Skala sidan visar aktuella värden](media/search-capacity-planning/1-initial-values.png "Skala sidan visar aktuella värden")

1. Använd skjutreglaget för att öka eller minska antalet partitioner. Formeln längst ned anger hur många Sök enheter som används.

   I det här exemplet dubbleras kapaciteten, med två repliker och partitioner. Observera antalet Sök enheter; nu är det fyra eftersom fakturerings formeln är repliker multiplicerade med partitioner (2 x 2). Dubbla kapaciteter är mer än dubbelt så mycket kostnaden för att köra tjänsten. Om priset för Sök enheten var $100 skulle den nya månads fakturan nu vara $400.

   För aktuella kostnader per enhet för varje nivå går du till [sidan med priser](https://azure.microsoft.com/pricing/details/search/).

   ![Lägg till repliker och partitioner](media/search-capacity-planning/2-add-2-each.png "Lägg till repliker och partitioner")

1. Klicka på **Spara** för att bekräfta ändringarna.

   ![Bekräfta ändringar av skalning och fakturering](media/search-capacity-planning/3-save-confirm.png "Bekräfta ändringar av skalning och fakturering")

   Ändringar i kapaciteten tar flera timmar att slutföra. Det går inte att avbryta när processen har startats och det inte finns någon real tids övervakning för replik-och partitions justeringar. Följande meddelande är dock fortfarande synligt när ändringarna pågår.

   ![Status meddelande i portalen](media/search-capacity-planning/4-updating.png "Status meddelande i portalen")

> [!NOTE]
> När en tjänst har allokerats kan den inte uppgraderas till en högre nivå. Du måste skapa en Sök tjänst på den nya nivån och läsa in index på nytt. Se [skapa en Azure kognitiv sökning-tjänst i portalen](search-create-service-portal.md) för hjälp med tjänst etablering.
>
> Dessutom hanteras partitioner och repliker exklusivt och internt av tjänsten. Det finns inget begrepp för processor tillhörighet eller tilldelning av en arbets belastning till en speciell nod.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Kombinationer av partition och repliker

En grundläggande tjänst kan ha exakt en partition och upp till tre repliker, för en gräns på tre SUs. Den enda justerbara resursen är repliker. Du behöver minst två repliker för att få hög tillgänglighet för frågor.

Alla standard-och Storage-optimerade Sök tjänster kan utgå från följande kombinationer av repliker och partitioner, beroende på 36-SU-gränsen. 

|   | **1 partition** | **2 partitioner** | **3 partitioner** | **4 partitioner** | **6 partitioner** | **12 partitioner** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replik** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 repliker** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 repliker** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 repliker** |4 SU |8 SU |12 SU |16 SU |24 SU |Ej tillämpligt |
| **5 repliker** |5 SU |10 SU |15 SU |20 SU |30 SU |Ej tillämpligt |
| **6 repliker** |6 SU |12 SU |18 SU |24 SU |36 SU |Ej tillämpligt |
| **12 repliker** |12 SU |24 SU |36 SU |Saknas |Saknas |Saknas |

SUs, priser och kapacitet beskrivs i detalj på Azure-webbplatsen. Mer information finns i [pris information](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Antalet repliker och partitioner delas upp jämnt i 12 (särskilt, 1, 2, 3, 4, 6, 12). Detta beror på att Azure Kognitiv sökning fördelar varje index i 12 Shards så att det kan spridas i lika stora delar av alla partitioner. Om din tjänst till exempel har tre partitioner och du skapar ett index, kommer varje partition att innehålla fyra Shards av indexet. Hur Azure Kognitiv sökning Shards ett index är en implementerings information som kan komma att ändras i framtida versioner. Även om talet är 12 i dag, bör du inte vänta att antalet alltid är 12 i framtiden.
>

<a id="HA"></a>

## <a name="high-availability"></a>Hög tillgänglighet

Eftersom det är enkelt och relativt enkelt att skala upp rekommenderar vi vanligt vis att du börjar med en partition och en eller två repliker, och sedan skalar upp som fråge volymer skapas. Fråga arbets belastningar körs främst på repliker. Om du behöver mer data flöde eller hög tillgänglighet behöver du förmodligen ytterligare repliker.

Allmänna rekommendationer för hög tillgänglighet:

* Två repliker för hög tillgänglighet för skrivskyddade arbets belastningar (frågor)

* Tre eller flera repliker för hög tillgänglighet av Läs-och skriv arbets belastningar (frågor plus indexering när enskilda dokument läggs till, uppdateras eller tas bort)

Service avtal (SLA) för Azure Kognitiv sökning riktas mot åtgärder och vid index uppdateringar som består av att lägga till, uppdatera eller ta bort dokument.

Basic-nivån ligger utanför en partition och tre repliker. Om du vill att flexibiliteten omedelbart ska svara på fluktuationer i behov för både indexering och frågans data flöde, bör du överväga en av standard nivåerna.  Om du upptäcker att dina lagrings krav ökar mycket snabbare än din fråga genom att använda en av de optimerade lagrings nivåerna.

## <a name="disaster-recovery"></a>Haveriberedskap

För närvarande finns det ingen inbyggd mekanism för haveri beredskap. Att lägga till partitioner eller repliker skulle vara fel strategi för att uppfylla återställnings mål för haveri beredskap. Den vanligaste metoden är att lägga till redundans på service nivå genom att konfigurera en andra Sök tjänst i en annan region. Som med tillgänglighet under ett index återuppbyggnad måste omdirigeringen eller växlings logiken komma från din kod.

## <a name="estimate-replicas"></a>Uppskatta repliker

I en produktions tjänst bör du allokera tre repliker i SLA-syfte. Om du upplever långsam prestanda för frågor kan du lägga till repliker så att ytterligare kopior av indexet blir online för att stödja större arbets belastningar och belastningsutjämna begär anden över flera repliker.

Vi tillhandahåller inte rikt linjer för hur många repliker som behövs för att läsa in frågor. Fråga om prestanda beror på komplexiteten i frågan och konkurrerande arbets belastningar. Även om du lägger till repliker tydligt resulterar i bättre prestanda är resultatet inte strikt linjärt: om du lägger till tre repliker garanterar vi inte tredubbel data flöde.

Vägledning för att uppskatta frågor per sekund för din lösning finns i [skala för prestanda](search-performance-optimization.md)-och [övervaknings frågor](search-monitor-queries.md)

## <a name="estimate-partitions"></a>Uppskatta partitioner

[Nivån du väljer](search-sku-tier.md) bestämmer partitionens storlek och hastighet, och varje nivå optimeras runt en uppsättning egenskaper som passar olika scenarier. Om du väljer en högre nivå kan du behöva färre partitioner än om du går med S1. En av de frågor som du behöver svara via självdirigerad testning är om en större och dyrare partition ger bättre prestanda än två billigare partitioner på en tjänst som tillhandahålls på en lägre nivå.

Sök efter program som kräver data uppdatering i nära real tid måste ha proportionellt sett fler partitioner än repliker. Att lägga till partitioner sprider Läs-och skriv åtgärder över ett större antal beräknings resurser. Du får också mer disk utrymme för att lagra ytterligare index och dokument.

Större index tar längre tid att fråga. Därför kanske du upptäcker att varje stegvis ökning i partitioner kräver en mindre men proportionell ökning av repliker. Komplexiteten för dina frågor och frågesträngen kommer att kopplas till hur snabbt frågekörningen ska aktive ras runt.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Välj en pris nivå för Azure Kognitiv sökning](search-sku-tier.md)