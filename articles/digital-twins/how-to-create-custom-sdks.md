---
title: 'Skapa anpassade SDK: er för Azure Digitals dubbla med AutoRest'
titleSuffix: Azure Digital Twins
description: 'Se hur du genererar anpassade SDK: er för att använda Azure Digitals dubbla med andra språk än C#.'
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-js
ms.openlocfilehash: 1ccbe6cb332f357eeef02dff22b8a4be328b8de0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324237"
---
# <a name="create-custom-sdks-for-azure-digital-twins-using-autorest"></a>Skapa anpassade SDK: er för Azure Digitals dubbla med AutoRest

Just nu är de enda publicerade data Plans SDK: erna för att interagera med Azures digitala dubbla API: er för .NET (C#), Java Script och Java. Du kan läsa om dessa SDK: er och API: erna i allmänhet i [*How-to: använda Azure Digitals dubbla API: er och SDK: er*](how-to-use-apis-sdks.md). Om du arbetar på ett annat språk visar den här artikeln hur du skapar ett eget data plan SDK på det språk som du väljer med hjälp av AutoRest.

>[!NOTE]
> Du kan också använda AutoRest för att generera ett Control plan SDK om du vill. Det gör du genom att följa anvisningarna i den här artikeln med hjälp av [openapi-filen (Control plan Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/preview/2020-03-01-preview) i stället för data planet en.

## <a name="set-up-your-machine"></a>Konfigurera din dator

Om du vill generera ett SDK behöver du:
* [AutoRest](https://github.com/Azure/autorest), version 2.0.4413 (version 3 stöds inte för närvarande)
* [Node.js](https://nodejs.org) som ett krav för AutoRest
* Azure Digitals [Swagger-fil (data Plans-openapi)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins/preview/2020-05-31-preview) som är berättigad *digitaltwins.jspå*, och dess tillhör ande mapp med exempel. Hämta Swagger-filen och dess mapp med exempel till den lokala datorn.

När datorn är utrustad med allt från listan ovan är du redo att använda AutoRest för att skapa SDK: n.

## <a name="create-the-sdk-with-autorest"></a>Skapa SDK med AutoRest 

Om du har Node.js installerat kan du köra det här kommandot för att kontrol lera att du har rätt version av AutoRest installerat:
```cmd/sh
npm install -g autorest@2.0.4413
```

Följ dessa steg om du vill köra AutoRest mot Azure Digitals Swagger-filen:
1. Kopiera Azure Digitals Swagger-fil och dess tillhör ande mapp med exempel till en arbets katalog.
2. Använd ett kommando tolks fönster för att växla till den arbets katalogen.
3. Kör AutoRest med följande kommando. Ersätt `<language>` plats hållaren med önskat språk: `python` ,, `java` `go` och så vidare. (Du hittar den fullständiga listan med alternativ i README- [filen för AutoRest](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

Därför visas en ny mapp med namnet *ADTApi* i din arbets katalog. De genererade SDK-filerna kommer att ha namn området *ADTApi*. Du kommer att fortsätta att använda det namn området genom resten av användnings exemplen i den här artikeln.

AutoRest har stöd för en mängd olika språk kods generatorer.

## <a name="add-the-sdk-to-a-visual-studio-project"></a>Lägg till SDK i ett Visual Studio-projekt

Du kan inkludera de filer som genereras av AutoRest direkt i en .NET-lösning. Det är dock troligt att du vill lägga till Azure Digitals-SDK: n i flera separata projekt (dina klient program, Azure Functions appar och så vidare). Därför kan det vara praktiskt att skapa ett separat projekt (ett .NET-klass bibliotek) från de genererade filerna. Sedan kan du inkludera detta klass biblioteks projekt i flera lösningar som en projekt referens.

Det här avsnittet innehåller anvisningar om hur du skapar SDK som ett klass bibliotek, vilket är ett eget projekt och kan ingå i andra projekt. De här stegen är beroende av **Visual Studio** (du kan installera den senaste versionen [härifrån).](https://visualstudio.microsoft.com/downloads/)

Här är stegen:

1. Skapa en ny Visual Studio-lösning för ett klass bibliotek
2. Använd *ADTApi* som projekt namn
3. I Solution Explorer högerklickar du på *ADTApi* -projektet för den genererade lösningen och väljer *Lägg till > befintligt objekt...*
4. Hitta mappen där du genererade SDK och välj filerna på rotnivån
5. Tryck på OK
6. Lägg till en mapp i projektet (Högerklicka på projektet i Solution Explorer och välj *Lägg till > ny mapp*)
7. Namnge mapparna *modeller*
8. Högerklicka på mappen *modeller* i lösnings Utforskaren och välj *Lägg till > befintligt objekt...*
9. Välj filerna i mappen *modeller* i den genererade SDK: n och tryck på OK

För att utveckla SDK: n måste ditt projekt ha följande referenser:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Om du vill lägga till dessa öppnar du *verktyg > NuGet Package Manager > hantera NuGet-paket för lösningen.*...

1. I panelen kontrollerar du att fliken *Bläddra* är markerad
2. Sök efter *Microsoft. rest*
3. Välj `ClientRuntime` `ClientRuntime.Azure` paketen och Lägg till dem i din lösning

Nu kan du skapa projektet och inkludera det som en projekt referens i Azure Digitals dubbla program som du skriver.

## <a name="general-guidelines-for-generated-sdks"></a>Allmänna rikt linjer för genererade SDK: er

Det här avsnittet innehåller allmän information och rikt linjer för att använda den genererade SDK: n.

### <a name="synchronous-and-asynchronous-calls"></a>Synkrona och asynkrona anrop

Alla SDK-funktioner ingår i synkrona och asynkrona versioner.

### <a name="typed-and-untyped-data"></a>Skrivna och data utan typ

REST API-anrop returnerar vanligt vis starkt skrivna objekt. Men eftersom Azure Digital-enheter låter användare definiera sina egna anpassade typer för dubbla, finns det inget sätt att fördefiniera statiska retur data för många Azure Digital-samtal. I stället returnerar API: erna starkt skrivna omslutnings typer där det är tillämpligt och de anpassade dubbla data är i Json.NET-objekt (används där data typen "objekt" visas i API-signaturer). Du kan omvandla dessa objekt korrekt i din kod.

### <a name="error-handling"></a>Felhantering

När ett fel uppstår i SDK (inklusive HTTP-fel som 404), kommer SDK att utlösa ett undantag. Därför är det viktigt att kapsla in alla API-anrop inom try/catch-block.

Här är ett kodfragment som försöker lägga till en dubbel och fångar upp eventuella fel i den här processen:

```csharp
try
{
    await client.DigitalTwins.AddAsync(id, initData);
    Console.WriteLine($"Created a twin successfully: {id}");
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
}
```

### <a name="paging"></a>Sidindelning

AutoRest genererar två typer av sid indelnings mönster för SDK:
* En för alla API: er utom fråge-API
* En för fråge-API

I växlings mönstret som inte är fråga finns det två versioner av varje anrop:
* En version som gör det första anropet (till exempel `DigitalTwins.ListEdges()` )
* En version för att hämta följande sidor. Dessa anrop har suffixet "Next" (t. ex. `DigitalTwins.ListEdgesNext()` )

Här är ett kodfragment som visar hur du hämtar en växlad lista över utgående relationer från Azure Digitals:
```csharp
try
{
    // List to hold the results in
    List<object> relList = new List<object>();
    // Enumerate the IPage object returned to get the results
    // ListAsync will throw if an error occurs
    IPage<object> relPage = await client.DigitalTwins.ListEdgesAsync(id);
    relList.AddRange(relPage);
    // If there are more pages, the NextPageLink in the page is set
    while (relPage.NextPageLink != null)
    {
        // Get more pages...
        relPage = await client.DigitalTwins.ListEdgesNextAsync(relPage.NextPageLink);
        relList.AddRange(relPage);
    }
    Console.WriteLine($"Found {relList.Count} relationships on {id}");
    // Do something with each object found
    // As relationships are custom types, they are JSON.Net types
    foreach (JObject r in relList)
    {
        string relId = r.Value<string>("$edgeId");
        string relName = r.Value<string>("$relationship");
        Console.WriteLine($"Found relationship {relId} from {id}");
    }
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error retrieving relationships on {id}: {e.Response.StatusCode}");
}
```

Det andra mönstret genereras bara för fråge-API: et. Den använder `continuationToken` uttryckligen.

Här är ett exempel med det här mönstret:

```csharp
string query = "SELECT * FROM digitaltwins";
string conToken = null; // continuation token from the query
int page = 0;
try
{
    // Repeat the query while there are pages
    do
    {
        QuerySpecification spec = new QuerySpecification(query, conToken);
        QueryResult qr = await client.Query.QueryTwinsAsync(spec);
        page++;
        Console.WriteLine($"== Query results page {page}:");
        if (qr.Items != null)
        {
            // Query returns are JObjects
            foreach(JObject o in qr.Items)
            {
                string twinId = o.Value<string>("$dtId");
                Console.WriteLine($"  Found {twinId}");
            }
        }
        Console.WriteLine($"== End query results page {page}");
        conToken = qr.ContinuationToken;
    } while (conToken != null);
} catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error in twin query: ${e.Response.StatusCode}");
}
```

## <a name="next-steps"></a>Nästa steg

Gå igenom stegen för att skapa en klient-app där du kan använda ditt SDK:
* [*Självstudie: koda en klient app*](tutorial-code.md)