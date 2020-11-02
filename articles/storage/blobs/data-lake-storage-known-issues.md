---
title: Kända problem med Azure Data Lake Storage Gen2 | Microsoft Docs
description: Läs mer om begränsningar och kända problem med Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/28/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: f995750c1e009febcb9872c230e22921ff9c50c4
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186594"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Kända problem med Azure Data Lake Storage Gen2

I den här artikeln beskrivs begränsningar och kända problem med Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Blob Storage-funktioner som stöds

Nu fungerar ett ökande antal Blob Storage-funktioner med konton som har ett hierarkiskt namn område. En fullständig lista finns [i Blob Storage funktioner som är tillgängliga i Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Azure Service-integration som stöds

Azure Data Lake Storage Gen2 stöder flera Azure-tjänster som du kan använda för att mata in data, utföra analyser och skapa visuella representationer. En lista över Azure-tjänster som stöds finns i [Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Se [Azure-tjänster som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plattformar för öppen källkod som stöds

Flera plattformar med öppen källkod har stöd för Data Lake Storage Gen2. En fullständig lista finns i [plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Se [plattformar med öppen källkod som stöder Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>API: er för Blob Storage

BLOB-API: er och Data Lake Storage Gen2-API: er kan köras på samma data.

I det här avsnittet beskrivs problem och begränsningar med att använda BLOB-API: er och Data Lake Storage Gen2 API: er för att använda samma data.

* Du kan inte använda BLOB API och Data Lake Storage-API: er för att skriva till samma instans av en fil. Om du skriver till en fil med hjälp av Data Lake Storage Gen2 API: er visas inte filens block för anrop till BLOB-API: t [Get block List](https://docs.microsoft.com/rest/api/storageservices/get-block-list) . Det enda undantaget är när du använder du skriver över. Du kan skriva över en fil/BLOB med antingen API.

* När du använder [list-bloben](https://docs.microsoft.com/rest/api/storageservices/list-blobs) utan att ange en avgränsare, kommer resultatet att inkludera både kataloger och blobbar. Om du väljer att använda en avgränsare använder du bara ett snedstreck ( `/` ). Detta är den enda avgränsare som stöds.

* Om du använder [Delete BLOB](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API för att ta bort en katalog tas katalogen bara bort om den är tom. Det innebär att du inte kan använda BLOB API-borttagningarna rekursivt.

Dessa BLOB REST-API: er stöds inte:

* [Lägg till BLOB (sida)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Placerings sida](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Hämta sid intervall](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [BLOB för stegvis kopiering](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Lägg till sida från URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Lägg till block från URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Ohanterade VM-diskar stöds inte i konton som har ett hierarkiskt namn område. Om du vill aktivera ett hierarkiskt namn område på ett lagrings konto placerar du ohanterade virtuella dator diskar i ett lagrings konto där funktionen för hierarkiskt namn område inte är aktive rad.

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>Stöd för att ställa in åtkomst kontrol listor (ACL) rekursivt

Möjligheten att tillämpa ACL-ändringar rekursivt från överordnad katalog till underordnade objekt finns i [offentlig för hands version](recursive-access-control-lists.md). I den aktuella versionen av den här funktionen kan du tillämpa ACL-ändringar med hjälp av PowerShell, .NET SDK och python SDK. Support är ännu inte tillgängligt för Java SDK, Azure CLI, Azure Portal eller Azure Storage Explorer.

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

Använd endast den senaste versionen av AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Tidigare versioner av AzCopy, till exempel AzCopy v 8.1, stöds inte.

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Azure Lagringsutforskaren

Använd endast versioner `1.6.0` eller högre.

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Storage Explorer i Azure Portal

ACL: er stöds inte ännu.

<a id="third-party-apps"></a>

## <a name="third-party-applications"></a>Program från tredje part

Program från tredje part som använder REST-API: er för arbete fortsätter att fungera om du använder dem med Data Lake Storage Gen2 program som anropar BLOB-API: er kommer att fungera.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Åtkomst kontrol listor (ACL) och anonym Läs åtkomst

Om [Anonym Läs åtkomst](storage-manage-access-to-resources.md) har beviljats till en behållare, har ACL: er ingen påverkan på den behållaren eller filerna i den behållaren.

## <a name="diagnostic-logs"></a>Diagnostikloggar

Inställningen för kvarhållning av dagar stöds inte ännu, men du kan ta bort loggar manuellt med hjälp av ett verktyg som stöds, till exempel Azure Storage Explorer, REST eller en SDK.

## <a name="lifecycle-management-policies-with-premium-tier-for-azure-data-lake-storage"></a>Policys för livs cykel hantering med Premium-nivån för Azure Data Lake Storage

Du kan inte flytta data som lagras på Premium-nivån mellan frekvent, låg frekvent och Arkiv lag rings nivå. Du kan dock kopiera data från Premium-nivån till frekvent åtkomst nivå i ett annat konto.

## <a name="dremio-support-with-premium-performance-blockblobstorage-storage-accounts"></a>Dremio-support med BlockBlobStorage-lagrings konton för Premium-prestanda

Dremio ansluter ännu inte till ett BlockBlobStorage-konto som har funktionen hierarkiskt namn område aktive rad på den. 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Windows Azure Storage Blob (WASB) driv rutin (stöds inte med Data Lake Storage Gen2)

WASB-drivrutinen, som har utformats för att fungera med enbart BLOB API, påträffar för närvarande problem i några vanliga scenarier. Mer specifikt, när det är en klient till ett hierarkiskt namn områdes lagrings konto. Åtkomst med flera protokoll på Data Lake Storage inte minimerar problemen. 

För tiden som är (och troligt vis den förutsebara framtiden) stöder vi inte kunder som använder WASB-drivrutinen som en klient till ett hierarkiskt namn områdes lagrings konto. I stället rekommenderar vi att du väljer att använda [ABFS-drivrutinen (Azure Blob File System)](data-lake-storage-abfs-driver.md) i din Hadoop-miljö. Om du försöker migrera från en lokal Hadoop-miljö med en tidigare version än Hadoop Branch-3 kan du öppna ett support ärende i Azure så att vi kan kontakta dig på rätt väg för dig och din organisation.
