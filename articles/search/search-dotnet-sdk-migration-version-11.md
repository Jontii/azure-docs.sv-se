---
title: Uppgradera till .NET SDK version 11
titleSuffix: Azure Cognitive Search
description: Migrera kod till Azure Kognitiv sökning .NET SDK version 11 från äldre versioner. Läs om vad som är nytt och vilka kod ändringar som krävs.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5c943de87cbf53d4971e5cbf58768221bf2cf248
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462266"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Uppgradera till Azure Kognitiv sökning .NET SDK version 11

Den här artikeln hjälper dig att uppgradera till version 11 om du använder version 10,0 eller äldre av [.NET SDK](/dotnet/api/overview/azure/search).

Version 11 är ett helt omdesignat klient bibliotek som släpps av Azure SDK Development-teamet (tidigare versioner genererades av Azure Kognitiv sökning Development Team). Biblioteket har gjorts om för bättre konsekvens med andra Azure-klient bibliotek, vilket tar ett beroende på [Azure. Core](/dotnet/api/azure.core) och [System.Text.Jspå](/dotnet/api/system.text.json)och implementerar välbekanta metoder för vanliga uppgifter.

Några viktiga skillnader som du ser i den nya versionen är:

+ Ett paket och bibliotek i stället för flera
+ Ett nytt paket namn: `Azure.Search.Documents` i stället för `Microsoft.Azure.Search` .
+ Tre klienter i stället för två: `SearchClient` , `SearchIndexClient` , `SearchIndexerClient`
+ Namngivning av skillnader i flera API: er och små strukturella skillnader som fören klar vissa uppgifter

> [!NOTE]
> Granska [**ändrings loggen**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) för en lista över ändringar i .NET SDK version 11.

## <a name="package-and-library-consolidation"></a>Paket-och biblioteks konsolidering

Version 11 konsoliderar flera paket och bibliotek till ett. Efter migreringen har du färre bibliotek att hantera.

+ [Azure.Search.Documents-paket](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [API-referens för klient biblioteket](/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)

## <a name="client-differences"></a>Klient skillnader

I förekommande fall mappar följande tabell klient biblioteken mellan de två versionerna.

| Åtgärds omfång | Microsoft. Azure. search &nbsp; (v10) | Azure.Search.Documents &nbsp; (V11) |
|---------------------|------------------------------|------------------------------|
| Klient som används för frågor och för att fylla ett index. | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](/dotnet/api/azure.search.documents.searchclient) |
| Klient som används för index, analyser, synonym kartor | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| Klient som används för indexerare, data källor, färdighetsuppsättningar | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**ny**)](/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient` finns i båda versionerna, men har stöd för olika saker. `SearchIndexClient`Skapa index och andra objekt i version 10. I version 11 `SearchIndexClient` fungerar med befintliga index. För att undvika förvirring vid uppdatering av kod ska du vara mindful i den ordning som klient referenserna uppdateras. Genom [att följa stegen i steg för uppgraderingen](#UpgradeSteps) bör du undvika eventuella sträng ersättnings problem.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>Namn och andra API-skillnader

Förutom klient skillnaderna (anges tidigare och utelämnade här), har flera andra API: er bytt namn och i vissa fall har omkonstruerats. Skillnaderna mellan klass namn sammanfattas nedan. Den här listan är inte fullständig, men den gör Group API-ändringar efter uppgift, vilket kan vara till hjälp för revideringar av vissa kodblock. En lista över API-uppdateringar finns i [ändrings loggen](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) för `Azure.Search.Documents` på GitHub.

### <a name="authentication-and-encryption"></a>Autentisering och kryptering

| Version 10 | Version 11 motsvarande |
|------------|-----------------------|
| [SearchCredentials](/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey` (fanns i för [hands versionen SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) som en allmänt tillgänglig funktion) | [SearchResourceEncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>Index, analyser, synonym kartor

| Version 10 | Version 11 motsvarande |
|------------|-----------------------|
| [Index](/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [Fält](/dotnet/api/microsoft.azure.search.models.field) | [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analysen](/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) (även `AnalyzerName` till `LexicalAnalyzerName` ) |
| [AnalyzeRequest](/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (även `StandardTokenizerV2` till `LuceneStandardTokenizerV2` ) |
| [TokenInfo](/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizer](/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (även `TokenizerName` till `LexicalTokenizerName` ) |
| [SynonymMap. format](/dotnet/api/microsoft.azure.search.models.synonymmap.format) | Inga. Ta bort referenser till `Format` . |

Fält definitioner är effektiviserade: [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield), [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield), [ComplexField](/dotnet/api/azure.search.documents.indexes.models.complexfield) är nya API: er för att skapa fält definitioner.

### <a name="indexers-datasources-skillsets"></a>Indexerare, data källor, färdighetsuppsättningar

| Version 10 | Version 11 motsvarande |
|------------|-----------------------|
| [Indexerare](/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [Datakälla](/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Kvalifikation](/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Färdigheter](/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>Data import

| Version 10 | Version 11 motsvarande |
|------------|-----------------------|
| [IndexAction](/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>Fråga om definitioner och resultat

| Version 10 | Version 11 motsvarande |
|------------|-----------------------|
| [DocumentSearchResult](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](/dotnet/api/azure.search.documents.models.searchresult-1) eller [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1), beroende på om resultatet är ett enda dokument eller flera. |
| [DocumentSuggestResult](/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>Vad är i version 11

Varje version av ett Azure Kognitiv sökning klient bibliotek är riktad mot en motsvarande version av REST API. REST API anses vara grundläggande för tjänsten, med enskilda SDK: er för att figursätta en version av REST API. Som .NET-utvecklare kan det vara bra att granska [REST API dokumentation](/rest/api/searchservice/) om du vill ha mer bakgrund på vissa objekt eller åtgärder.

Version 11 är inriktad på [2020-06-30 Search-tjänsten](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json). Eftersom version 11 också är ett nytt klient bibliotek som byggts från grunden, har de flesta utvecklings ansträngningar fokuserat på likvärdighet med version 10, och vissa REST API funktioner stöds fortfarande.

Version 11,0 har fullständigt stöd för följande objekt och åtgärder:

+ Skapa och hantera index
+ Skapa och hantera synonym mappning
+ Alla typer av frågetyper och syntax (utom geo-spatial-filter)
+ Indexerare objekt och åtgärder för indexering av Azure-datakällor, inklusive data källor och färdighetsuppsättningar

Version 11,1 lägger till följande:

+ [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) (lades till i 11,1)
+ [Egenskapen serialiserare](/dotnet/api/azure.search.documents.searchclientoptions.serializer) (lades till i 11,1) för att stödja anpassad serialisering

### <a name="pending-features"></a>Väntande funktioner

Följande version 10-funktioner är ännu inte tillgängliga i version 11. Om du behöver dessa funktioner ska du hålla på migrering tills de stöds.

+ geospatiala typer
+ [Knowledge Store](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera

Följande steg hjälper dig att komma igång med en kod migrering genom att gå igenom den första uppsättningen nödvändiga uppgifter, särskilt i avseende på klient referenser.

1. Installera [Azure.Search.Documents-paketet](https://www.nuget.org/packages/Azure.Search.Documents/) genom att högerklicka på dina projekt referenser och välja "hantera NuGet-paket..." i Visual Studio.

1. Ersätt med direktiv för Microsoft. Azure. Sök med följande:

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. För klasser som kräver JSON-serialisering ersätter du `using Newtonsoft.Json` med `using System.Text.Json.Serialization` .

1. Ändra koden för klientautentisering. I tidigare versioner skulle du använda egenskaperna för klient objekt för att ange API-nyckeln (till exempel egenskapen [SearchServiceClient. credentials](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) ). I den aktuella versionen använder du klassen [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) för att skicka nyckeln som en autentiseringsuppgift, så om det behövs kan du uppdatera API-nyckeln utan att skapa nya klient objekt.

   Klient egenskaper har effektiviserats till just `Endpoint` , `ServiceName` och (där det är `IndexName` lämpligt). I följande exempel används klassen system- [URI](/dotnet/api/system.uri) för att ange slut punkten och [miljö](/dotnet/api/system.environment) klassen som ska läsas i nyckelvärdet:

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. Lägg till nya klient referenser för indexerare-relaterade objekt. Om du använder indexerare, data källor eller färdighetsuppsättningar ändrar du klient referenserna till [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). Den här klienten är ny i version 11 och har ingen Antecedent.

1. Uppdatera klient referenser för frågor och data import. Instanser av [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) ska ändras till [SearchClient](/dotnet/api/azure.search.documents.searchclient). Se till att du fångar alla instanser innan du fortsätter till nästa steg för att undvika namn förvirring.

1. Uppdatera klient referenser för index, indexerare, synonym mappning och Analyzer-objekt. Instanser av [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) ska ändras till [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient). 

1. Uppdatera klasser, metoder och egenskaper så mycket som möjligt för att använda API: erna för det nya biblioteket. Avsnittet [namngivnings skillnader](#naming-differences) är en plats för att starta, men du kan också granska [ändrings loggen](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md).

   Om du har problem med att hitta motsvarande API: er, rekommenderar vi att du loggar in ett problem [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) så att vi kan förbättra dokumentationen eller undersöka problemet.

1. Återskapa lösningen. När du har åtgärdat eventuella build-fel eller varningar kan du göra ytterligare ändringar i programmet för att dra nytta av [nya funktioner](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>Bryta ändringar i version 11

Med tanke på de ändringar som görs i bibliotek och API: er, är en uppgradering till version 11 icke-trivial och utgör en brytande ändring i den mening att koden inte längre är bakåtkompatibel med version 10 och tidigare. En grundlig granskning av skillnaderna finns i [ändrings loggen](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) för `Azure.Search.Documents` .

När det gäller uppdateringar av tjänst version, där kod ändringar i version 11 relaterar till befintliga funktioner (och inte bara en omstrukturering av API: er), så kommer du att se följande ändringar:

+ [Algoritmen för BM25](index-ranking-similarity.md) ersätter den tidigare rangordningen med nyare teknik. Nya tjänster kommer att använda den här algoritmen automatiskt. För befintliga tjänster måste du ange parametrar för att använda den nya algoritmen.

+ [Sorterade resultat](search-query-odata-orderby.md) för null-värden har ändrats i den här versionen, med null-värden som visas först om sorteringen är `asc` och sist `desc` . Om du har skrivit kod för att hantera hur null-värden sorteras bör du granska och eventuellt ta bort koden om den inte längre behövs.

## <a name="next-steps"></a>Nästa steg

+ [Azure.Search.Documents-paket](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Exempel på GitHub](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Document API-referens](/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)