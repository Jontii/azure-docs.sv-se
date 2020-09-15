---
title: Introduktion till Azure Cognitive Search
titleSuffix: Azure Cognitive Search
description: Azure Kognitiv sökning är en helt hanterad moln Sök tjänst från Microsoft. Lär dig mer om användnings fall, utvecklings arbets flödet, jämförelser med andra Microsoft Search-produkter och hur du kommer igång.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 09/15/2020
ms.openlocfilehash: f042c171c120b7b5dd9b011bca2c2243be664767
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530632"
---
# <a name="what-is-azure-cognitive-search"></a>Vad är Azure Cognitive Search?

Azure Kognitiv sökning ([tidigare kallat "Azure Search"](whats-new.md#new-service-name)) är en moln Sök tjänst som tillhandahåller utvecklares API: er och verktyg för att lägga till en omfattande Sök upplevelse över privat, heterogent innehåll i webb-, mobil-och företags program.

Kognitiv sökning tillhandahåller en indexerings-och frågemotor och beständig lagring av Sök index. Den passar in mellan de externa data lager som bidrar till innehåll och en klient app som skickar förfrågningar och hanterar svar. De två primära arbets belastningarna i en Sök tjänst inkluderar *indexering* och *frågor*.

![Azure Kognitiv sökning-arkitektur](media/search-what-is-azure-search/azure-search-diagram.svg "Azure Kognitiv sökning-arkitektur")

Indexering är när din kod eller ett verktyg definierar ett index schema och läser in data i din Sök tjänst. Du kan också lägga till kognitiva kunskaper under indexeringen för att tillämpa AI-processer. Om du gör det kan du skapa ny information och strukturer som är användbara för scenarier för sökning och kunskaps utvinning.

När ett index finns skickar klient programmet förfrågningar till en Sök tjänst och hanterar svar. Sök funktionen definieras i klienten med hjälp av API: er från Azure Kognitiv sökning, med frågekörningen i ett Sök index som du skapar, äger och lagrar i din tjänst.

Funktionerna exponeras via en enkel [REST API](/rest/api/searchservice/) eller [.NET-SDK](search-howto-dotnet-sdk.md) som maskerar den inbyggda komplexiteten i informationshämtning. Du kan också använda Azure Portal för tjänst administration och innehålls hantering med verktyg för prototypering och fråga dina index. Eftersom tjänsten körs i molnet hanteras infrastruktur och tillgänglighet av Microsoft.

## <a name="when-to-use-cognitive-search"></a>När du ska använda Kognitiv sökning

Azure Kognitiv sökning passar bra för följande program scenarier:

+ Konsolidering av heterogena innehålls typer i ett privat och användardefinierat sökindex. Du kan fylla i ett Sök index med strömmar av JSON-dokument från vilken källa som helst. Använd en *indexerare* för att automatisera indexering för källor som stöds i Azure. Kontroll över index schema och uppdaterings schema är en viktig orsak till att använda Kognitiv sökning.

+ Enkel implementering av sökrelaterade funktioner. API: er för sökning fören klar frågornas konstruktion, fasett navigering, filter (inklusive geo-spatial sökning), synonym mappning, komplettering och relevans-justering. Med hjälp av inbyggda funktioner kan du tillgodose förväntningar på slutanvändare för en Sök upplevelse som liknar de kommersiella sökmotorer för Webbs ökning.

+ RAW-innehåll är en stor mängd olika text-eller bildfiler eller programfiler som lagras i Azure Blob Storage eller Cosmos DB. Du kan använda [kognitiva kunskaper](cognitive-search-concept-intro.md) under indexeringen för att extrahera text, skapa en struktur eller skapa ny information, till exempel översatt text eller entiteter.

+ Innehållet behöver språklig eller anpassad text analys. Om du har ett annat innehåll än engelska, stöder Azure Kognitiv sökning både Lucene-analyser och Microsofts naturliga språk processorer. Du kan också konfigurera analys verktyg för att uppnå specialiserad bearbetning av rå data, till exempel att filtrera ut dia kritiska tecken eller identifiera och bevara mönster i strängar.

Mer information om de olika funktionerna finns i [funktioner i Azure kognitiv sökning](search-features-list.md)

## <a name="how-to-use-cognitive-search"></a>Använda Kognitiv sökning

### <a name="step-1-provision-service"></a>Steg 1: Etablera tjänsten

Du kan [skapa en kostnads fri tjänst](search-create-service-portal.md) som delas med andra prenumeranter eller en [betald nivå](https://azure.microsoft.com/pricing/details/search/) som dedikerar resurser som endast används av din tjänst. Alla snabbstarter och självstudier kan utföras med den kostnadsfria tjänsten.

För betalda nivåer kan du skala en tjänst i två dimensioner för att kalibrera omursprung baserat på produktions krav:

+ Lägg till repliker för att utöka kapaciteten för att hantera tungt lästa frågor
+ Lägg till partitioner för att utöka lagringen för fler dokument

### <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index

Definiera ett index schema som ska mappas för att återspegla strukturen för de dokument som du vill söka efter, på samma sätt som fält i en databas.. Ett sökindex är en specialiserad data struktur som är optimerad för att köra snabba frågor.

Det är vanligt att [skapa index schemat i Azure Portal](search-what-is-an-index.md)eller program mässigt med [.net SDK](search-howto-dotnet-sdk.md) eller [REST API](/rest/api/searchservice/).

### <a name="step-3-load-data"></a>Steg 3: Läsa in data

När du har definierat ett index kan du ladda upp innehåll. Du kan använda en push- eller pull-modell.

Push-modellen push-överför JSON-dokument till ett index med hjälp av API: er från ett [SDK](search-howto-dotnet-sdk.md) eller [rest](/rest/api/searchservice/addupdate-or-delete-documents). Den externa data uppsättningen kan vara i stort sett alla data källor så länge dokumenten är JSON.

Pull-modellen hämtar data från källor på Azure och skickar dem till ett sökindex. Pull-modellen implementeras genom [*indexerare*](/rest/api/searchservice/Indexer-operations) som effektiviserar och automatiserar aspekter av data inmatning, till exempel att ansluta till, läsa och serialisera data. Data källor som stöds är Azure Cosmos DB, Azure SQL och Azure Storage.

### <a name="step-4-send-queries-and-handle-responses"></a>Steg 4: skicka frågor och hantera svar

När du har fyllt i ett index kan du [utfärda Sök frågor](search-query-overview.md) till tjänstens slut punkt med hjälp av enkla HTTP-begäranden med [REST API](/rest/api/searchservice/Search-Documents) eller [.NET SDK](/dotnet/api/microsoft.azure.search.idocumentsoperations).

Stega genom att [skapa din första Sökapp](tutorial-csharp-create-first-app.md) för att skapa och utöka en webb sida som samlar in användarindata och hanterar resultat. Du kan också använda [Postman för interaktiva rest](search-get-started-postman.md) -anrop eller den inbyggda [sök Utforskaren](search-explorer.md) i Azure Portal om du vill fråga ett befintligt index.

## <a name="how-it-compares"></a>Jämförelse

Kunder frågar ofta hur Azure Kognitiv sökning jämför med andra sökrelaterade lösningar. I följande tabell sammanfattas viktiga skillnader.

| Jämfört med | Viktiga skillnader |
|-------------|-----------------|
|Bing | [API för webbsökning i Bing](../cognitive-services/bing-web-search/index.yml) söker i indexen på Bing.com efter matchande termer som du skickar. Index skapas utifrån HTML-, XML- och annat webbinnehåll på offentliga webbplatser. [Anpassad sökning i Bing](/azure/cognitive-services/bing-custom-search/) som bygger på samma grund, erbjuder samma crawlerteknik för webbinnehållstyper, begränsat till enskilda webbplatser.<br/><br/>Azure Kognitiv sökning söker i ett index som du definierar, ifyllt med data och dokument som du äger, ofta från olika källor. Azure Kognitiv sökning har Crawler-funktioner för vissa data källor via [indexerare](search-indexer-overview.md), men du kan skicka alla JSON-dokument som följer index schemat till en enda, konsol IDE rad sökbar resurs. |
|Databassökning | Flera olika databasplattformar innehåller en inbyggd sökupplevelse. SQL Server har [fulltextsökning](/sql/relational-databases/search/full-text-search). Cosmos DB och liknande tekniker har frågbara index. Vid utvärdering av produkter som kombinerar sökning och lagring, kan det vara svårt att avgöra vad som är bäst. Många lösningar använder både: DBMS för lagring och Azure Kognitiv sökning för specialiserade Sök funktioner.<br/><br/>Jämfört med DBMS-sökning lagrar Azure Kognitiv sökning innehåll från heterogena källor och innehåller särskilda funktioner för text bearbetning, till exempel språk medveten text bearbetning (ord Forms igenkänning, lemmatisering och ord Forms) på [56 språk](/rest/api/searchservice/language-support). Det stöder också autokorrigering av felstavade ord, [synonymer](/rest/api/searchservice/synonym-map-operations), [förslag](/rest/api/searchservice/suggestions), [bedömningskontroller](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facetter](./search-filters-facets.md) och [anpassad tokenisering](/rest/api/searchservice/custom-analyzers-in-azure-search). [Motorn för full texts ökning](search-lucene-query-architecture.md) i Azure kognitiv sökning bygger på Apache Lucene, en bransch standard i informations hämtning. Medan Azure Kognitiv sökning behåller data i form av ett inverterat index, är det dock inte en ersättning för sann data lagring och vi rekommenderar inte att du använder den i den kapaciteten. För ytterligare information, se det här [foruminlägget](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Resursutnyttjande är en annan brytpunkt i den här kategorin. Indexering och vissa frågeåtgärder är ofta beräkningsmässigt intensiva. Om sökningen avlastas från DBMS till en dedikerad lösning i molnet, bevaras systemets resurser för transaktionsbearbetning. Genom att dessutom exterrnalisera sökningen kan du enkelt justera skalan utifrån frågevolymen.|
|Dedikerad söklösning | Under förutsättning att du har valt dedikerad sökning med fullspektrumfunktioner, är en slutlig kategorisk jämförelse mellan lokala lösningar eller en molntjänst. Många söktekniker erbjuder kontroll över indexerings- och frågepipelines, tillgång till mer avancerad fråge- och filtreringssyntax, kontroll över rangordning och relevans, samt funktioner för självdirigerad och intelligent sökning. <br/><br/>En molntjänst är det rätta valet om du vill ha en nyckelfärdig lösning med minimalt merarbete och underhåll, och justerbar skala. <br/><br/>Inom molnparadigmet erbjuder flera leverantörer jämförbara baslinjefunktioner, med fulltextsökning, geo-sökning och möjlighet att hantera en viss nivå av tvetydighet i sökinmatningar. Det är vanligtvis en [specialiserad funktion](search-features-list.md) eller den övergripande enkelheten i API:er, verktyg och hantering som avgör det bästa valet. |

I moln leverantörer är Azure Kognitiv sökning starkast för full texts öknings arbets belastningar över innehålls lager och databaser på Azure, för appar som främst är beroende av att söka efter både informations hämtning och innehålls navigering. 

Viktiga fördelar är:

+ Azure-dataintegrering (crawler) på indexeringslagret
+ Azure Portal för central hantering
+ Azure-skala, tillförlitlighet och tillgänglighet i världsklass
+ AI-bearbetning av rå data för att göra den mer sökbar, inklusive text från bilder eller hitta mönster i ostrukturerat innehåll.
+ Språklig och anpassad analys, med analysverktyg för fulltextsökning på 56 språk
+ [Centrala funktioner som är gemensamma för sökcentriska appar](search-features-list.md): bedömning, aspektbasering, förslag, synonymer, geo-sökning och mycket mer.

Bland våra kunder kan de som har nytta av de många funktionerna i Azure Kognitiv sökning inkludera kataloger, branschspecifika program och dokument identifierings program.

## <a name="watch-this-video"></a>Titta på den här videon

I den här 15 minuter långa videon introducerar program hanteraren Luis Cabrera Azure Kognitiv sökning.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]