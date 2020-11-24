---
title: Överför stora mängder slumpmässiga data parallellt till Azure Storage
description: Lär dig hur du använder Azure Storage klient bibliotek för att överföra stora mängder slumpmässiga data parallellt till ett Azure Storage konto
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/08/2019
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: 5dc1f8b8a7c46a3d6ad6f62d93bc91753e42c3ae
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545048"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Överföra stora mängder slumpmässiga data parallellt till Azure Storage

Den här självstudien är del två i en serie. Den här kursen visar hur du distribuerar ett program som överför stora mängder slumpmässiga data till ett Azure-lagringskonto.

I del två i serien lär du dig hur du:

> [!div class="checklist"]
> * Konfigurera anslutningssträngen
> * Skapa programmet
> * Kör programmet
> * Validera antalet anslutningar

Azure Blob Storage är en skalbar tjänst för att lagra data. För att ditt program ska få bästa möjliga prestanda rekommenderar vi att du lär dig hur Blob Storage fungerar. Det är viktigt att känna till gränserna för Azure-blobar för att lära dig mer om de här gränserna: [skalbarhets-och prestanda mål för Blob Storage](../blobs/scalability-targets.md).

[Namngivning av partitioner](../blobs/storage-performance-checklist.md#partitioning) är en annan potentiellt viktig faktor när du skapar ett program med höga prestanda med hjälp av blobbar. För block storlekar som är större än eller lika med 4 MiB används [block blobbar med hög genomflöde](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) , och partitionens namn påverkar inte prestanda. För block storlekar som är mindre än 4 MiB använder Azure Storage ett intervall baserat partitionerings schema för skalning och belastnings utjämning. Den här konfigurationen innebär att filer med liknande namnkonventioner eller prefix hamnar i samma partition. Den här logiken innehåller namnet på den container som filerna överförs till. I den här kursen använder du filer som har globalt unika identifierare som namn samt slumpmässigt genererat innehåll. De överförs sedan till fem olika containrar med slumpmässiga namn.

## <a name="prerequisites"></a>Förutsättningar

För att utföra den här kursen måste du först ha slutfört den föregående Storage-självstudiekursen: [Skapa en virtuell dator och ett lagringskonto för ett skalbart program][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Fjärranslut till din virtuella dator

Använd följande kommando på den lokala datorn för att skapa en fjärrskrivbordssession med den virtuella datorn. Ersätt IP-adressen med publicIPAddress för den virtuella datorn. När du uppmanas att göra det anger du de autentiseringsuppgifter som du använde när du skapade den virtuella datorn.

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Konfigurera anslutningssträngen

Navigera till ditt lagringskonto i Azure Portal. Välj **Åtkomstnycklar** under **Inställningar** i ditt lagringskonto. Kopiera **anslutningssträngen** från den primära eller sekundära nyckeln. Logga in på den virtuella datorn som du skapade i den föregående självstudiekursen. Öppna en **kommandotolk** som administratör och kör kommandot `setx` med växeln `/m`. Det här kommandot sparar en miljövariabel för datorn. Miljövariabeln inte tillgänglig förrän du har läst in **kommandotolken** på nytt. Ersätt **\<storageConnectionString\>** i följande exempel:

```
setx storageconnectionstring "<storageConnectionString>" /m
```

När det är klart öppnar du en annan **kommandotolk**, navigerar till `D:\git\storage-dotnet-perf-scale-app` och skriver `dotnet build` för att skapa programmet.

## <a name="run-the-application"></a>Kör programmet

Navigera till `D:\git\storage-dotnet-perf-scale-app`.

Skriv `dotnet run` för att köra programmet. Första gången du kör `dotnet` fyller den i din lokala paketcache för att förbättra återställningstiden och aktivera åtkomst offline. Det här kommandot tar upp till en minut att slutföra och görs bara en gång.

```
dotnet run
```

Programmet skapar fem containrar med slumpmässiga namn och börjar överföra filerna i mellanlagringskatalogen till lagringskontot. Programmet anger inställningen för minsta antal trådar till 100 och [DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) till 100 för att säkerställa att ett stort antal samtidiga anslutningar tillåts när programmet körs.

Förutom att ange inställningarna för trådning och anslutningsgräns konfigureras [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) för metoden [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) för att använda parallellitet och inaktivera MD5-hashverifiering. Filerna överförs i block om 100 MB. Den här konfigurationen ger bättre prestanda, men kan vara kostsam om du använder ett bristfälligt nätverk eftersom hela blocket på 100 MB hämtas om det uppstår ett fel.

|Egenskap|Värde|Beskrivning|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| Den här inställningen delar in bloben i block vid överföringen. För högsta prestanda måste värdet vara åtta gånger antalet kärnor. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Den här egenskapen inaktiverar kontrollen av MD5-hashen för innehållet som har överförts. Överföringen går snabbare om MD5-verifieringen inaktiveras. Däremot bekräftas inte giltigheten eller integriteten för de filer som överförs.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| falskt| Den här egenskapen anger om en MD5-hash beräknas och sparas med filen.   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| Två sekunders backoff med högst tio återförsök |Bestämmer återförsöksprincipen för begäranden. Vi anslutningsfel görs nya försök. I det här exemplet har en [ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry)-princip angetts med två sekunders backoff och högst tio återförsök. Den här inställningen är viktig när programmet stängs för att uppnå skalbarhets målen för Blob Storage. Mer information finns i [skalbarhets-och prestanda mål för Blob Storage](../blobs/scalability-targets.md).  |

Aktiviteten `UploadFilesAsync` visas i följande exempel:

```csharp
private static async Task UploadFilesAsync()
{
    // Create random 5 characters containers to upload files to.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();
    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // path to the directory to upload
    string uploadPath = currentdir + "\\upload";
    Stopwatch time = Stopwatch.StartNew();
    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);
        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections are retried with a backoff policy. As multiple large files are being uploaded
        // large block sizes this can cause an issue if an exponential retry policy is not defined.  Additionally parallel operations are enabled with a thread count of 8
        // This could be should be multiple of the number of cores that the machine has. Lastly MD5 hash validation is disabled for this example, this improves the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };
        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            // Create random file names and set the block size that is used for the upload.
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}.", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;
            await sem.WaitAsync();

            // Create tasks for each file that is uploaded. This is added to a collection that executes them all asyncronously.  
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));
            count++;
        }

        // Creates an asynchronous task that completes when all the uploads complete.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

I följande exempel visas trunkerade utdata för ett program som körs på ett Windows-system.

```
Created container https://mystorageaccount.blob.core.windows.net/9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container https://mystorageaccount.blob.core.windows.net/bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container https://mystorageaccount.blob.core.windows.net/9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container https://mystorageaccount.blob.core.windows.net/47646f1a-c498-40cd-9dae-840f46072180
Created container https://mystorageaccount.blob.core.windows.net/38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directory: D:\git\storage-dotnet-perf-scale-app\upload
Found 50 file(s)
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt to container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt to container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt to container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt to container 47646f1a-c498-40cd-9dae-840f46072180.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt to container 38b2cdab-45fa-4cf9-94e7-d533837365aa.
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### <a name="validate-the-connections"></a>Verifiera anslutningarna

När filerna överförs kan du verifiera antalet samtidiga anslutningar till lagringskontot. Öppna en **kommandotolk** och skriv `netstat -a | find /c "blob:https"`. Det här kommandot visar antalet anslutningar som för närvarande öppnas med hjälp av `netstat`. Följande exempel visar utdata som ser ut ungefär som när du kör kursen själv. Som du ser i exemplet var 800 anslutningar öppna när de slumpmässiga filerna överfördes till lagringskontot. Det här värdet ändras under tiden överföringen körs. I och med att överföringen görs i parallella block går det mycket snabbare att överföra innehållet.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Nästa steg

I den andra delen i serien har du lärt dig hur du överför stora mängder slumpmässiga data parallellt till lagringskontot, till exempel hur du gör för att:

> [!div class="checklist"]
> * Konfigurera anslutningssträngen
> * Skapa programmet
> * Kör programmet
> * Validera antalet anslutningar

Gå vidare till den tredje delen i serien och lär dig hur du laddar ned stora mängder data från ett lagringskonto.

> [!div class="nextstepaction"]
> [Ladda ned stora mängder slumpmässiga data från Azure Storage](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md