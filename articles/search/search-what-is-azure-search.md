---
title: Introduktion till Azure Cognitive Search
titleSuffix: Azure Cognitive Search
description: Azure Kognitiv sökning är en helt hanterad moln Sök tjänst från Microsoft. Lär dig mer om användnings fall, utvecklings arbets flödet, jämförelser med andra Microsoft Search-produkter och hur du kommer igång.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 11/24/2020
ms.custom: contperfq1
ms.openlocfilehash: c36a88317f71d08094b43d06cc7ba86985a73ca0
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511856"
---
# <a name="what-is-azure-cognitive-search"></a>Vad är Azure Cognitive Search?

Azure Kognitiv sökning ([tidigare kallat "Azure Search"](whats-new.md#new-service-name)) är en moln Sök tjänst som tillhandahåller utvecklares API: er och verktyg för att skapa en omfattande Sök upplevelse över privat, heterogent innehåll i webb-, mobil-och företags program. 

När du skapar en Kognitiv sökning-tjänst får du:

+ En sökmotor som utför indexering och frågekörningen
+ Beständig lagring av Sök index som du skapar och hanterar
+ Ett frågespråk för att skriva enkla till komplexa frågor
+ AI – centrerad analys, som skapar sökbart innehåll av bilder, rå text, programfiler
+ Integrering med Azure-data via Sök indexerare, automatisera data import och uppdatering

En Sök tjänst är arkitekturad mellan de externa data lager som innehåller dina data som inte har indexerats och en klient-app som skickar förfrågningar till ett sökindex och hanterar svaret.

![Azure Kognitiv sökning-arkitektur](media/search-what-is-azure-search/azure-search-diagram.svg "Azure Kognitiv sökning-arkitektur")

En Sök tjänst integreras med andra Azure-tjänster i form av *indexerare* som automatiserar data inmatning/hämtning från Azure-datakällor, och *färdighetsuppsättningar* som innehåller förbruknings bara AI från Cognitive Services, till exempel bild-och text analys eller anpassad AI som du skapar i Azure Machine Learning eller omslutning i Azure Functions.

På själva Sök tjänsten är de två primära arbets belastningarna *Indexera* och *fråga*. 

+ Indexeringen ger text till Sök tjänsten och gör den sökbar. Internt bearbetas inkommande text till tokens och lagras i inverterade index för snabba sökningar. 

  Inom indexering har du möjlighet att lägga till AI- *anrikning* genom [kognitiva kunskaper](cognitive-search-working-with-skillsets.md), antingen fördefinierade från Microsoft eller anpassade kunskaper som du skapar. Den efterföljande analysen och transformeringar kan leda till ny information och strukturer som inte tidigare fanns, vilket ger ett högt verktyg för många Sök-och kunskaps utvinnings scenarier.

+ När ett index har fyllts med sökbara data skickar klient programmet förfrågningar till en Sök tjänst och hanterar svar. All frågekörningen är över ett Sök index som du skapar, äger och lagrar i din tjänst. I din klient app definieras Sök funktionen med hjälp av API: er från Azure Kognitiv sökning, och det kan ta med relevans-justering, komplettering, synonym matchning, fuzzy-matchning, mönster matchning, filter och sortering.

Funktionerna exponeras via en enkel [REST API](/rest/api/searchservice/) eller [.NET-SDK](search-howto-dotnet-sdk.md) som maskerar den inbyggda komplexiteten i informationshämtning. Du kan också använda Azure Portal för tjänst administration och innehålls hantering med verktyg för prototypering och frågor mot index och färdighetsuppsättningar. Eftersom tjänsten körs i molnet hanteras infrastruktur och tillgänglighet av Microsoft.

## <a name="why-use-cognitive-search"></a>Varför använda Kognitiv sökning

Azure Kognitiv sökning lämpar sig väl för följande program scenarier:

+ Konsolidera heterogent innehåll till ett privat, användardefinierat sökindex. Du kan fylla i ett Sök index med strömmar av JSON-dokument från vilken källa som helst. Använd en *indexerare* för att automatisera indexering för källor som stöds i Azure. Kontroll över index schema och uppdaterings schema är en viktig orsak till att använda Kognitiv sökning.

+ Enkel implementering av sökrelaterade funktioner. API: er för sökning fören klar frågornas konstruktion, fasett navigering, filter (inklusive geo-spatial sökning), synonym mappning, komplettering och relevans-justering. Med hjälp av inbyggda funktioner kan du tillgodose förväntningar på slutanvändare för en Sök upplevelse som liknar de kommersiella sökmotorer för Webbs ökning.

+ RAW-innehåll är en stor mängd olika text-eller bildfiler eller programfiler som lagras i Azure Blob Storage eller Cosmos DB. Du kan använda [kognitiva kunskaper](cognitive-search-concept-intro.md) under indexeringen för att identifiera och extrahera text, skapa struktur eller skapa ny information, till exempel översatt text eller entiteter.

+ Innehållet behöver språklig eller anpassad text analys. Om du har ett annat innehåll än engelska, stöder Azure Kognitiv sökning både Lucene-analyser och Microsofts naturliga språk processorer. Du kan också konfigurera analys verktyg för att uppnå specialiserad bearbetning av rå data, till exempel att filtrera ut dia kritiska tecken eller identifiera och bevara mönster i strängar.

Mer information om de olika funktionerna finns i [funktioner i Azure kognitiv sökning](search-features-list.md)

## <a name="how-to-get-started"></a>Så här kommer du igång

En heltäckande utforskning av Core search-funktioner kan uppnås i fyra steg:

1. [**Skapa en Sök tjänst**](search-create-service-portal.md) på den kostnads fria nivån som delas med andra prenumeranter eller en [betald nivå](https://azure.microsoft.com/pricing/details/search/) för dedikerade resurser som endast används av din tjänst. Alla snabbstarter och självstudier kan utföras med den kostnadsfria tjänsten.

1. [**Skapa ett Sök index**](search-what-is-an-index.md) med hjälp av portalen [REST API](/rest/api/searchservice/create-index). [.NET SDK](search-howto-dotnet-sdk.md)eller en annan SDK. Index-schemat definierar strukturen för sökbart innehåll.

1. [**Överför innehåll**](search-what-is-data-import.md) till indexet. Använd ["push"-modellen](tutorial-optimize-indexing-push-api.md) för att skicka JSON-dokument från vilken källa som helst, eller Använd ["pull"-modellen (indexerarna)](search-indexer-overview.md) om dina källdata är i Azure.

1. [**Fråga ett index**](search-query-overview.md) med hjälp av [Sök Utforskaren](search-explorer.md) i portalen, [REST API](search-get-started-rest.md), [.NET SDK](/dotnet/api/azure.search.documents.searchclient.search)eller något annat SDK.

> [!TIP]
> Minimera stegen genom att starta med [**guiden Importera data**](search-get-started-portal.md) och en Azure-datakälla för att skapa, läsa in och fråga ett index på några minuter.

## <a name="how-it-compares"></a>Jämförelse

Kunder frågar ofta hur Azure Kognitiv sökning jämför med andra sökrelaterade lösningar. I följande tabell sammanfattas viktiga skillnader.

| Jämfört med | Viktiga skillnader |
|-------------|-----------------|
| Microsoft Search | [Microsoft Search](/microsoftsearch/overview-microsoft-search) är för Microsoft 365 autentiserade användare som behöver fråga över innehåll i SharePoint. Den erbjuds som en färdig sökning, som är aktive rad och konfigurerad av administratörer, med möjlighet att ta emot externt innehåll via anslutningar från Microsoft och andra källor. Om detta beskriver ditt scenario är Microsoft Search med Microsoft 365 ett attraktivt alternativ att utforska.<br/><br/>Azure Kognitiv sökning kör däremot frågor över ett index som du definierar, ifyllt med data och dokument som du äger, ofta från olika källor. Azure Kognitiv sökning har Crawler-funktioner för vissa Azure-datakällor via [indexerare](search-indexer-overview.md), men du kan skicka alla JSON-dokument som följer ditt index schema till en enda, konsol IDE rad sökbar resurs. Du kan också anpassa indexerings pipelinen så att den inkluderar Machine Learning och lexikaliska analyser. Eftersom Kognitiv sökning är byggd för att vara en plugin-komponent i större lösningar kan du integrera sökningen i nästan vilken app som helst, på vilken plattform som helst.|
|Bing | [API för webbsökning i Bing](../cognitive-services/bing-web-search/index.yml) söker i indexen på Bing.com efter matchande termer som du skickar. Index skapas utifrån HTML-, XML- och annat webbinnehåll på offentliga webbplatser. [Anpassad sökning i Bing](/azure/cognitive-services/bing-custom-search/) som bygger på samma grund, erbjuder samma crawlerteknik för webbinnehållstyper, begränsat till enskilda webbplatser.<br/><br/>I Kognitiv sökning kan du definiera och fylla i indexet. Du kan använda [indexerare](search-indexer-overview.md) för att crawla data på Azure-datakällor eller skicka alla indexbaserade JSON-dokument till din Sök tjänst. |
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