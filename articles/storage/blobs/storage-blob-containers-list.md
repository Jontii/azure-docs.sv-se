---
title: Lista BLOB-behållare med .NET-Azure Storage
description: Lär dig hur du listar BLOB-behållare i ditt Azure Storage-konto med hjälp av .NET-klient biblioteket.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: f443cd5603e6ca0f60dc0e69b734bfa46138d476
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018951"
---
# <a name="list-blob-containers-with-net"></a>Lista BLOB-behållare med .NET

När du listar behållarna i ett Azure Storage konto från din kod kan du ange ett antal alternativ för att hantera hur resultaten returneras från Azure Storage. Den här artikeln visar hur du visar behållare med hjälp av [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage?view=azure-dotnet).  

## <a name="understand-container-listing-options"></a>Förstå alternativ för behållar lista

Om du vill lista behållare i ditt lagrings konto, anropa någon av följande metoder:

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

Överlagringarna för dessa metoder ger ytterligare alternativ för att hantera hur behållare returneras av list åtgärden. De här alternativen beskrivs i följande avsnitt.

### <a name="manage-how-many-results-are-returned"></a>Hantera hur många resultat som returneras

Som standard returnerar en List åtgärd upp till 5000 resultat i taget. Om du vill returnera en mindre uppsättning resultat anger du ett värde som inte är noll för `maxresults` parametern när du anropar en av **ListContainerSegmented** -metoderna.

Om ditt lagrings konto innehåller fler än 5000 behållare, eller om du har angett ett värde för en `maxresults` sådan att List åtgärden returnerar en delmängd av behållare i lagrings kontot, Azure Storage returnerar en *fortsättnings-token* med listan över behållare. En fortsättnings-token är ett ogenomskinligt värde som du kan använda för att hämta nästa uppsättning resultat från Azure Storage.

I din kod kontrollerar du värdet för fortsättnings-token för att avgöra om det är null. När tilläggs-token är null slutförs uppsättningen av resultat. Om tilläggs-token inte är null anropar du **ListContainersSegmented** eller **ListContainersSegmentedAsync** igen och skickar i fortsättnings-token för att hämta nästa uppsättning resultat, tills den fortsatta token är null.

### <a name="filter-results-with-a-prefix"></a>Filtrera resultat med ett prefix

Om du vill filtrera listan över behållare anger du en sträng för `prefix` parametern. Prefixlängden kan innehålla ett eller flera tecken. Azure Storage returnerar sedan bara de behållare vars namn börjar med prefixet.

### <a name="return-metadata"></a>Returnera metadata

Om du vill returnera metadata för containern med resultaten anger du värdet för **metadata** för [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) -uppräkningen. Azure Storage innehåller metadata med varje behållare som returneras, så du behöver inte också anropa en av **FetchAttributes** -metoderna för att hämta containerns metadata.

## <a name="example-list-containers"></a>Exempel: list behållare

I följande exempel visas en asynkron lista över behållare i ett lagrings konto som börjar med ett angivet prefix. Exemplet visar behållare i steg om fem resultat i taget, och använder en fortsättnings-token för att hämta nästa resultat segment. Exemplet returnerar även containerns metadata med resultaten.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix)
{
    Console.WriteLine("List all containers beginning with prefix {0}, plus container metadata:", prefix);

    try
    {
        ContainerResultSegment resultSegment = null;
        BlobContinuationToken continuationToken = null;

        do
        {
            // List containers beginning with the specified prefix, returning segments of 5 results each.
            // Passing null for the maxResults parameter returns the max number of results (up to 5000).
            // Requesting the container's metadata with the listing operation populates the metadata,
            // so it's not necessary to also call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, 5, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token. If not null, get the next segment.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Se även

[Lista behållare](/rest/api/storageservices/list-containers2) 
 [Räkna upp BLOB-resurser](/rest/api/storageservices/enumerating-blob-resources)
