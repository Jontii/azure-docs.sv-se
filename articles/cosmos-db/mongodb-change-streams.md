---
title: Ändra strömmar i Azure Cosmos DBs API för MongoDB
description: Lär dig hur du använder ändrings strömmar n Azure Cosmos DBs API för MongoDB för att hämta de ändringar som gjorts i dina data.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/04/2020
ms.author: rosouz
ms.custom: devx-track-javascript
ms.openlocfilehash: b5bf7cc74a5444e5f51aaddb1d088f6b0c1e52a8
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798898"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Ändra strömmar i Azure Cosmos DBs API för MongoDB

[Ändra feed](change-feed.md) -stöd i Azure Cosmos DBS API för MongoDB är tillgängligt med hjälp av API: et för ändrings strömmar. Med hjälp av API: et för ändrings strömmar kan dina program få ändringarna som gjorts i samlingen eller till objekten i en enda Shard. Senare kan du vidta ytterligare åtgärder baserat på resultaten. Ändringar av objekt i samlingen samlas i ordningen av deras ändrings tid och sorterings ordningen garanteras per Shard nyckel.

> [!NOTE]
> Om du vill använda ändrings strömmar skapar du kontot med version 3,6 av Azure Cosmos DB s API för MongoDB eller en senare version. Om du kör exemplen för ändrings data strömmar mot en tidigare version kan du se `Unrecognized pipeline stage name: $changeStream` felet.

## <a name="examples"></a>Exempel

I följande exempel visas hur du hämtar ändrings strömmar för alla objekt i samlingen. I det här exemplet skapas en markör som tittar på objekt när de infogas, uppdateras eller ersätts. `$match`Stage, `$project` Stage och `fullDocument` option krävs för att hämta ändrings strömmar. Det finns för närvarande inte stöd för att titta efter borttagnings åtgärder med ändrings strömmar. Som en lösning kan du lägga till en mjuk markör för de objekt som tas bort. Du kan till exempel lägga till ett attribut i objektet som kallas "borttaget". När du vill ta bort objektet kan du ange "borttagen" `true` och ange ett TTL-värde för objektet. Eftersom uppdatering av borttaget till `true` är en uppdatering visas den här ändringen i ändrings data strömmen.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas hur du använder funktionen för att ändra ström i Java, för det fullständiga exemplet, se den här [GitHub-lagrings platsen](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-changestream/blob/master/mongostream/src/main/java/com/azure/cosmos/mongostream/App.java). I det här exemplet visas även hur du använder- `resumeAfter` metoden för att söka efter alla ändringar från senaste läsning. 

```java
Bson match = Aggregates.match(Filters.in("operationType", asList("update", "replace", "insert")));

// Pick the field you are most interested in
Bson project = Aggregates.project(fields(include("_id", "ns", "documentKey", "fullDocument")));

// This variable is for second example
BsonDocument resumeToken = null;

// Now time to build the pipeline
List<Bson> pipeline = Arrays.asList(match, project);

//#1 Simple example to seek changes

// Create cursor with update_lookup
MongoChangeStreamCursor<ChangeStreamDocument<org.bson.Document>> cursor = collection.watch(pipeline)
        .fullDocument(FullDocument.UPDATE_LOOKUP).cursor();

Document document = new Document("name", "doc-in-step-1-" + Math.random());
collection.insertOne(document);

while (cursor.hasNext()) {
    // There you go, we got the change document.
    ChangeStreamDocument<Document> csDoc = cursor.next();

    // Let is pick the token which will help us resuming
    // You can save this token in any persistent storage and retrieve it later
    resumeToken = csDoc.getResumeToken();
    //Printing the token
    System.out.println(resumeToken);
    
    //Printing the document.
    System.out.println(csDoc.getFullDocument());
    //This break is intentional but in real project feel free to remove it.
    break;
}

cursor.close();

```
---

## <a name="changes-within-a-single-shard"></a>Ändringar inom en enskild Shard

I följande exempel visas hur du kan få ändringar i objekten i en enda Shard. I det här exemplet hämtas ändringar av objekt som har Shard-nyckeln lika med "a" och värdet för Shard-nyckeln som är lika med "1". Det är möjligt att olika klienter läser ändringar från olika Shards parallellt.

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="current-limitations"></a>Aktuella begränsningar

Följande begränsningar gäller när du använder ändrings strömmar:

* `operationType`Egenskaperna och `updateDescription` stöds inte ännu i utmatnings dokumentet.
* `insert` `update` `replace` Operations typerna, och stöds för närvarande. Borttagnings åtgärden eller andra händelser stöds dock inte ännu.

På grund av dessa begränsningar krävs $match steg, $project Stage och fullDocument alternativ som du ser i föregående exempel.

Till skillnad från byte-feeden i Azure Cosmos DB SQL-API: t finns det ingen separat [ändrings flödes processor bibliotek](change-feed-processor.md) för att använda ändrings strömmar eller ett behov av en container container. För närvarande stöds inte Azure Functions- [utlösare](change-feed-functions.md) för att bearbeta ändrings strömmar.

## <a name="error-handling"></a>Felhantering

Följande felkoder och meddelanden stöds när du använder ändrings strömmar:

* **Http-felkod 16500** -när ändrings data strömmen är begränsad returneras en tom sida.

* **NamespaceNotFound (OperationType ogiltig)** – om du kör ändrings ström för samlingen som inte finns, eller om samlingen har släppts, `NamespaceNotFound` returneras ett fel. Eftersom `operationType` egenskapen inte kan returneras i utdatafilen returneras felet i stället för `operationType Invalidate` felet `NamespaceNotFound` .

## <a name="next-steps"></a>Nästa steg

* [Använd Time to Live för att förfalla data automatiskt i Azure Cosmos DBs API för MongoDB](mongodb-time-to-live.md)
* [Indexering i Azure Cosmos DBs API för MongoDB](mongodb-indexing.md)
