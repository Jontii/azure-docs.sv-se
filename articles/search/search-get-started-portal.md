---
title: Skapa ett Sök index i Azure Portal
titleSuffix: Azure Cognitive Search
description: I den här Azure Portal snabb starten använder du guiden Importera data för att skapa, läsa in och fråga ditt första Sök index i Azure Kognitiv sökning.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 09/16/2020
ms.openlocfilehash: 0a28bd0d4101fc9d49413adaaccc0a6d38ec64ed
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91307729"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Snabb start: skapa ett Azure Kognitiv sökning-index i Azure Portal

Guiden **Importera data** är ett Azure Portal verktyg som vägleder dig genom skapandet av ett Sök index så att du kan skriva intressanta frågor inom några minuter. 

Guiden innehåller också sidor för AI-berikning så att du kan extrahera text och struktur från bildfiler och ostrukturerad text. Innehålls bearbetning med AI inkluderar optisk tecken läsning (OCR), nyckel fras och enhets extrahering och bild analys.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).

+ En Azure Kognitiv sökning-tjänst. [Skapa en tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här snabb starten. 

### <a name="check-for-space"></a>Kontrollera utrymmet

Många kunder börjar med den kostnadsfria tjänsten. Den här versionen är begränsad till tre index, tre datakällor och tre indexerare. Kontrollera att du har plats för extra objekt innan du börjar. Med den här guiden kan du skapa ett objekt av varje sort.

Avsnitten i tjänstinstrumentpanelen visar hur många index, indexerare och datakällor som du redan har. 

![En lista över index, indexerare och datakällor](media/search-get-started-portal/tiles-indexers-datasources.png)

## <a name="create-an-index-and-load-data"></a><a name="create-index"></a> Skapa ett index och läsa in data

Sökfrågor itererar över ett [*index*](search-what-is-an-index.md) som innehåller sökbara data, metadata och ytterligare konstruktioner som optimerar vissa sökbeteenden.

I den här självstudien använder vi en inbyggd exempel data uppsättning som kan crawlas med hjälp av en [*indexerare*](search-indexer-overview.md) via [guiden **Importera data** ](search-import-data-portal.md). En indexerare är en källspecifik crawler som kan läsa metadata och innehåll från Azure-datakällor som stöds. Normalt sett används indexerare programmässigt, men i portalen så kan du komma åt dem via guiden **Importera data**. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Steg 1 – starta guiden Importera data och skapa en datakälla

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ditt Azure-konto.

1. [Hitta Sök tjänsten](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) och klicka på **Importera data** i kommando fältet för att skapa och fylla i ett sökindex på sidan Översikt.

   ![Kommandot Importera data](media/search-get-started-portal/import-data-cmd.png)

1. I guiden klickar du på **Anslut till dina data**  >  **exempel**  >  **Hotels-Sample**. Den här datakällan är inbyggd. Om du skapar din egen datakälla så behöver du ange ett namn, typ och anslutningsinformation. När du har skapat den blir den en ”befintlig datakälla” som kan återanvändas i andra importåtgärder.

   ![Välj exempeldatauppsättning](media/search-get-started-portal/import-datasource-sample.png)

1. Fortsätt till nästa sida.

### <a name="step-2---skip-the-enrich-content-page"></a>Steg 2 – hoppa över sidan "utöka innehåll"

Guiden stöder skapandet av en pipeline för [AI-anrikning](cognitive-search-concept-intro.md) för att införliva Cognitive Services AI-algoritmer i indexering. 

Vi hoppar över det här steget för tillfället och går direkt till **Anpassa målindex**.

   ![Hoppa över steget Kognitiva kunskaper](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> Du kan gå igenom ett AI-index exempel i en [snabb start](cognitive-search-quickstart-blob.md) eller [självstudier](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Steg 3 – Konfigurera index

Skapandet av index är vanligtvis en kodbaserad övning som slutförs innan du läser in data. Som den här guiden visar kan dock guiden skapa ett grundläggande index för valfri datakälla som den kan crawla. Ett index kräver minst ett namn och en samling fält. Ett av fälten ska vara markerat som dokumentnyckeln som fungerar som en unik identifierare för dokumentet. Du kan också välja att använda språkanalys eller språkförslag för automatisk komplettering eller frågeförslag.

Fälten har datatyper och attribut. Kryssrutorna högst upp är *indexattribut* som styr hur fältet används.

* **Hämtningsbar** innebär att det visas i listor med sökresultat. Du kan markera enskilda fält som gränser för Sök resultat genom att avmarkera den här kryss rutan, till exempel för fält som endast används i filter uttryck.
* **Nyckel** är den unika dokumentdentifieraren. Den är alltid en sträng och den är obligatorisk.
* **Filtrerbar**, **Sorterbar** och **Fasetterbar** avgör om fält användas i ett filter, en sortering eller en fasetterad navigeringsstruktur.
* **Sökbar** innebär att ett fält ingår i fulltextsökning. Strängarna är sökbara. Numeriska fält och fält för booleska värden är ofta markerade som icke sökbara.

Lagringskraven varierar inte till följd av ditt val. Om du till exempel anger attributet **Hämtningsbar** på flera fält så ökar inte lagringskraven.

Som standard söker guiden igenom datakällan för att hitta unika identifierare som utgör själva grunden för sökordsfältet. *Strängar* attributas som **hämtnings** bara och **sökbara**. *Heltalen* attributas som **hämtnings**bara, **filter**bara, **sorterbara**och **aspekt**bara.

1. Acceptera alla standardinställningar. 

   Om du kör guiden igen en andra gång med en befintlig hotell data källa, konfigureras indexet inte med standardattribut. Du måste då manuellt välja attribut för framtida importer. 

   ![Ett hotell index har skapats](media/search-get-started-portal/hotelsindex.png)

2. Fortsätt till nästa sida.


### <a name="step-4---configure-indexer"></a>Steg 4 – Konfigurera indexeraren

Klicka på **Indexnamn** > **Namn** i guiden **Importera data** och skriv in ett namn på indexeraren.

Det här objektet definierar en körbar process. Du kan lägga till det i ett återkommande schema, men för tillfället använder du standardalternativet för att köra indexeraren en gång direkt.

Klicka på **Skicka** för att skapa och köra indexeraren samtidigt.

  ![Hotell indexerare](media/search-get-started-portal/hotels-indexer.png)

## <a name="monitor-progress"></a>Övervaka förloppet

Guiden ska ta dig till listan med indexerare där du kan övervaka förloppet. För självnavigering går du till översikten över sidan och klickar på **indexerare**.

Det kan ta några minuter för portalen att uppdatera sidan men du borde se den nyligen skapade indexeraren i listan med status Pågående eller Lyckades, tillsammans med antalet dokument som indexerats.

   ![Meddelande om pågående indexeringsaktiviteter](media/search-get-started-portal/indexers-inprogress.png)

## <a name="view-the-index"></a>Visa indexet

Huvud tjänst sidan innehåller länkar till de resurser som skapats i Azure Kognitiv sökning-tjänsten.  Om du vill visa det index som du precis skapat klickar du på **Index** i listan över länkar. 

Vänta tills Portal sidan har uppdaterats. Efter några minuter bör du se indexet med ett antal dokument och lagrings storlek.

   ![Indexlista på instrumentpanelen för tjänster](media/search-get-started-portal/indexes-list.png)

I den här listan kan du klicka på det *hotell-exempel* index som du nyss skapade, Visa index schemat. Du kan också lägga till nya fält. 

Fliken **Fält** visar indexschemat. Rulla till slutet av listan för att ange ett nytt fält. Oftast kan du inte ändra befintliga fält. Befintliga fält har en fysisk representation i Azure Kognitiv sökning och kan därför inte ändras, inte ens i kod. Om du vill ändra ett befintligt fält från grunden så skapar du ett nytt index och tar bort det ursprungliga.

   ![exempel på indexdefinition](media/search-get-started-portal/sample-index-def.png)

Andra konstruktioner, t.ex. bedömningsprofiler och CORS-alternativ, kan läggas till när som helst.

Ägna några minuter åt att gå igenom definitionsalternativen för index så att du förstår vad du kan och inte kan redigera när du utformar ett index. Nedtonade alternativ indikerar att ett värde inte kan ändras eller tas bort. 

## <a name="query-using-search-explorer"></a><a name="query-index"></a> Fråga med Sökutforskaren

Nu bör du ha ett sökindex som du kan börja köra frågor mot med hjälp av den inbyggda frågesidan [**Sökutforskaren**](search-explorer.md). Den innehåller en sökruta så att du kan testa godtyckliga frågesträngar.

**Sökutforskaren** kan endast hantera [REST-API-förfrågningar](/rest/api/searchservice/search-documents), men stöder syntax för både [enkel frågesyntax](/rest/api/searchservice/simple-query-syntax-in-azure-search) och [fullständig Lucene-frågeparser](/rest/api/searchservice/lucene-query-syntax-in-azure-search), samt alla tillgängliga sökparametrar i åtgärder med [REST-API:et för dokumentsökning](/rest/api/searchservice/search-documents#bkmk_examples).

> [!TIP]
> Följande steg visas på 6m08s i [Azure kognitiv sökning översikts video](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Klicka på **Sökutforskaren** i kommandofältet.

   ![Kommandot Sökutforskaren](media/search-get-started-portal/search-explorer-cmd.png)

2. I list rutan **index** väljer du  *hotell-Sample-index*. Klicka på list rutan **API-version** för att se vilka REST-API: er som är tillgängliga. Använd den allmänt tillgängliga versionen (2020-06-30) för frågorna nedan.

   ![Index- och API-kommandon](media/search-get-started-portal/search-explorer-changeindex.png)

3. Klistra in frågesträngarna nedan i sökfältet och klicka på **Sök**.

   ![Frågesträng och sökknapp](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Exempelfrågor

Du kan ange termer och fraser på ett liknande sätt som du gör i en Bing- eller Google-sökning eller fullständigt angivna frågeuttryck. Resultat returneras som utförliga JSON-dokument.

### <a name="simple-query-with-top-n-results"></a>Exempelfråga med de N främsta resultaten

#### <a name="example-string-query-searchspa"></a>Exempel (strängfråga): `search=spa`

* **Sök** parametern används för att mata in en nyckelords sökning för full texts ökning, i det här fallet returneras hotell data för de som innehåller *Spa* i valfritt sökbart fält i dokumentet.

* **Sökutforskaren** returnerar resultat i JSON, vilket kan vara detaljerat och svårläst om dokumenten har en kompakt struktur. Detta är avsiktligt eftersom det är viktigt för utvecklingssyften att kunna se hela dokumentet, särskilt under testning. För en bättre användarupplevelse måste du skriva kod som [hanterar sökresultaten](search-pagination-page-layout.md) så att viktiga element framhävs.

* Dokument består av alla fält som är markerade som ”hämtningsbara” i indexet. Om du vill visa indexattribut i portalen klickar du på *hotell-exempel* i listan **index** .

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Exempel (parameteriserad fråga): `search=spa&$count=true&$top=10`

* **&** Symbolen används för att lägga till Sök parametrar, som kan anges i valfri ordning.

* Parametern **$Count = True** returnerar det totala antalet returnerade dokument. Det här värdet visas längst upp i sökresultaten. Du kan verifiera filterfrågor genom att övervaka ändringar som rapporterats via **$count=true**. Mindre antal indikerar att filtret fungerar.

* **$Top = 10** returnerar de högst rangordnade 10 dokumenten av totalt. Som standard returnerar Azure Kognitiv sökning de första 50 bästa matchningarna. Du kan öka eller minska antalet via **$top**.

### <a name="filter-the-query"></a><a name="filter-query"></a> Filtrera frågan

Filter tas med i sökbegäranden när du lägger till parametern **$filter**. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Exempel (filtrerat): `search=beach&$filter=Rating gt 4`

* Parametern **$filter** returnerar resultat som matchar de kriterier som du har angett. I det här fallet är klassificeringarna större än 4.

* Syntaxen för filtret är en OData-konstruktion. Mer information finns i [OData-filtersyntax](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-the-query"></a><a name="facet-query"></a> Fasettera frågan

Fasettfilter tas med i sökbegäranden. Du kan använda parametern facet för att returnera ett aggregerat antal dokument som matchar ett fasettvärde som du anger.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Exempel (fasetterat med områdesreducering): `search=*&facet=Category&$top=2`

* **search=*** är en tom sökning. Tomma sökningar söker efter allt. En anledning till att skicka en tom fråga är att filtrera eller fasettera över hela uppsättningen dokument. Du vill till exempel att en aspekt navigerings struktur ska bestå av alla hotell i indexet.
* **facet** returnerar en navigeringsstruktur som du kan skicka till en kontroll i användargränssnittet. Den returnerar kategorier och antal. I det här fallet baseras kategorierna på ett fält som är bekvämt kallat *kategori*. Det finns ingen agg regering i Azure Kognitiv sökning, men du kan uppskatta agg regering via `facet` , vilket ger ett antal dokument i varje kategori.

* **$top=2** hämtar tillbaka två dokument, som visar att du kan använda `top` för att både minska eller öka resultat.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Exempel (fasetterat för numeriska värden): `search=spa&facet=Rating`

* Frågan är aspekt för klassificering, i en texts ökning för *Spa*. Termen *klassificering* kan anges som en aspekt eftersom fältet är markerat som hämtnings Bart, filter Bart och fasettable i indexet, och de värden som den innehåller (numeriska, 1 till 5) är lämpliga för kategorisering av listor i grupper.

* Endast filtrerbara fält kan fasetteras. Endast hämtningsbara fält kan returneras i resultatet.

* Fältet *gradering* är en flyttal med dubbel precision och grupperingen kommer att vara med exakt värde. Mer information om gruppering efter intervall (till exempel "3 stjärn klassificeringar," "4 stjärnor" osv.) finns i [så här implementerar du fasett-navigering i Azure kognitiv sökning](./search-faceted-navigation.md#filter-based-on-a-range).


### <a name="highlight-search-results"></a><a name="highlight-query"></a> Markera sökresultat

Träffmarkering innebär att formatera all text som matchar sökordet på ett särskilt sätt inom ett givet fält. Om sökordet begravt långt ned i en beskrivning kan du använda träffmarkering för att göra det lättare att hitta ordet.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Exempel (markering): `search=beach&highlight=Description`

* I det här exemplet är det formaterade ordet *strand* lättare att hitta i fältet Beskrivning.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Exempel (språklig analys): `search=beaches&highlight=Description`

* Full texts ökning känner igen grundläggande variationer i Word-formulär. I det här fallet innehåller Sök resultaten markerad text för "strand", för hotell som har ordet i sökbara fält, som svar på en nyckelords sökning på "stränder". Tack vare språkanalysen kan olika former av samma ord visas i resultaten. 

* Azure Kognitiv sökning stöder 56-analyser från både Lucene och Microsoft. Som standard används av Azure Kognitiv sökning är standard Lucene Analyzer.

### <a name="try-fuzzy-search"></a><a name="fuzzy-search"></a> Prova fuzzy-sökning

Som standard kan felstavade sökord som *seatle* för "Seattle" inte returnera matchningar i normal sökning. Följande exempel returnerar inga resultat.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Exempel (felstavat ord, ohanterat): `search=seatle`

Du kan använda fuzzy-sökning för att hantera felstavningar. Fuzzy-sökning aktiveras när du använder den fullständiga Lucene-frågesyntaxen, som aktiveras när du gör två saker: när du anger **queryType = full** i frågan och när du lägger till **~** i söksträngen.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Exempel (felstavat ord, hanterat): `search=seatle~&queryType=full`

Det här exemplet returnerar nu dokument som innehåller matchningar på "Seattle".

När **queryType** inte är angivet används den enklare standardfrågeparsern. Den enklare frågeparsern är snabbare, men om du behöver tillgång till fuzzy-sökning, reguljära uttryck, närhetssökning eller andra typer av avancerade frågetyper behöver du den fullständiga syntaxen.

Fuzzy-sökning och sökning med jokertecken påverkar sökresultatet. Språkliga analyser utförs inte med dessa frågeformat. Innan du använder Fuzzy-och jokertecken kan du läsa [hur full texts ökning fungerar i Azure kognitiv sökning](search-lucene-query-architecture.md#stage-2-lexical-analysis) och leta efter avsnittet om undantag från lexikal analys.

Mer information om de scenarier som har Aktiver ATS med fullständig fråga i parsern finns [i Lucene-frågesyntax i Azure kognitiv sökning](/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="try-geospatial-search"></a><a name="geo-search"></a> Prova geospatial sökning

 Geospatial sökning stöds av [datatypen edm.GeographyPoint](/rest/api/searchservice/supported-data-types) i fält som innehåller koordinater. Geosearch är en filtertyp som finns med i [OData-filtersyntaxen](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Exempel (geo-koordinatfilter): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

Med den här exempelfrågan filtreras alla resultat efter platsdata, där resultaten måste ligga mindre än 5 km från en given plats (koordinaterna anges med latitud och longitud). Om du lägger till **$count** kan du se hur många resultat som returneras när du ändrar antingen avståndet eller koordinaterna.

Geospatial sökning kan vara användbart om sökprogrammet har en funktion av typen ”hitta en bensinstation i närheten av där jag befinner mig” eller om programmet har en funktion för kartnavigering. Det är dock inte fråga om någon fulltextsökning. Om du har användar krav för att söka på en stad eller ett land/en region efter namn, lägger du till fält som innehåller Orts-eller lands-/region namn, förutom koordinaterna.

## <a name="takeaways"></a>Lärdomar

I den här självstudien får du en snabb introduktion till Azure Kognitiv sökning med hjälp av Azure Portal.

Du lärde dig hur du skapar ett sökindex med hjälp av guiden **Importera data**. Du lärde dig om [indexerare](search-indexer-overview.md), som är den drivande kraften bakom guiden, samt om det grundläggande arbetsflödet vid indexgenerering, inklusive vilka [ändringar du kan göra i ett publicerat index](/rest/api/searchservice/update-index).

Med hjälp av **Sökutforskaren** i Azure-portalen lärde du dig grundläggande frågesyntax genom praktiska exempel som demonstrerade viktiga funktioner som filter, markering av träffar, fuzzy-sökning och geo-sökning.

Du har också läst hur du hittar index, indexerare och datakällor i portalen. När du får nya datakällor i framtiden kan du använda portalen för att snabbt kontrollera deras definitioner eller fältsamlingar utan större ansträngningar.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Använd en portal guide för att skapa en färdig webb program som körs i en webbläsare. Du kan prova den här guiden på det lilla index som du nyss skapade, eller använda en av de inbyggda exempel data uppsättningarna för en rikare Sök upplevelse.

> [!div class="nextstepaction"]
> [Skapa en demo-app i portalen](search-create-app-portal.md)