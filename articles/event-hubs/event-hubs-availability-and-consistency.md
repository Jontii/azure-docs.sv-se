---
title: Tillgänglighet och konsekvens – Azure Event Hubs | Microsoft Docs
description: Hur du ger maximal tillgänglighet och konsekvens med Azure Event Hubs att använda partitioner.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 774332b8f2d5c336f1a22d717516ae35a62b341f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89000642"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Tillgänglighet och konsekvens i Event Hubs

## <a name="overview"></a>Översikt
Azure Event Hubs använder en [partitionerings modell](event-hubs-scalability.md#partitions) för att förbättra tillgänglighets-och parallellisering i en enskild händelsehubben. Om en händelsehubben till exempel har fyra partitioner, och en av dessa partitioner flyttas från en server till en annan i en belastnings Utjämnings åtgärd, kan du fortfarande skicka och ta emot från tre andra partitioner. Med fler partitioner kan du dessutom låta fler samtidiga läsare bearbeta dina data och förbättra det sammanställda data flödet. Att förstå konsekvenserna av partitionering och beställning i ett distribuerat system är en viktig aspekt av lösnings designen.

För att hjälpa till att förklara kompromisser mellan beställningar och tillgänglighet, se [Cap-satsen](https://en.wikipedia.org/wiki/CAP_theorem), även kallat bryggeriens satsen. I den här satsen diskuteras valet mellan konsekvens, tillgänglighet och partitions tolerans. Det anger att för de system som är partitionerade efter nätverk alltid kompromisser mellan konsekvens och tillgänglighet.

I bryggeriens satsen definieras konsekvens och tillgänglighet enligt följande:
* Partitions tolerans: data bearbetnings systemets möjlighet att fortsätta att bearbeta data även om ett partitionsschema inträffar.
* Tillgänglighet: en icke-misslyckad nod returnerar ett rimligt svar inom en rimlig tids period (utan fel eller tids gränser).
* Konsekvens: en läsning garanterar att returnera den senaste skrivningen för en specifik klient.

## <a name="partition-tolerance"></a>Partitions tolerans
Event Hubs skapas ovanpå en partitionerad data modell. Du kan konfigurera antalet partitioner i din Event Hub under installationen, men du kan inte ändra det här värdet senare. Eftersom du måste använda partitioner med Event Hubs måste du fatta ett beslut om tillgänglighet och konsekvens för ditt program.

## <a name="availability"></a>Tillgänglighet
Det enklaste sättet att komma igång med Event Hubs är att använda standard beteendet. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 eller senare)](#tab/latest)
Om du skapar ett nytt **[EventHubProducerClient](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient?view=azure-dotnet)** -objekt och använder **[SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync?view=azure-dotnet)** -metoden distribueras händelserna automatiskt mellan partitioner i händelsehubben. Det här beteendet gör det möjligt för den största delen av tiden.

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 eller tidigare)](#tab/old)
Om du skapar ett nytt **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** -objekt och använder **[send](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** -metoden, distribueras händelserna automatiskt mellan partitioner i händelsehubben. Det här beteendet gör det möjligt för den största delen av tiden.

---

Den här modellen rekommenderas för användnings fall som kräver den maximala tiden.

## <a name="consistency"></a>Konsekvens
I vissa fall kan sortering av händelser vara viktigt. Till exempel kanske du vill att Server dels systemet ska bearbeta ett uppdaterings kommando före kommandot DELETE. I den här instansen kan du antingen ange partitionsnyckel för en händelse eller använda ett `PartitionSender` objekt (om du använder det gamla Microsoft. Azure. Messaging-biblioteket) för att endast skicka händelser till en viss partition. Detta säkerställer att när dessa händelser läses från partitionen, läses de in i ordning. Om du använder **Azure. Messaging. EventHubs** -biblioteket och mer information finns i [migrera kod från PartitionSender till EventHubProducerClient för publicering av händelser till en partition](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition).

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 eller senare)](#tab/latest)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    var batchOptions = new CreateBatchOptions() { PartitionId = "my-partition-id" };
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync(batchOptions);
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));
    
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 eller tidigare)](#tab/old)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(connectionString){ EntityPath = eventHubName }; 
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
PartitionSender partitionSender = eventHubClient.CreatePartitionSender("my-partition-id");
try
{
    EventDataBatch eventBatch = partitionSender.CreateBatch();
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));

    await partitionSender.SendAsync(eventBatch);
}
finally
{
    await partitionSender.CloseAsync();
    await eventHubClient.CloseAsync();
}
```

---

I den här konfigurationen ska du tänka på att om den partition som du skickar till inte är tillgänglig får du ett fel meddelande. Som jämförelse punkt, om du inte har en tillhörighet till en enda partition, skickar Event Hubs tjänsten din händelse till nästa tillgängliga partition.

En möjlig lösning för att säkerställa sortering, samtidigt som den maximerar tiden, är att aggregera händelser som en del av ditt händelse bearbetnings program. Det enklaste sättet att göra detta är att stämpla din händelse med en anpassad sekvens nummer egenskap. Följande kod visar ett exempel:

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 eller senare)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 eller tidigare)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

Det här exemplet skickar din händelse till en av de tillgängliga partitionerna i händelsehubben och anger motsvarande ordnings nummer från ditt program. Den här lösningen kräver tillstånd att behållas av ditt bearbetnings program, men ger dina avsändare en slut punkt som är mer sannolik tillgänglig.

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs tjänsten](./event-hubs-about.md)
* [Skapa en händelsehubb](event-hubs-create.md)
