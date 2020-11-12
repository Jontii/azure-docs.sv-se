---
title: Hantera tvillingdiagram med relationer
titleSuffix: Azure Digital Twins
description: Se hur du hanterar en graf med digitala dubbla, genom att ansluta dem till relationer.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 73aa6f8f6ee36aeeb41fbc54afe217ac776a4ebc
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533901"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Hantera en graf med digitala dubbla med relationer

Hjärtat i Azure Digitals flätas är det [dubbla diagrammet](concepts-twins-graph.md) som representerar hela miljön. Det dubbla grafen består av enskilda digitala dubbla anslutningar via **relationer**. 

När du har en fungerande [Azure Digital-instansen](how-to-set-up-instance-portal.md) och har konfigurerat [autentiserings](how-to-authenticate-client.md) kod i din klient app, kan du använda [**DigitalTwins-API: er**](/rest/api/digital-twins/dataplane/twins) för att skapa, ändra och ta bort digitala dubbla och deras relationer i en digital Azure-instans. Du kan också använda [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)eller [Azure Digitals flätat CLI](how-to-use-cli.md).

Den här artikeln fokuserar på att hantera relationer och diagrammet som helhet. information om hur du arbetar med enskilda digitala dubbla, finns i [*så här gör du: hantera digitala dubbla*](how-to-manage-twin.md).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>Sätt att hantera diagram

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

Du kan också göra ändringar i grafen med hjälp av ADT (Azure Digital flätas) som gör att du kan visualisera dina sammanställningar och grafer och använda SDK i bakgrunden. I nästa avsnitt beskrivs det här exemplet i detalj.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Skapa relationer

Relationerna beskriver hur olika digitala anslutningar är anslutna till varandra, som utgör grunden för det dubbla diagrammet.

Relationer skapas med hjälp av `CreateOrReplaceRelationshipAsync()` anropet. 

Om du vill skapa en relation måste du ange:
* Källans dubbla ID ( `srcId` i kod exemplet nedan): ID: t för den dubbla där relationen kommer.
* Målets dubbla ID ( `targetId` i kod exemplet nedan): ID: t för den dubbla där relationen kommer.
* Ett Relations namn ( `relName` i kod exemplet nedan): den generiska typen av relation, något som _innehåller_.
* Ett relations-ID ( `relId` i kod exemplet nedan): det angivna namnet för den här relationen, t. ex. _Relationship1_.

Relations-ID: t måste vara unikt inom den aktuella källan. Det behöver inte vara globalt unikt.
Till exempel måste varje ID för en speciell relation vara unikt för den dubbla *foo*. En annan rad dubbla *staplar* kan dock ha en utgående relation som matchar samma ID för en *foo* -relation.

Följande kod exempel illustrerar hur du skapar en relation i din Azure Digital-instansen.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
            
        }
```
I din huvud metod kan du nu anropa `CreateRelationship()` funktionen för att skapa en _contains_ -relation så här: 

```csharp
await CreateRelationship(client, srcId, targetId, "contains");
```
Om du vill skapa flera relationer kan du upprepa anrop till samma metod och skicka olika Relations typer till argumentet. 

Mer information om hjälp klassen `BasicRelationship` finns i [*How-to: använda Azure Digitals dubbla API: er och SDK: er*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Skapa flera relationer mellan dubbla

Relationer kan klassificeras som antingen: 

* Utgående relationer: relationer som hör till den här dubbla som punkten utåt för att ansluta den till andra dubbla. `GetRelationshipsAsync()`Metoden används för att hämta utgående relationer för en dubbel.
* Inkommande relationer: relationer som hör till andra dubbla platser som pekar mot den här dubbla för att skapa en "inkommande" länk. `GetIncomingRelationshipsAsync()`Metoden används för att hämta inkommande relationer av en dubbel.

Det finns ingen begränsning för antalet relationer som du kan ha mellan två dubbla, och du kan ha så många relationer som du vill. 

Det innebär att du kan uttrycka flera olika typer av relationer mellan två dubblas samtidigt. Till exempel kan *dubbla a* ha både en *lagrad* *relation och en* relation med *dubbla B*.

Du kan till och med skapa flera instanser av samma typ av relation mellan samma två dubbla, om du vill. I det här exemplet kan *dubbla A* ha två olika *lagrade* relationer med *dubbla B* , så länge relationerna har olika relations-ID.

## <a name="list-relationships"></a>List relationer

Om du vill få åtkomst till listan över **utgående** relationer för en specifik i grafen kan du använda `GetRelationships()` metoden som detta:

```csharp
await client.GetRelationships()
```

Detta returnerar en `Azure.Pageable<T>` eller `Azure.AsyncPageable<T>` , beroende på om du använder en synkron eller asynkron version av anropet.

Här är ett exempel som hämtar en lista med relationer:

```csharp
public static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

```
Nu kan du anropa den här metoden för att se de utgående relationerna för de dubblarna så här:

```csharp
await FindOutgoingRelationshipsAsync(client, twin_Id);
```
Du kan använda de hämtade relationerna för att navigera till andra dubbla i grafen. Det gör du genom att läsa `target` fältet från relationen som returneras och använda det som ID för nästa anrop till `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Hitta inkommande relationer till en digital, dubbel

Azure Digitals dubbla är också ett API för att hitta alla _ *inkommande* *-relationer till en specifik dubbel. Detta är ofta användbart för omvänd navigering, eller när du tar bort en dubbel.

Föregående kod exempel fokuserade på att hitta utgående relationer från en dubbel. Följande exempel är strukturerat på samma sätt, men hittar *inkommande* relationer till den dubbla i stället.

Observera att `IncomingRelationship` anropen inte returnerar hela bröd texten i relationen.

```csharp
public static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }
```

Nu kan du anropa den här metoden för att se de ingående relationerna för de dubblarna så här:

```csharp
await FindIncomingRelationshipsAsync(client, twin_Id);
```
### <a name="list-all-twin-properties-and-relationships"></a>Lista alla dubbla egenskaper och relationer

Med hjälp av ovanstående metoder för att visa utgående och inkommande relationer till en dubbel, kan du skapa en metod som skriver ut fullständig dubbel information, inklusive de dubbla egenskaperna och båda typerna av dess relationer. Här är en exempel metod som kallas `FetchAndPrintTwinAsync()` för att visa hur du gör detta.

```csharp  
private static async Task FetchAndPrintTwinAsync(DigitalTwinsClient client, string twin_Id)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }
```

Nu kan du anropa den här funktionen i din main-metod så här: 

```csharp
await FetchAndPrintTwinAsync(client, targetId);
```
## <a name="delete-relationships"></a>Ta bort relationer

Den första parametern anger den dubbla källan (den dubbla där relationen kommer). Den andra parametern är relations-ID. Du behöver både det dubbla ID: t och relations-ID: t eftersom relations-ID: n endast är unika inom omfånget för en dubbel.

```csharp
private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
```

Du kan nu anropa den här metoden för att ta bort en relation så här:

```csharp
await DeleteRelationship(client, srcId, relId);
```

## <a name="runnable-twin-graph-sample"></a>Körbara, två diagram exempel

Följande körbara-kodfragment använder Relations åtgärderna från den här artikeln för att skapa ett sammanflätat diagram från digitala dubbla och relationer.

### <a name="set-up-the-runnable-sample"></a>Konfigurera körbara-exemplet

Kodfragmentet använder [*Room.jspå*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) och [*Floor.jspå*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) modell definitioner från [*självstudien: utforska Azure Digitals med en exempel klient program*](tutorial-command-line-app.md). Du kan använda dessa länkar om du vill gå direkt till filerna eller ladda ned dem som en del av det fullständiga exempel projektet från början till slut [här](/samples/azure-samples/digital-twins-samples/digital-twins-samples/). 

Innan du kör exemplet gör du följande:
1. Ladda ned modell filerna, placera dem i projektet och Ersätt `<path-to>` plats hållarna i koden nedan för att tala om för programmet var de finns.
2. Ersätt plats hållaren `<your-instance-hostname>` med din Azure Digital-instansen värdnamn.
3. Lägg till de här paketen i projektet:
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Du måste också konfigurera lokala autentiseringsuppgifter om du vill köra exemplet direkt. Nästa avsnitt går igenom detta.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Kör exemplet

När du har slutfört ovanstående steg kan du köra följande exempel kod direkt.

```csharp 
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {

        public static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            //Create the Azure Digital Twins client for API calls
            DigitalTwinsClient client = createDTClient();
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload models");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            string dtdl1 = File.ReadAllText("<path-to>/Floor.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            typeList.Add(dtdl1);
            // Upload the models to the service
            await client.CreateModelsAsync(typeList);

            //Create new (Floor) digital twin
            BasicDigitalTwin floorTwin = new BasicDigitalTwin();
            string srcId = "myFloorID";
            floorTwin.Metadata = new DigitalTwinMetadata();
            floorTwin.Metadata.ModelId = "dtmi:example:Floor;1";
            //Floor twins have no properties, so nothing to initialize
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(srcId, floorTwin);
            Console.WriteLine("Twin created successfully");

            //Create second (Room) digital twin
            BasicDigitalTwin roomTwin = new BasicDigitalTwin();
            string targetId = "myRoomID";
            roomTwin.Metadata = new DigitalTwinMetadata();
            roomTwin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            roomTwin.Contents = props;
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(targetId, roomTwin);
            
            //Create relationship between them
            await CreateRelationship(client, srcId, targetId, "contains");
            Console.WriteLine();

            //Print twins and their relationships
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete the relationship
            Console.WriteLine("Deleting the relationship");
            await DeleteRelationship(client, srcId, $"{srcId}-contains->{targetId}");
            Console.WriteLine();

            //Print twins and their relationships again
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();
        }

        private static DigitalTwinsClient createDTClient()
        {
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
        private async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            // Create relationship between twins
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }

        }

        private static async Task FetchAndPrintTwinAsync(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }

        private static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");
                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
    }
}
```

Här är konsol resultatet av programmet ovan: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Konsol utdata som visar den dubbla informationen, inkommande och utgående relationer för de dubbla." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> Det dubbla diagrammet är ett koncept för att skapa relationer mellan dubbla. Om du vill visa den visuella representationen av den dubbla grafen, se avsnittet [_Visualization *](how-to-manage-graph.md#visualization) i den här artikeln. 

### <a name="create-a-twin-graph-from-a-csv-file"></a>Skapa ett dubbel diagram från en CSV-fil

I praktiska användnings fall kommer dubbla hierarkier ofta att skapas från data som lagras i en annan databas eller kanske i ett kalkyl blad eller en CSV-fil. I det här avsnittet visas hur du läser data från en CSV-fil och skapar ett sammanflätat diagram.

Tänk på följande data tabell som beskriver en uppsättning digitala dubbla och relationer.

|  Modell-ID    | Dubbla ID (måste vara unikt) | Relations namn  | Mål, dubbla ID  | Dubbla init-data |
| --- | --- | --- | --- | --- |
| dtmi: exempel: våning; 1    | Floor1 | innehåller | Room1 | |
| dtmi: exempel: våning; 1    | Floor0 | innehåller | Room0 | |
| dtmi: exempel: Room; 1    | Room1 | | | {"Temperatur": 80} |
| dtmi: exempel: Room; 1    | Room0 | | | {"Temperatur": 70} |

Ett sätt att hämta dessa data till Azure Digitals, är att konvertera tabellen till en CSV-fil och skriva kod för att tolka filen till kommandon för att skapa dubbla och relationer. I följande kod exempel illustreras hur du läser data från CSV-filen och hur du skapar en dubbel graf i Azure Digitals flätas.

I koden nedan kallas CSV-filen *data.csv* , och det finns en plats hållare som representerar **värd namnet** för din Azure Digital-instans. Exemplet använder också flera paket som du kan lägga till i projektet för att hjälpa dig med den här processen.

```csharp
using System;
using System.Collections.Generic;
using System.Text.Json;
using System.Threading.Tasks;
using Azure;
using Azure.DigitalTwins.Core;
using Azure.Identity;

namespace creating_twin_graph_from_csv
{
    class Program
    {
        static async Task Main(string[] args)
        {
            List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
            List<BasicDigitalTwin> TwinList = new List<BasicDigitalTwin>();
            List<List<string>> data = ReadData();
            DigitalTwinsClient client = createDTClient();

            // Interpret the CSV file data, by each row
            foreach (List<string> row in data)
            {
                string modelID = row.Count > 0 ? row[0].Trim() : null;
                string srcID = row.Count > 1 ? row[1].Trim() : null;
                string relName = row.Count > 2 ? row[2].Trim() : null;
                string targetID = row.Count > 3 ? row[3].Trim() : null;
                string initProperties = row.Count > 4 ? row[4].Trim() : null;
                Console.WriteLine($"ModelID: {modelID}, TwinID: {srcID}, RelName: {relName}, TargetID: {targetID}, InitData: {initProperties}");
                Dictionary<string, object> props = new Dictionary<string, object>();
                // Parse properties into dictionary (left out for compactness)
                // ...

                // Null check for source and target ID's
                if (srcID != null && srcID.Length > 0 && targetID != null && targetID.Length > 0)
                {
                    BasicRelationship br = new BasicRelationship()
                    {
                        SourceId = srcID,
                        TargetId = targetID,
                        Name = relName
                    };
                    RelationshipRecordList.Add(br);
                }
                BasicDigitalTwin srcTwin = new BasicDigitalTwin();
                srcTwin.Id = srcID;
                srcTwin.Metadata = new DigitalTwinMetadata();
                srcTwin.Metadata.ModelId = modelID;
                srcTwin.Contents = props;
                TwinList.Add(srcTwin);
            }

            // Create digital twins 
            foreach (BasicDigitalTwin twin in TwinList)
            {
                try
                {
                    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twin.Id, twin);
                    Console.WriteLine("Twin is created");
                }
                catch (RequestFailedException e)
                {
                    Console.WriteLine($"Error {e.Status}: {e.Message}");
                }
            }
            // Create relationships between the twins
            foreach (BasicRelationship rec in RelationshipRecordList)
            {
                try
                {
                    string relId = $"{rec.SourceId}-{rec.Name}->{rec.TargetId}";
                    await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(rec.SourceId, relId, rec);
                    Console.WriteLine("Relationship is created");
                }
                catch (RequestFailedException e)
                {
                    Console.WriteLine($"Error {e.Status}: {e.Message}");
                }
            }
        }

        // Method to ingest data from the CSV file
        public static List<List<string>> ReadData()
        {
            string path = "<path-to>/data.csv";
            string[] lines = System.IO.File.ReadAllLines(path);
            List<List<string>> data = new List<List<string>>();
            int count = 0;
            foreach (string line in lines)
            {
                if (count++ == 0)
                    continue;
                List<string> cols = new List<string>();
                data.Add(cols);
                string[] columns = line.Split(',');
                foreach (string column in columns)
                {
                    cols.Add(column);
                }
            }
            return data;
        }
        // Method to create the digital twins client
        private static DigitalTwinsClient createDTClient()
        {

            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
    }
}

```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att skicka frågor till en Azure Digitals dubbla graf:
* [*Begrepp: frågespråk*](concepts-query-language.md)
* [*Anvisningar: fråga det dubbla diagrammet*](how-to-query-graph.md)