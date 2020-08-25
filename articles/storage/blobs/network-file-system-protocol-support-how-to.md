---
title: Montera Azure Blob Storage med hjälp av NFS 3,0-protokollet (för hands version) | Microsoft Docs
description: Lär dig hur du monterar en behållare i Blob Storage från en virtuell Azure-dator (VM) eller en klient som körs lokalt med hjälp av NFS 3,0-protokollet.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 09206b8189f03a37f8bd7d073238609a3f1bd3ad
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816107"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>Montera Blob Storage med hjälp av Network File System (NFS) 3,0-protokollet (för hands version)

Du kan montera en behållare i Blob Storage från en Windows-eller Linux-baserad virtuell Azure-dator (VM) eller ett Windows-eller Linux-system som körs lokalt med hjälp av NFS 3,0-protokollet. Den här artikeln innehåller steg-för-steg-anvisningar. Om du vill veta mer om NFS 3,0 protokoll stöd i Blob Storage, se [Network File System (NFS) stöd för 3,0-protokoll i Azure Blob Storage (för hands version)](network-file-system-protocol-support.md).

> [!NOTE]
> NFS 3,0 protokoll stöd i Azure Blob Storage finns i offentlig för hands version och är tillgängligt i följande regioner: USA, östra, centrala USA, västra centrala, Australien, sydöstra, Nord Europa, Storbritannien, västra, Korea, centrala, Korea, södra och centrala Kanada.

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>Steg 1: registrera NFS 3,0-protokoll funktionen med din prenumeration

1. Öppna ett PowerShell-kommando fönster. 

2. Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

   ```powershell
   Connect-AzAccount
   ```

3. Om din identitet är associerad med mer än en prenumeration anger du den aktiva prenumerationen.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   Ersätt `<subscription-id>` placeholder-värdet med ID: t för din prenumeration.

4. Registrera `AllowNFSV3` funktionen med hjälp av följande kommando.

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. Registrera `PremiumHns` funktionen med hjälp av följande kommando.

   ```powershell
   Register-AzProviderFeature -FeatureName PremiumHns -ProviderNamespace Microsoft.Storage  
   ```

6. Registrera resurs leverantören med hjälp av följande kommando.
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>Steg 2: kontrol lera att funktionen har registrerats 

Registreringens godkännande kan ta upp till en timme. Kontrol lera att registreringen är klar med hjälp av följande kommandon.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName PremiumHns  
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>Steg 3: skapa ett Azure-Virtual Network (VNet)

Ditt lagrings konto måste finnas i ett VNet. Ett VNet gör det möjligt för klienter att ansluta på ett säkert sätt till ditt lagrings konto. Mer information om VNet och hur du skapar ett finns i [Virtual Network-dokumentationen](https://docs.microsoft.com/azure/virtual-network/).

> [!NOTE]
> Klienter i samma VNet kan montera behållare i ditt konto. Du kan också montera en behållare från en klient som körs i ett lokalt nätverk, men du måste först ansluta ditt lokala nätverk till ditt VNet. Se [nätverks anslutningar som stöds](network-file-system-protocol-support.md#supported-network-connections).

## <a name="step-4-configure-network-security"></a>Steg 4: Konfigurera nätverks säkerhet

Det enda sättet att skydda data i ditt konto är genom att använda ett VNet och andra nätverks säkerhets inställningar. Andra verktyg som används för att skydda data, inklusive konto nyckel auktorisering, Azure Active Directory (AD) säkerhet och åtkomst kontrol listor (ACL) stöds inte ännu i konton som har stöd för NFS 3,0-protokollet aktiverat. 

Information om hur du skyddar data i ditt konto finns i följande rekommendationer: [nätverks säkerhets rekommendationer för Blob Storage](security-recommendations.md#networking).

## <a name="step-5-create-and-configure-a-storage-account"></a>Steg 5: skapa och konfigurera ett lagrings konto

Om du vill montera en behållare med hjälp av NFS 3,0 måste du skapa ett lagrings konto **när** du har registrerat funktionen med din prenumeration. Du kan inte aktivera konton som fanns innan du registrerade funktionen. 

I för hands versionen av den här funktionen stöds NFS 3,0-protokollet endast i [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) -konton.

När du konfigurerar kontot väljer du dessa värden:

|Inställningen | Värde|
|----|---|
|Plats|En av följande regioner: USA, östra, centrala USA, västra centrala, sydöstra Australien, norra Europa, Storbritannien, västra, Korea, centrala, Korea, södra och centrala Kanada |
|Prestanda|Premium|
|Typ av konto|BlockBlobStorage|
|Replikering|Lokalt redundant lagring (LRS)|
|Anslutningsmetod|Offentlig slut punkt (valda nätverk) eller privat slut punkt|
|Säker överföring krävs|Inaktiverad|
|Hierarkiskt namn område|Enabled|
|NFS V3|Enabled|

Du kan acceptera standardvärdena för alla andra inställningar. 

## <a name="step-6-create-a-container"></a>Steg 6: Skapa en container

Skapa en behållare i ditt lagrings konto genom att använda något av dessa verktyg eller SDK: er:

|Verktyg|SDK:er|
|---|---|
|[Azure-portalen](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-blobs.md#create-a-container)|[Java](data-lake-storage-directory-file-acl-java.md#create-a-container)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](https://docs.microsoft.com/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>Steg 7: montera behållaren

Skapa en katalog på ditt Windows-eller Linux-system och montera sedan en behållare i lagrings kontot.

### <a name="linux"></a>[Linux](#tab/linux)

1. Skapa en katalog på ett Linux-system.

   ```
   mkdir -p /mnt/test
   ```

2. Montera en behållare med hjälp av följande kommando.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - Ersätt `<storage-account-name>` plats hållaren som visas i det här kommandot med namnet på ditt lagrings konto.  

   - Ersätt `<container-name>` plats hållaren med namnet på din behållare.


### <a name="windows"></a>[Windows](#tab/windows)

1. Öppna dialog rutan **Windows-funktioner** och aktivera sedan funktionen **klient för NFS** . 

   ![Funktionen klient för Network File System](media/network-file-system-protocol-how-to/client-for-network-files-system-feature.png)

2. Montera en behållare med hjälp av [monterings](https://docs.microsoft.com/windows-server/administration/windows-commands/mount) kommandot.

   ```
   mount -o nolock <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name> *
   ```

   - Ersätt `<storage-account-name>` plats hållaren som visas i det här kommandot med namnet på ditt lagrings konto.  

   - Ersätt `<container-name>` plats hållaren med namnet på din behållare.

3. Om du behöver Skriv behörighet kan du behöva ändra standard-UID och GID som Windows använder för att ansluta till resursen. Det gör du genom att köra följande PowerShell-kommandon som administratör:

   ```
   New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousUid -PropertyType DWord -Value 0
   New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousGid -PropertyType DWord -Value 0
   ```
   
   - Starta om NFS-klienttjänsten eller starta om servern när du har gjort den här ändringen.

---

## <a name="resolve-common-issues"></a>Lös vanliga problem

|Problem/fel | Lösning|
|---|---|
|`Access denied by server while mounting`|Se till att klienten körs i ett undernät som stöds. Se de [nätverks platser som stöds](network-file-system-protocol-support.md#supported-network-connections).|
|`No such file or directory`| Se till att containern du monterar skapades efter att du verifierade att funktionen hade registrerats. Se [steg 2: kontrol lera att funktionen har registrerats](#step-2-verify-that-the-feature-is-registered). Se också till att ange monterings kommandot och dess parametrar direkt i terminalen. Om du kopierar och klistrar in någon del av kommandot i terminalen från ett annat program kan det finnas dolda tecken i den inklistrade informationen som orsakar felet.|

## <a name="see-also"></a>Se även

[NFS (Network File System) 3,0 protokoll stöd i Azure Blob Storage (för hands version)](network-file-system-protocol-support.md)







