---
title: Hantera digitala tvillingar
titleSuffix: Azure Digital Twins
description: Se hur du hämtar, uppdaterar och tar bort enskilda dubbla och relationer.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ede358cdbe533a32ff99fbd736e171463472e45c
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461334"
---
# <a name="manage-digital-twins"></a>Hantera digitala tvillingar

Entiteter i din miljö representeras av [digitala dubbla](concepts-twins-graph.md). Att hantera digitala dubbla, kan vara att skapa, ändra och ta bort. Om du vill utföra dessa åtgärder kan du använda [**DigitalTwins-API: er**](/rest/api/digital-twins/dataplane/twins), [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true)eller [Azure Digitals flätade CLI](how-to-use-cli.md).

Den här artikeln fokuserar på att hantera digitala dubbla, information om hur du arbetar med relationer och det [dubbla diagrammet](concepts-twins-graph.md) som helhet finns i [*instruktion: hantera den dubbla grafen med relationer*](how-to-manage-graph.md).

> [!TIP]
> Alla SDK-funktioner ingår i synkrona och asynkrona versioner.

## <a name="create-a-digital-twin"></a>Skapa en digital, dubbel

Om du vill skapa en dubbel, använder du `CreateDigitalTwin()` -metoden på tjänst klienten så här:

```csharp
await client.CreateDigitalTwinAsync("myTwinId", initData);
```

Om du vill skapa en digital Digital måste du ange:
* Önskat ID för den digitala dubbla
* Den [modell](concepts-models.md) som du vill använda

Om du vill kan du ange startvärden för alla egenskaper för den digitala, dubbla. 

Värdena modell och initial egenskap anges via `initData` parametern, som är en JSON-sträng som innehåller relevanta data. Fortsätt till nästa avsnitt om du vill ha mer information om att strukturera objektet.

> [!TIP]
> När du har skapat eller uppdaterat en dubbel, kan det finnas en fördröjning på upp till 10 sekunder innan ändringarna visas i [frågor](how-to-query-graph.md). `GetDigitalTwin`API (beskrivs [längre fram i den här artikeln](#get-data-for-a-digital-twin)) förväntar sig inte den här fördröjningen, så om du behöver ett direkt svar använder du API-anropet i stället för att fråga för att se dina nyligen skapade dubbla. 

### <a name="initialize-model-and-properties"></a>Initiera modell och egenskaper

Det dubbla skapande-API: et accepterar ett objekt som är serialiserat i en giltig JSON-Beskrivning av de dubbla egenskaperna. Se [*begrepp: digitala garn och den dubbla grafen*](concepts-twins-graph.md) för en beskrivning av JSON-formatet för en dubbel. 

Först kan du skapa ett data objekt som representerar den dubbla och dess egenskaps data. Sedan kan du använda `JsonSerializer` för att skicka en serialiserad version av det här objektet till API-anropet för `initdata` parametern, så här:

```csharp
await client.CreateDigitalTwinAsync(srcId, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```
Du kan skapa ett parameter objekt antingen manuellt eller med hjälp av en angiven hjälp klass. Här är ett exempel på var och en.

#### <a name="create-twins-using-manually-created-data"></a>Skapa dubbla med manuellt skapade data

Utan att använda anpassade hjälp klasser kan du representera en dubbels egenskaper i a `Dictionary<string, object>` , där `string` är namnet på egenskapen och `object` är ett objekt som representerar egenskapen och dess värde.

[!INCLUDE [Azure Digital Twins code: create twin](../../includes/digital-twins-code-create-twin.md)]

#### <a name="create-twins-with-the-helper-class"></a>Skapa dubbla med hjälp av klassen

Med hjälp klassen i `BasicDigitalTwin` kan du lagra egenskaps fält i ett "" ""-objekt direkt. Du kanske fortfarande vill bygga listan med egenskaper med hjälp av en `Dictionary<string, object>` , som sedan kan läggas till i det dubbla objektet som dess `CustomProperties` direkt.

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.CustomProperties = props;

client.CreateDigitalTwinAsync("myRoomId", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
Console.WriteLine("The twin is created successfully");
```

>[!NOTE]
> `BasicDigitalTwin` objekt levereras med ett `Id` fält. Du kan lämna fältet tomt, men om du lägger till ett ID-värde måste det matcha ID-parametern som skickas till `CreateDigitalTwin()` anropet. Exempel:
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>Hämta data för en digital, dubbel

Du kan komma åt information om alla digitala dubbla genom att anropa- `GetDigitalTwin()` metoden så här:

```csharp
object result = await client.GetDigitalTwin(id);
```
Anropet returnerar dubbla data som en JSON-sträng. Här är ett exempel på hur du kan använda den för att Visa dubbel information:

```csharp
Response<string> res = client.GetDigitalTwin("myRoomId");
twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
  if (twin.CustomProperties.TryGetValue(prop, out object value))
  Console.WriteLine($"Property '{prop}': {value}");
}
```
Endast egenskaper som har angetts minst en gång returneras när du hämtar en delad med- `GetDigitalTwin()` metoden.

>[!TIP]
>`displayName`För en enhet är en del av modellens metadata, så den visas inte när data hämtas för den dubbla instansen. Om du vill se det här värdet kan du [Hämta det från modellen](how-to-manage-model.md#retrieve-models).

Om du vill hämta flera multiplar med ett enda API-anrop, se fråge-API-exemplen i [*How-to: fråga det dubbla diagrammet*](how-to-query-graph.md).

Tänk på följande modell (skrivet i [Digitals definitions språk (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)) som definierar en *måne*:

```json
{
    "@id": "dtmi:example:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "contents": [
        {
            "@type": "Property",
            "name": "radius",
            "schema": "double",
            "writable": true
        },
        {
            "@type": "Property",
            "name": "mass",
            "schema": "double",
            "writable": true
        }
    ]
}
```
Resultatet av att ringa `object result = await client.GetDigitalTwinAsync("my-moon");` på en *måne*-typ kan se ut så här:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

De definierade egenskaperna för den digitala kanten returneras som toppnivå egenskaper på den digitala dubbla. Metadata-eller system information som inte ingår i DTDL-definitionen returneras med ett `$` prefix. Metadata-egenskaper inkluderar:
* ID: t för den digitala dubbla i den här Azure Digital-instansen, som `$dtId` .
* `$etag`, ett standard-HTTP-fält som tilldelas av webb servern.
* Andra egenskaper i ett `$metadata` avsnitt. Exempel:
    - DTMI för den digitala dubbla.
    - Synkroniseringsstatus för varje skrivbar egenskap. Detta är mest användbart för enheter, där det är möjligt att tjänsten och enheten har avvikande status (till exempel när en enhet är offline). Den här egenskapen gäller för närvarande endast för fysiska enheter som är anslutna till IoT Hub. Med data i avsnittet metadata är det möjligt att förstå fullständig status för en egenskap samt de senast ändrade tidsstämplar. Mer information om synkroniseringsstatus finns i [den här IoT Hub själv studie kursen](../iot-hub/tutorial-device-twins.md) om synkronisering av enhets status.
    - Tjänstspecifika metadata, t. ex. från IoT Hub eller Azure digitala dubbla. 

Du kan parsa den returnerade JSON-filen för den dubbla med ett JSON-tolknings bibliotek som du väljer, till exempel `System.Text.Json` .

Du kan också använda den serialiserande hjälp klassen `BasicDigitalTwin` som ingår i SDK: n, vilket returnerar de dubbla metadata och egenskaperna i förparsat formulär. Här är ett exempel:

```csharp
Response<string> res = client.GetDigitalTwin(twin_Id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

Du kan läsa mer om serialiserings hjälp klasser i [*How-to: använda Azure Digitals dubbla API: er och SDK: er*](how-to-use-apis-sdks.md).

## <a name="update-a-digital-twin"></a>Uppdatera en digital delad

Om du vill uppdatera egenskaperna för en digital, så skriver du den information som du vill ersätta i [JSON patch](http://jsonpatch.com/) -format. På så sätt kan du ersätta flera egenskaper samtidigt. Sedan skickar du JSON-korrigerings dokumentet till en `UpdateDigitalTwin()` metod:

```csharp
await client.UpdateDigitalTwin(id, patch);
```

Ett korrigerings anrop kan uppdatera så många egenskaper på samma sätt som du vill (även alla). Om du behöver uppdatera egenskaper över flera multiplar behöver du ett separat uppdaterings anrop för var och en.

> [!TIP]
> När du har skapat eller uppdaterat en dubbel, kan det finnas en fördröjning på upp till 10 sekunder innan ändringarna visas i [frågor](how-to-query-graph.md). `GetDigitalTwin`API: et (beskrivs [tidigare i den här artikeln](#get-data-for-a-digital-twin)) förväntar sig inte den här fördröjningen, så Använd API-anropet istället för att fråga om du vill se dina nyligen uppdaterade dubbla om du behöver ett direkt svar. 

Här är ett exempel på en JSON-patch-kod. Det här dokumentet ersätter *Mass* *-och RADIUS-* egenskapsvärdena för det digitala dubbla objektet som tillämpas på.

```json
[
  {
    "op": "replace",
    "path": "/mass",
    "value": 0.0799
  },
  {
    "op": "replace",
    "path": "/radius",
    "value": 0.800
  }
]
```
Du kan skapa uppdateringar manuellt eller genom att använda en serialiserande hjälp klass i [SDK](how-to-use-apis-sdks.md). Här är ett exempel på var och en.

#### <a name="create-patches-manually"></a>Skapa korrigeringar manuellt

```csharp
List<object> twinData = new List<object>();
twinData.Add(new Dictionary<string, object>() {
    { "op", "add"},
    { "path", "/Temperature"},
    { "value", 25.0}
});

await client.UpdateDigitalTwinAsync(twin_Id, JsonSerializer.Serialize(twinData));
Console.WriteLine("Updated twin properties");
FetchAndPrintTwin(twin_Id, client);
}
```

#### <a name="create-patches-using-the-helper-class"></a>Skapa korrigeringar med hjälp av klassen

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
await client.UpdateDigitalTwinAsync(twin_Id, uou.Serialize());
```

### <a name="update-properties-in-digital-twin-components"></a>Uppdatera egenskaper i digitala dubbla komponenter

Kom ihåg att en modell kan innehålla komponenter, så att den kan bestå av andra modeller. 

Om du vill korrigera egenskaper i en digital-enhets komponent kan du använda Path-syntax i JSON-korrigering:

```json
[
  {
    "op": "replace",
    "path": "/mycomponentname/mass",
    "value": 0.0799
  }
]
```

### <a name="update-a-digital-twins-model"></a>Uppdatera en digital Garns modell

`UpdateDigitalTwin()`Funktionen kan också användas för att migrera en digital, dubbel till en annan modell. 

Anta till exempel följande JSON-korrigerings dokument som ersätter det digitala `$model` området metadata:

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:example:foo;1"
  }
]
```

Den här åtgärden kan bara utföras om den digitala filen som ändras av korrigeringen överensstämmer med den nya modellen. 

Se följande exempel:
1. Föreställ dig ett digitalt med en modell av *foo_old*. *foo_old* definierar en obligatorisk egenskaps *vikt*.
2. Den nya modell *foo_new* definierar en egenskaps vikt och lägger till en ny obligatorisk egenskaps *temperatur*.
3. Efter korrigeringen måste den digitala, dubbla, ha både en egenskap för massa och temperatur. 

Korrigeringen för den här situationen måste uppdatera både modellen och den dubbla egenskapen temperatur, så här:

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:example:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

### <a name="handle-conflicting-update-calls"></a>Hantera motstridiga uppdaterings anrop

Azure Digitals dubbla, säkerställer att alla inkommande begär Anden bearbetas efter den andra. Det innebär att även om flera funktioner försöker uppdatera samma egenskap på en enhet samtidigt, **behöver du inte** skriva explicit låsnings kod för att hantera konflikten.

Det här beteendet sker per nivå. 

Ett exempel är att anta ett scenario där de här tre anropen kommer till samma tidpunkt: 
*   Skriv egenskap A på *Twin1*
*   Skriv egenskap B på *Twin1*
*   Skriv egenskap A på *Twin2*

De två anropen som ändrar *Twin1* körs en efter en annan och ändrings meddelanden genereras för varje ändring. Anropet till Modify *Twin2* kan köras samtidigt utan konflikter, så snart det kommer.

## <a name="delete-a-digital-twin"></a>Ta bort en digital delad

Du kan ta bort dubbla med- `DeleteDigitalTwin()` metoden. Men du kan bara ta bort en dubbel när den inte har fler relationer. Så ta bort de dubbla inkommande och utgående relationerna först.

Här är ett exempel på koden för att ta bort dubbla och deras relationer:

```csharp
static async Task DeleteTwin(string id)
{
    await FindAndDeleteOutgoingRelationshipsAsync(id);
    await FindAndDeleteIncomingRelationshipsAsync(id);
    try
    {
        await client.DeleteDigitalTwin(id);
    } catch (RequestFailedException exc)
    {
        Console.WriteLine($"*** Error:{exc.Error}/{exc.Message}");
    }
}

public async Task FindAndDeleteOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);

        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```
### <a name="delete-all-digital-twins"></a>Ta bort alla digitala dubbla

Ett exempel på hur du tar bort alla dubbla på en gång får du genom att ladda ned exempel appen som används i [_Tutorial: utforska grunderna med ett exempel på klient program *](tutorial-command-line-app.md). *CommandLoop.cs* -filen gör detta i en `CommandDeleteAllTwins()` funktion.

## <a name="manage-twins-using-runnable-code-sample"></a>Hantera dubbla med körbara kod exempel

Du kan använda körbara-kod exemplet nedan för att skapa en dubbel, uppdatera dess information och ta bort den dubbla. 

Kodfragmentet använder [Room.jspå](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) modell definitionen från [*självstudien: utforska digitala Azure-enheter med ett exempel på en klient-app*](tutorial-command-line-app.md). Du kan använda den här länken för att gå direkt till filen eller ladda ned den som en del av ett fullständigt exempel projekt från början till slut [här](/samples/azure-samples/digital-twins-samples/digital-twins-samples/).

Innan du kör exemplet gör du följande:
1. Ladda ned modell filen, placera den i projektet och Ersätt `<path-to>` plats hållaren i koden nedan för att tala om för programmet var du hittar det.
2. Ersätt plats hållaren `<your-instance-hostname>` med din Azure Digital-instansen värdnamn.
3. Lägg till de här paketen i projektet:
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Kör sedan exemplet.

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
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload a model");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            // Upload the model to the service
            await client.CreateModelsAsync(typeList);

            //Create new digital twin
            BasicDigitalTwin twin = new BasicDigitalTwin();
            string twin_Id = "myRoomId";
            twin.Metadata = new DigitalTwinMetadata();
            twin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            twin.CustomProperties = props;
            await client.CreateDigitalTwinAsync(twin_Id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
            Console.WriteLine("Twin created successfully");
            Console.WriteLine();

            //Print twin
            Console.WriteLine("--- Printing twin details:");
            twin = FetchAndPrintTwin(twin_Id, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Update twin data
            List<object> twinData = new List<object>();
            twinData.Add(new Dictionary<string, object>() 
            {
                { "op", "add"},
                { "path", "/Temperature"},
                { "value", 25.0}
            });
            await client.UpdateDigitalTwinAsync(twin_Id, JsonSerializer.Serialize(twinData));
            Console.WriteLine("Twin properties updated");
            Console.WriteLine();

            //Print twin again
            Console.WriteLine("--- Printing twin details (after update):");
            FetchAndPrintTwin(twin_Id, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete twin
            await DeleteTwin(client, twin_Id);
        }

        private static BasicDigitalTwin FetchAndPrintTwin(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<string> res = client.GetDigitalTwin(twin_Id);
            twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
            Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
            foreach (string prop in twin.CustomProperties.Keys)
            {
                if (twin.CustomProperties.TryGetValue(prop, out object value))
                    Console.WriteLine($"Property '{prop}': {value}");
            }

            return twin;
        }
        private static async Task DeleteTwin(DigitalTwinsClient client, string id)
        {
            await FindAndDeleteOutgoingRelationshipsAsync(client, id);
            await FindAndDeleteIncomingRelationshipsAsync(client, id);
            try
            {
                await client.DeleteDigitalTwinAsync(id);
                Console.WriteLine("Twin deleted successfully");
            }
            catch (RequestFailedException exc)
            {
                Console.WriteLine($"*** Error:{exc.Message}");
            }
        }

        private static async Task FindAndDeleteOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin

            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);

                await foreach (string relJson in relsJson)
                {
                    var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
                    await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
                    Console.WriteLine($"Deleted relationship {rel.Id} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
            }
        }

       private static async Task FindAndDeleteIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin

            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
                    Console.WriteLine($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
            }
        }

    }
}

```
Här är konsol resultatet av programmet ovan: 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="Konsol resultat som visar att den dubbla skapas, uppdateras och tas bort" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="manage-twins-with-cli"></a>Hantera dubbla med CLI

Uppdelade kan också hanteras med hjälp av Azure Digitals flätade CLI. Du hittar kommandona i [_How-till: Använd Azure Digitals flätat CLI *](how-to-use-cli.md).

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="view-all-digital-twins"></a>Visa alla digitala dubbla

Om du vill visa alla digitala dubbla i din instans använder du en [fråga](how-to-query-graph.md). Du kan köra en fråga med [fråge-API: erna](/rest/api/digital-twins/dataplane/query) eller [CLI-kommandona](how-to-use-cli.md).

Här är bröd texten i den grundläggande frågan som returnerar en lista över alla digitala, dubbla, i instansen:

```sql
SELECT *
FROM DIGITALTWINS
``` 

## <a name="next-steps"></a>Nästa steg

Se hur du skapar och hanterar relationer mellan digitala dubbla:
* [*Anvisningar: hantera den dubbla grafen med relationer*](how-to-manage-graph.md)