---
title: Skapa en tjänst-SAS för en behållare eller BLOB med .NET
titleSuffix: Azure Storage
description: Lär dig hur du skapar en signatur för delad åtkomst för en behållare (SAS) för en behållare eller BLOB med hjälp av .NET-klient biblioteket.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/05/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 6ebb81e6f5adce1dbc301ac905fed5af1ee6a825
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91758313"
---
# <a name="create-a-service-sas-for-a-container-or-blob-with-net"></a>Skapa en tjänst-SAS för en behållare eller BLOB med .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Den här artikeln visar hur du använder lagrings konto nyckeln för att skapa en tjänst-SAS för en behållare eller BLOB med [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="create-a-service-sas-for-a-blob-container"></a>Skapa en tjänst-SAS för en BLOB-behållare

I följande kod exempel skapas en SAS för en behållare. Om namnet på en befintlig lagrad åtkomst princip anges, är den principen associerad med SAS. Om ingen lagrad åtkomst princip anges skapar koden en ad hoc SAS på behållaren.

### <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

En tjänst-SAS är signerad med konto åtkomst nyckeln. Använd [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) -klassen för att skapa de autentiseringsuppgifter som används för att signera SAS. Skapa sedan ett nytt [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) -objekt och anropa [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) för att hämta SAS-token-strängen.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetContainerSasUri":::

### <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

Om du vill skapa en tjänst-SAS för en behållare anropar du metoden [CloudBlobContainer. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) .

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}

```
---

## <a name="create-a-service-sas-for-a-blob"></a>Skapa en tjänst-SAS för en BLOB

I följande kod exempel skapas en SAS på en blob. Om namnet på en befintlig lagrad åtkomst princip anges, är den principen associerad med SAS. Om ingen lagrad åtkomst princip anges skapar koden en ad hoc SAS på blobben.

### <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

En tjänst-SAS är signerad med konto åtkomst nyckeln. Använd [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) -klassen för att skapa de autentiseringsuppgifter som används för att signera SAS. Skapa sedan ett nytt [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) -objekt och anropa [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) för att hämta SAS-token-strängen.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetBlobSasUri":::

### <a name="net-v11"></a>[\.NET-v11](#tab/dotnetv11)

Om du vill skapa en tjänst-SAS för en BLOB anropar du metoden [CloudBlob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) .

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Nästa steg

- [Bevilja begränsad åtkomst till Azure Storage resurser med signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md)
- [Skapa en tjänst-SAS](/rest/api/storageservices/create-service-sas)
