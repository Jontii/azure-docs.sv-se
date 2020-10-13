---
title: Hämta lagrings konto typ och SKU-namn med .NET
titleSuffix: Azure Storage
description: Lär dig hur du hämtar Azure Storage konto typ och SKU-namn med hjälp av .NET-klient biblioteket.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 8fa1e258b07ab98040cbbc5217be789e0bb1b783
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020141"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Hämta lagrings konto typ och SKU-namn med .NET

Den här artikeln visar hur du hämtar Azure Storage konto typ och SKU-namn för en blob med hjälp av [Azure Storage klient biblioteket för .net](/dotnet/api/overview/azure/storage?view=azure-dotnet).

Konto information finns på tjänst versioner som börjar med version 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>Om kontotyp och SKU-namn

**Kontotyp**: giltiga konto typer är,,,, `BlobStorage` `BlockBlobStorage` `FileStorage` `Storage` och `StorageV2` . [Översikt över Azure Storage-kontot](storage-account-overview.md) innehåller mer information, inklusive beskrivningar av de olika lagrings kontona.

**SKU-namn**: giltiga SKU-namn är,,,,,, `Premium_LRS` `Premium_ZRS` `Standard_GRS` `Standard_GZRS` `Standard_LRS` `Standard_RAGRS` `Standard_RAGZRS` och `Standard_ZRS` . SKU-namn är Skift läges känsliga och är sträng fält i [klassen SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet).

## <a name="retrieve-account-information"></a>Hämta konto information

Om du vill hämta lagrings konto typen och det SKU-namn som är associerat med en BLOB anropar du metoden [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) eller [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) .

I följande kod exempel hämtas och visas de skrivskyddade konto egenskaperna.

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra åtgärder som du kan utföra på ett lagrings konto via [Azure Portal](https://portal.azure.com) och Azure-REST API.

- [Hämta konto informations åtgärd (REST)](/rest/api/storageservices/get-account-information)
