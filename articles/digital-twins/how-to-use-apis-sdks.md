---
title: Använda Azure Digital Twins-API:er och -SDK:er
titleSuffix: Azure Digital Twins
description: 'Se hur du arbetar med Azure Digitals dubbla API: er, inklusive via SDK.'
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1cf7bd744870b9f0a04d63445268b2ccd3134a66
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046765"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Använda Azure Digital Twins-API:er och -SDK:er

Azure Digitals dubbla är utrustade med både **kontroll Plans-API: er** och **data Plans-API: er** för att hantera din instans och dess element. 
* API: erna för kontroll plan är [Azure Resource Manager (arm)](../azure-resource-manager/management/overview.md) API: er, och de kan hantera resurs hanterings åtgärder som att skapa och ta bort din instans. 
* Data Plans-API: erna är Azure Digitals dubbla API: er och används för data hanterings åtgärder som att hantera modeller, dubbla och diagrammet.

Den här artikeln ger en översikt över de API: er som är tillgängliga och metoderna för att interagera med dem. Du kan antingen använda REST-API: er direkt med deras associerade swaggers eller via en SDK.

## <a name="overview-control-plane-apis"></a>Översikt: kontroll Plans-API: er

API: er för kontroll plan är [arm](../azure-resource-manager/management/overview.md) -API: er som används för att hantera din Azure Digital-instansen som helhet, så att de täcker åtgärder som att skapa eller ta bort hela instansen. Du kan också använda dessa för att skapa och ta bort slut punkter.

Den mest aktuella API-versionen för kontroll plan för den offentliga för hands versionen är _**2020-03-01 – för hands**_ version.

Använda API: er för kontroll plan:
* Du kan anropa API: erna direkt genom att referera till de senaste Swagger i [mappen Control plan Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins). Den här lagrings platsen innehåller också en mapp med exempel som visar användningen.
* Du kan för närvarande komma åt SDK: er för kontroll-API: er i...
  - [.Net (C#)](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/1.0.0-preview.1) ([källa](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins)) ([referens [genereras automatiskt]](/dotnet/api/overview/azure/digitaltwins/management?preserve-view=true&view=azure-dotnet-preview))
  - [Java](https://search.maven.org/artifact/com.microsoft.azure.digitaltwins.v2020_03_01_preview/azure-mgmt-digitaltwins) ([källa](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins)) ([referens [automatiskt genererad]](/java/api/overview/azure/digitaltwins/management?preserve-view=true&view=azure-java-preview))
  - [Java Script](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([källa](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [Python](https://pypi.org/project/azure-mgmt-digitaltwins/) ([källa](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [Go-source](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/digitaltwins/mgmt/2020-03-01-preview/digitaltwins)

Du kan också öva på att kontrol lera plan-API: er genom att interagera med Azure Digitals sammanflätar via [Azure Portal](https://portal.azure.com) och [CLI](how-to-use-cli.md).

## <a name="overview-data-plane-apis"></a>Översikt: data Plans-API: er

Data Plans-API: erna är Azure Digitals dubbla API: er som används för att hantera elementen i Azure Digitals-instansen. De innehåller åtgärder som att skapa vägar, överföra modeller, skapa relationer och hantera dubbla. De kan delas i stor mängd i följande kategorier:
* **DigitalTwinsModels** – DigitalTwinsModels-kategorin innehåller API: er för att hantera [modeller](concepts-models.md) i en digital Azure-instans. Hanterings aktiviteter omfattar uppladdning, validering, hämtning och borttagning av modeller som skapats i DTDL.
* **DigitalTwins** – DigitalTwins-kategorin innehåller de API: er som utvecklare kan använda för att skapa, ändra och ta bort [digitala dubbla](concepts-twins-graph.md) och deras relationer i en digital Azure-instans.
* **Fråga** – kategorin fråga gör det möjligt [för utvecklare att hitta uppsättningar digitala delar i det dubbla diagrammet](how-to-query-graph.md) mellan relationer.
* **EventRoutes** – EventRoutes-kategorin innehåller API: er för att [dirigera data](concepts-route-events.md)genom systemet och till underordnade tjänster.

Den mest aktuella data planet API-versionen för den offentliga för hands versionen är _**2020-05-31 – för hands**_ version. API _-versionen 2020-03-01-Preview_ för data Plans åtgärder har nu blivit föråldrad.

Använda data Plans-API: er:
* Du kan anropa API: erna direkt, efter...
   - referera till de senaste Swagger i [mappen dataplan Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Den här lagrings platsen innehåller också en mapp med exempel som visar användningen. 
   - Visa [API Reference-dokumentationen](/rest/api/azure-digitaltwins/).
* Du kan använda **.net (C#)** SDK. För att använda .NET SDK...
   - Du kan visa och lägga till paketet från NuGet: [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - Du kan hitta SDK-källan, inklusive en mapp med exempel, i GitHub: [Azure IoT Digitals klient bibliotek för .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - Du kan visa [SDK Reference-dokumentationen](/dotnet/api/overview/azure/digitaltwins?preserve-view=true&view=azure-dotnet-preview).
   - Du kan se detaljerade informations-och användnings exempel genom att gå vidare till avsnittet [.net (C#) SDK (data plan)](#net-c-sdk-data-plane) i den här artikeln.
* Du kan använda **Java Script** SDK. För att använda JavaScript SDK...
   - Du kan visa och installera paketet från NPM: [Azure Azure Digitals dubbla klient bibliotek för Java Script](https://www.npmjs.com/package/@azure/digital-twins/v/1.0.0-preview.1).
   - Du kan visa [SDK Reference-dokumentationen](/javascript/api/@azure/digital-twins/?preserve-view=true&view=azure-node-latest).
* Du kan använda **Java** SDK. För att använda Java SDK...
   - Du kan visa och installera paketet från maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0-beta.1/jar)
   - Du kan visa [SDK Reference-dokumentationen](/java/api/overview/azure/digitaltwins/client?preserve-view=true&view=azure-java-preview)
* Du kan skapa ett SDK för ett annat språk med hjälp av AutoRest. Följ instruktionerna i [*instruktion: skapa anpassade SDK: er för Azure Digitals med AutoRest*](how-to-create-custom-sdks.md).

Du kan också utföra API: er för datum plan genom att interagera med Azure Digitals flätar via [CLI](how-to-use-cli.md).

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (data plan)

Azure Digitals .NET (C#) SDK är en del av Azure SDK för .NET. Det är öppen källkod och baseras på Azures digitala dubbla data Plans-API: er.

> [!NOTE]
> Mer information om SDK-design finns i allmänna [design principer för Azure SDK](https://azure.github.io/azure-sdk/general_introduction.html) : er och de aktuella [.net-design rikt linjerna](https://azure.github.io/azure-sdk/dotnet_introduction.html).

Om du vill använda SDK inkluderar du NuGet-paketet **Azure. DigitalTwins. Core** med ditt projekt. Du kommer också att behöva den senaste versionen av **Azure. Identity** -paketet.

* I Visual Studio kan du lägga till paket med NuGet Package Manager (nås via *verktyg > NuGet package manager > hantera NuGet-paket för lösningen*). 
* Med kommando rads verktyget .NET kan du köra:

    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

En detaljerad genom gång av hur du använder API: erna i praktiken finns i [*självstudien: kod a klient program*](tutorial-code.md). 

### <a name="net-sdk-usage-examples"></a>Användnings exempel för .NET SDK

Här följer några kod exempel som illustrerar användningen av .NET SDK.

Autentisera mot tjänsten:

```csharp
// Authenticate against the service and create a client
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
```

Ladda upp en modell-och list modeller:

```csharp
// Upload a model
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
// Read a list of models back from the service
AsyncPageable<DigitalTwinsModelData> modelDataList = client.GetModelsAsync();
await foreach (DigitalTwinsModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Skapa och fråga dubbla:

```csharp
// Initialize twin metadata
BasicDigitalTwin twinData = new BasicDigitalTwin();

twinData.Id = $"firstTwin";
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", "Hello World!");
try {
    await client.CreateDigitalTwinAsync("firstTwin", JsonSerializer.Serialize(twinData));
} catch(RequestFailedException rex) {
    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
}
 
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    // Use JSON deserialization to pretty-print
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    // Or use BasicDigitalTwin for convenient property access
    BasicDigitalTwin btwin = JsonSerializer.Deserialize<BasicDigitalTwin>(twin);
}
```

Se [*självstudien: koda en klient app*](tutorial-code.md) för en genom gång av den här exempel koden. 

Du kan också hitta ytterligare exempel i [GitHub-lagrings platsen för .net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples).

#### <a name="serialization-helpers"></a>Hjälp program för serialisering

Hjälp för serialisering är hjälp funktioner som är tillgängliga i SDK för att snabbt skapa eller avserialisera dubbla data för åtkomst till grundläggande information. Eftersom Core SDK-metoderna returnerar dubbla data som JSON som standard, kan det vara bra att använda dessa hjälp klasser för att dela upp de dubbla data nedåt.

Tillgängliga hjälp klasser är:
* `BasicDigitalTwin`: Representerar huvud data för en digital, dubbel
* `BasicRelationship`: Representerar huvud data för en relation
* `UpdateOperationUtility`: Representerar JSON-patch-information som används i uppdaterings anrop
* `WriteableProperty`: Representerar metadata för egenskapen

##### <a name="deserialize-a-digital-twin"></a>Deserialisera en digital, dubbel

Du kan alltid deserialisera dubbla data med valfritt JSON-bibliotek, till exempel `System.Test.Json` eller `Newtonsoft.Json` . För grundläggande åtkomst till en enhet gör hjälp klasserna detta lite bekvämare.

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
```

`BasicDigitalTwin`Klassen Helper ger dig också åtkomst till egenskaper som definierats i den dubbla, via a `Dictionary<string, object>` . Om du vill visa egenskaperna för den dubbla kan du använda:

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-digital-twin"></a>Skapa en digital, dubbel

Med hjälp av `BasicDigitalTwin` klassen kan du förbereda data för att skapa en dubbel instans:

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

Koden ovan motsvarar följande "Manuell" variant:

```csharp
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:example:Room;1"}
};
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", 25.0 }
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

##### <a name="deserialize-a-relationship"></a>Deserialisera en relation

Du kan alltid deserialisera Relations data med valfritt JSON-bibliotek, till exempel `System.Test.Json` eller `Newtonsoft.Json` . För grundläggande åtkomst till en relation gör hjälp klasserna detta lite bekvämare.

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
```

`BasicRelationship`Hjälp klassen ger också åtkomst till egenskaper som definierats i relationen, via en `Dictionary<string, object>` . Om du vill visa egenskaper kan du använda:

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
foreach (string prop in rel.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-relationship"></a>Skapa en relation

Med hjälp av `BasicRelationship` klassen kan du också förbereda data för att skapa relationer på en delad instans:

```csharp
BasicRelationship rel = new BasicRelationship();
rel.TargetId = "myTargetTwin";
rel.Name = "contains"; // a relationship with this name must be defined in the model
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("active", true);
rel.CustomProperties = props;
client.CreateRelationship("mySourceTwin", "rel001", JsonSerializer.Serialize<BasicRelationship>(rel));
```

##### <a name="create-a-patch-for-twin-update"></a>Skapa en korrigering för dubbel uppdatering

Uppdatera anrop för dubbla och relationer Använd [JSON-patch](http://jsonpatch.com/) -struktur. Om du vill skapa listor över JSON-korrigeringsfiler kan du använda- `UpdateOperationsUtility` klassen som visas nedan.

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
uou.AppendAddOp("/myComponent/Property", "Hello");
// Un-set a property
uou.AppendRemoveOp("/Humidity");
client.UpdateDigitalTwin("myTwin", uou.Serialize());
```

## <a name="general-apisdk-usage-notes"></a>Allmän information om API/SDK-användning

> [!NOTE]
> Observera att Azure Digital-dubbla är för närvarande inte stöder **CORS (Cross-Origin resurs delning)**. Mer information om påverkan och lösnings strategier finns i avsnittet om [*resurs delning mellan ursprung (CORS)*](concepts-security.md#cross-origin-resource-sharing-cors) i *begrepp: säkerhet för Azure Digitals dubbla lösningar*.

Följande lista innehåller ytterligare information och allmänna rikt linjer för att använda API: er och SDK: er.

* Om du vill använda SDK instansierar du `DigitalTwinsClient` klassen. Konstruktorn kräver autentiseringsuppgifter som kan erhållas med en rad autentiseringsmetoder i `Azure.Identity` paketet. Mer `Azure.Identity` information finns i dokumentationen för [namn området](/dotnet/api/azure.identity?preserve-view=true&view=azure-dotnet). 
* Du kan `InteractiveBrowserCredential` komma igång med att komma igång, men det finns flera andra alternativ, inklusive autentiseringsuppgifter för [hanterad identitet](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet), som du kommer att använda för att autentisera [Azure Functions som är inställda med MSI](../app-service/overview-managed-identity.md?tabs=dotnet) mot digitala Azure-dubbla. Mer information `InteractiveBrowserCredential` finns i [klass dokumentationen](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet).
* Alla API-anrop visas som medlems funktioner i `DigitalTwinsClient` klassen.
* Alla tjänst funktioner finns i synkrona och asynkrona versioner.
* Alla tjänst funktioner genererar ett undantag för retur status på 400 eller senare. Se till att du omsluter anrop till ett `try` avsnitt och fånga minst `RequestFailedExceptions` . Mer information om den här typen av undantag finns [här](/dotnet/api/azure.requestfailedexception?preserve-view=true&view=azure-dotnet).
* De flesta service metoder returnerar `Response<T>` eller ( `Task<Response<T>>` för asynkrona anrop), där `T` är klassen för returnerat objekt för tjänst anropet. [`Response`](/dotnet/api/azure.response-1?preserve-view=true&view=azure-dotnet)Klassen kapslar in tjänsten Return och visar retur värden i sitt `Value` fält.  
* Tjänst metoder med växlade resultat returnerar `Pageable<T>` eller `AsyncPageable<T>` som resultat. Mer information om `Pageable<T>` klassen finns [här](/dotnet/api/azure.pageable-1?preserve-view=true&view=azure-dotnet-preview). mer information `AsyncPageable<T>` finns [här](/dotnet/api/azure.asyncpageable-1?preserve-view=true&view=azure-dotnet-preview).
* Du kan iterera över växlade resultat med en `await foreach` loop. Mer information om den här processen finns [här](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* Den underliggande SDK: n är `Azure.Core` . I [dokumentationen för Azure namespace](/dotnet/api/azure?preserve-view=true&view=azure-dotnet-preview) finns information om SDK-infrastruktur och-typer.

Tjänst metoder returnerar starkt skrivna objekt när det är möjligt. Men eftersom Azure Digital-dubbla är baserade på modeller som anpassas av användaren vid körning (via DTDL-modeller som har överförts till tjänsten), tar många tjänst-API: er att ta och returnera dubbla data i JSON-format.

## <a name="monitor-api-metrics"></a>Övervaka API-mått

API-mått som förfrågningar, svars tid och felgrader kan visas i [Azure Portal](https://portal.azure.com/). 

Från portalens start sida söker du efter din Azure Digital-instansen för att hämta information. Välj alternativet **mått** från Azure Digitals instansen meny för att öppna sidan *mått* .

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Skärm bild som visar mått sidan för Azure Digitals dubbla":::

Härifrån kan du visa måtten för din instans och skapa anpassade vyer.

## <a name="next-steps"></a>Nästa steg

Se hur du använder API: erna för att konfigurera en digital Azure-instans och autentisering:
* [*Anvisningar: Konfigurera en instans och autentisering*](how-to-set-up-instance-portal.md)

Du kan också gå igenom stegen för att skapa en klient app som den som används i den här instruktionen:
* [*Självstudie: koda en klient app*](tutorial-code.md)