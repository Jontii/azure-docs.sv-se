---
title: Referens för Azure Functions C#-utvecklare
description: Lär dig hur du utvecklar Azure Functions med C#.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/24/2020
ms.openlocfilehash: 23b0961c369c21f50d9a873678a1c910385e6a91
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88206212"
---
# <a name="azure-functions-c-developer-reference"></a>Referens för Azure Functions C#-utvecklare

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Den här artikeln är en introduktion till att utveckla Azure Functions med hjälp av C# i .NET-klass bibliotek.

Azure Functions stöder C#-och C#-skript programmeringsspråk. Om du vill ha vägledning om hur du [använder c# i Azure Portal](functions-create-function-app-portal.md), se [c#-skript (. CSX) som utvecklar referens](functions-reference-csharp.md).

Den här artikeln förutsätter att du redan har läst följande artiklar:

* [Guide för Azure Functions utvecklare](functions-reference.md)
* [Azure Functions Visual Studio 2019-verktyg](functions-develop-vs.md)

## <a name="supported-versions"></a>Versioner som stöds

Versioner av Functions runtime fungerar med vissa versioner av .NET. I följande tabell visas den högsta nivån av .NET Core och .NET Framework och .NET Core som kan användas med en speciell version av Functions i projektet. 

| Functions runtime-version | Högsta .NET-version |
| ---- | ---- |
| Functions 3. x | .NET Core 3,1 |
| Functions 2.x | .NET Core 2.2 |
| Functions 1.x | .NET Framework 4,7 |

Mer information finns i [Översikt över Azure Functions körnings versioner](functions-versions.md)

## <a name="functions-class-library-project"></a>Funktions klass biblioteks projekt

I Visual Studio skapar **Azure Functions** projekt mal len ett C#-klass biblioteks projekt som innehåller följande filer:

* [host.jspå](functions-host-json.md) lagrar konfigurations inställningar som påverkar alla funktioner i projektet när de körs lokalt eller i Azure.
* [local.settings.js](functions-run-local.md#local-settings-file) app-inställningar och anslutnings strängar som används när du kör lokalt. Den här filen innehåller hemligheter och publiceras inte i din Function-app i Azure. Lägg i stället [till appinställningar i din Function-app](functions-develop-vs.md#function-app-settings).

När du skapar projektet genereras en mappstruktur som ser ut som i följande exempel i skapa utdata-katalogen:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Den här katalogen är vad som distribueras till din Function-app i Azure. De bindnings tillägg som krävs i [version 2. x](functions-versions.md) i functions-körningen [läggs till i projektet som NuGet-paket](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> Build-processen skapar en *function.jspå* en fil för varje funktion. Den här *function.jspå* filen är inte avsedd att redige ras direkt. Du kan inte ändra bindnings konfigurationen eller inaktivera funktionen genom att redigera den här filen. Information om hur du inaktiverar en funktion finns i [så här inaktiverar du funktioner](disable-function.md).


## <a name="methods-recognized-as-functions"></a>Metoder som identifieras som funktioner

I ett klass bibliotek är en funktion en statisk metod med ett `FunctionName` och ett Utlös ande attribut, som visas i följande exempel:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

`FunctionName`Attributet markerar metoden som en funktions start punkt. Namnet måste vara unikt inom ett projekt, börja med en bokstav och får bara innehålla bokstäver, siffror, `_` och `-` upp till 127 tecken. I Project-mallar skapas ofta en metod med namnet `Run` , men metod namnet kan vara ett giltigt C#-metod namn.

Attributet trigger anger utlösarens typ och binder indata till en metod parameter. Exempel funktionen utlöses av ett köat meddelande, och Queue-meddelandet skickas till-metoden i- `myQueueItem` parametern.

## <a name="method-signature-parameters"></a>Parametrar för metodsignatur

Metodsignaturen kan innehålla andra parametrar än den som används med Utlösar-attributet. Här följer några av de ytterligare parametrar som du kan inkludera:

* [In-och utdata-bindningar](functions-triggers-bindings.md) som marker ATS som sådana genom att dekorera dem med attribut.  
* En `ILogger` eller `TraceWriter` ([version 1. x-](functions-versions.md#creating-1x-apps)parameter) för [loggning](#logging).
* En `CancellationToken` parameter för en [korrekt avstängning](#cancellation-tokens).
* [Bindnings uttryck](./functions-bindings-expressions-patterns.md) parametrar för att hämta metadata för utlösare.

Ordningen på parametrarna i funktions signaturen spelar ingen roll. Du kan till exempel ange utlösarens parametrar före eller efter andra bindningar, och du kan ange parametern för loggning före eller efter utlösare eller bindnings parametrar.

### <a name="output-binding-example"></a>Exempel på utgående bindning

I följande exempel ändras föregående, genom att lägga till en bindning för utgående köer. Funktionen skriver det köa meddelande som utlöser funktionen till ett nytt Queue-meddelande i en annan kö.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

I referens artiklarna för bindning (till exempel[lagrings köer](functions-bindings-storage-queue.md)) förklaras vilka parameter typer du kan använda med attributen utlösare, indata eller utgående bindning.

### <a name="binding-expressions-example"></a>Exempel på bindnings uttryck

Följande kod hämtar namnet på kön som ska övervakas från en app-inställning och den hämtar tiden för att skapa Queue-meddelande i `insertionTime` parametern.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Automatiskt genererad function.jspå

Build-processen skapar en *function.jspå* en fil i en Function-mapp i build-mappen. Som tidigare nämnts är den här filen inte avsedd att redige ras direkt. Du kan inte ändra bindnings konfigurationen eller inaktivera funktionen genom att redigera den här filen. 

Syftet med den här filen är att tillhandahålla information till den skalnings styrenhet som ska användas för att [skala beslut i förbruknings planen](functions-scale.md#how-the-consumption-and-premium-plans-work). Därför har filen bara utlösarens information, inte indata eller utdata-bindningar.

Den genererade *function.jsi* filen innehåller en `configurationSource` egenskap som instruerar körningen att använda .net-attribut för bindningar i stället för att *function.jsi* konfigurationen. Här är ett exempel:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft. NET. SDK. Functions

*function.js* av filgenerering utförs av NuGet-paketet [Microsoft \. net \. SDK \. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Samma paket används för både version 1. x och 2. x i functions-körningen. Mål ramverket är det som skiljer ett 1. x-projekt från ett 2. x-projekt. Här följer de relevanta delarna av *. CSPROJ* -filer som visar olika mål ramverk och samma `Sdk` paket:

**Functions 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Functions 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Bland `Sdk` paket beroenden är utlösare och bindningar. Ett 1. x-projekt refererar till 1. x-utlösare och bindningar eftersom dessa utlösare och bindningar är riktade mot .NET Framework, medan 2. x utlöser och binder till målets .NET Core.

`Sdk`Paketet är också beroende av [Newtonsoft.Jspå](https://www.nuget.org/packages/Newtonsoft.Json)och indirekt på [windowsazure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Dessa beroenden ser till att ditt projekt använder de versioner av de paketen som fungerar med funktionernas kör tids version som projektets mål. Har till exempel `Newtonsoft.Json` version 11 för .NET Framework 4.6.1, men Functions-körningen som är mål .NET Framework 4.6.1 är bara kompatibel med `Newtonsoft.Json` 9.0.1. Därför behöver din funktions kod i projektet även använda `Newtonsoft.Json` 9.0.1.

Käll koden för `Microsoft.NET.Sdk.Functions` är tillgänglig i GitHub lagrings platsen [Azure \- Functions \- vs \- build \- SDK](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Körnings version

I Visual Studio används [Azure Functions Core tools](functions-run-local.md#install-the-azure-functions-core-tools) för att köra Functions-projekt. Kärn verktygen är ett kommando rads gränssnitt för functions-körningen.

Om du installerar kärn verktygen med NPM, som inte påverkar den version av kärn verktyg som används av Visual Studio. För functions runtime version 1. x, lagrar Visual Studio kärn verktyg i *%USERPROFILE%\AppData\Local\Azure.functions.CLI* och använder den senaste versionen som lagras där. För funktioner 2. x ingår kärn verktygen i tillägget **Azure Functions och webb jobb verktyg** . För både 1. x och 2. x kan du se vilken version som används i konsolens utdata när du kör ett Functions-projekt:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="readytorun"></a>ReadyToRun

Du kan kompilera din Function-app som [ReadyToRun-binärfiler](/dotnet/core/whats-new/dotnet-core-3-0#readytorun-images). ReadyToRun är en form av för hands kompilering som kan förbättra start prestandan för att minska effekten av [kall start](functions-scale.md#cold-start) när den körs i en [förbruknings plan](functions-scale.md#consumption-plan).

ReadyToRun är tillgängligt i .NET 3,0 och kräver [version 3,0 av Azure Functions runtime](functions-versions.md).

Om du vill kompilera projektet som ReadyToRun uppdaterar du projekt filen genom att lägga `<PublishReadyToRun>` till `<RuntimeIdentifier>` elementen och. Följande är konfigurationen för att publicera till en Windows 32-bitars Function-app.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.1</TargetFramework>
  <AzureFunctionsVersion>v3</AzureFunctionsVersion>
  <PublishReadyToRun>true</PublishReadyToRun>
  <RuntimeIdentifier>win-x86</RuntimeIdentifier>
</PropertyGroup>
```

> [!IMPORTANT]
> ReadyToRun stöder för närvarande inte Cross-Compilation. Du måste bygga din app på samma plattform som distributions målet. Observera också att "bitness" är konfigurerat i din Function-app. Om din Function-app i Azure exempelvis är Windows 64-bitars, måste du kompilera din app i Windows med `win-x64` som [körnings-ID](/dotnet/core/rid-catalog).

Du kan också bygga din app med ReadyToRun från kommando raden. Mer information finns i `-p:PublishReadyToRun=true` alternativet i [`dotnet publish`](/dotnet/core/tools/dotnet-publish) .

## <a name="supported-types-for-bindings"></a>Typer som stöds för bindningar

Varje bindning har sina egna typer som stöds. ett BLOB-Utlösar-attribut kan till exempel användas för en sträng parameter, en POCO-parameter, en `CloudBlockBlob` parameter eller någon av flera andra typer som stöds. [Bindnings referens artikeln för BLOB-bindningar](functions-bindings-storage-blob-trigger.md#usage) visar alla parameter typer som stöds. Mer information finns i [utlösare och bindningar](functions-triggers-bindings.md) och [bindnings referens dokument för varje bindnings typ](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Bindning till metod retur värde

Du kan använda ett metod retur värde för en utgående bindning genom att tillämpa attributet på metodens retur värde. Exempel finns i [utlösare och bindningar](./functions-bindings-return-value.md). 

Använd returvärdet endast om en lyckad funktions körning alltid resulterar i ett retur värde som skickas till utgående bindning. Annars använder `ICollector` eller `IAsyncCollector` , som du ser i följande avsnitt.

## <a name="writing-multiple-output-values"></a>Skriva flera värden för utdata

Om du vill skriva flera värden till en utgående bindning, eller om ett lyckat funktions anrop kanske inte leder till att något skickas till utgående bindning, använder [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) du [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typerna eller. Dessa typer är skrivskyddade samlingar som skrivs till utgående bindning när metoden har slutförts.

I det här exemplet skrivs flera meddelanden i kön till samma kö med `ICollector` :

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Loggning

Om du vill logga utdata till dina strömmande loggar i C# inkluderar du ett argument av typen [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger). Vi rekommenderar att du namnger den `log` , som i följande exempel:  

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Undvik att använda `Console.Write` i Azure Functions. Mer information finns i [skriva loggar i C#-funktioner](functions-monitoring.md#write-logs-in-c-functions) i **övervakaren Azure Functions** artikel.

## <a name="async"></a>Async

Om du vill göra en funktion [asynkron](/dotnet/csharp/programming-guide/concepts/async/)använder du `async` nyckelordet och returnerar ett `Task` objekt.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

Du kan inte använda `out` parametrar i async functions. För utgående bindningar använder du [funktionen returnera värde](#binding-to-method-return-value) eller ett [insamlat objekt](#writing-multiple-output-values) i stället.

## <a name="cancellation-tokens"></a>Token för avbrytande

En funktion kan ta emot en [CancellationToken](/dotnet/api/system.threading.cancellationtoken) -parameter som gör att operativ systemet kan meddela din kod när funktionen ska avslutas. Du kan använda det här meddelandet för att se till att funktionen inte avslutas plötsligt på ett sätt som lämnar data i ett inkonsekvent tillstånd.

I följande exempel visas hur du söker efter förestående funktions avslutning.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="environment-variables"></a>Miljövariabler

Använd `System.Environment.GetEnvironmentVariable` , som du ser i följande kod exempel, för att hämta en miljö variabel eller ett inställnings värde för appar:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
    {
        log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
        log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

App-inställningar kan läsas från miljövariabler både när de utvecklas lokalt och när de körs i Azure. När du utvecklar lokalt hämtas app-inställningarna från `Values` samlingen i *local.settings.js* filen. I båda miljöerna, lokalt och Azure, `GetEnvironmentVariable("<app setting name>")` hämtas värdet för den namngivna appens inställningen. När du till exempel kör lokalt returneras "mitt webbplats namn" om *local.settings.jsi* filen innehåller `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }` .

Egenskapen [System.Configuration.ConfigurationManager. appSettings](/dotnet/api/system.configuration.configurationmanager.appsettings) är ett alternativt API för att hämta inställnings värden för appar, men vi rekommenderar att du använder `GetEnvironmentVariable` som visas här.

## <a name="binding-at-runtime"></a>Bindning vid körning

I C# och andra .NET-språk kan du använda ett [tvingande](https://en.wikipedia.org/wiki/Imperative_programming) bindnings mönster, till skillnad från [*deklarativ*](https://en.wikipedia.org/wiki/Declarative_programming) bindningar i attribut. Tvingande bindning är användbart när bindnings parametrar måste beräknas vid körning i stället för design tid. Med det här mönstret kan du binda till indata-och utgående bindningar som stöds i-farten i funktions koden.

Definiera en tvingande bindning enligt följande:

- Inkludera **inte** ett attribut i funktions under skriften för dina önskade tvingande bindningar.
- Skicka in en indataparameter [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) eller [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) .
- Använd följande C#-mönster för att utföra data bindningen.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` är .NET-attributet som definierar bindningen och `T` är en indata-eller utdatatyp som stöds av den bindnings typen. `T` kan inte vara en `out` parameter typ (till exempel `out JObject` ). Till exempel stöder Mobile Apps tabellens utgående bindning [sex typer av utdata](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), men du kan bara använda [ICollector \<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) eller [IAsyncCollector \<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) med tvingande bindning.

### <a name="single-attribute-example"></a>Exempel på ett attribut

Följande exempel kod skapar en [utgående bindning för Storage BLOB](functions-bindings-storage-blob-output.md) med en BLOB-sökväg som definieras vid körning och skriver sedan en sträng till blobben.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definierar [lagrings-BLOB](functions-bindings-storage-blob.md) -indata eller utdata-bindningen och [TextWriter](/dotnet/api/system.io.textwriter) är en typ av utgående bindning som stöds.

### <a name="multiple-attribute-example"></a>Exempel på flera attribut

I föregående exempel hämtas app-inställningen för funktions programmets huvud anslutnings sträng för lagrings konto (som är `AzureWebJobsStorage` ). Du kan ange en anpassad app-inställning som ska användas för lagrings kontot genom att lägga till [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) och skicka attributhierarkin till `BindAsync<T>()` . Använd en `Binder` parameter, inte `IBinder` .  Exempel:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Utlösare och bindningar 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om utlösare och bindningar](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Läs mer om metod tips för Azure Functions](functions-best-practices.md)
