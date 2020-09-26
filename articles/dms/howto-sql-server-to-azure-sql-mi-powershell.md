---
title: 'PowerShell: Migrera SQL Server till SQL-hanterad instans'
titleSuffix: Azure Database Migration Service
description: Lär dig att migrera från SQL Server till Azure SQL-hanterad instans med Azure PowerShell och Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit, devx-track-azurepowershell
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: c82acb66266fd36e5b7155adbfa5bd5ade1b765c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91291995"
---
# <a name="migrate-sql-server-to-sql-managed-instance-with-powershell--azure-database-migration-service"></a>Migrera SQL Server till SQL-hanterad instans med PowerShell & Azure Database Migration Service

I den här artikeln migrerar du **Adventureworks2016** -databasen som återställs till en lokal instans av SQL Server 2005 eller senare till en Azure SQL SQL SQL-hanterad instans med hjälp av Microsoft Azure PowerShell. Du kan migrera databaser från en SQL Server instans till en SQL-hanterad instans med hjälp av `Az.DataMigration` modulen i Microsoft Azure PowerShell.

I den här artikeln kan du se hur du:
> [!div class="checklist"]
>
> * Skapa en resursgrupp.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsjobb i en instans av Azure Database Migration Service.
> * Köra migreringen.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Den här artikeln innehåller information om hur du utför både online-och offline-migrering.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra de här stegen:

* [SQL Server 2016 eller senare](https://www.microsoft.com/sql-server/sql-server-downloads) (vilken utgåva som helst).
* En lokal kopia av **AdventureWorks2016** -databasen som är tillgänglig för nedladdning [här](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* För att aktivera TCP/IP-protokollet, som är inaktiverat som standard med SQL Server Express installation. Aktivera TCP/IP-protokollet genom att följa artikeln [Aktivera eller inaktivera ett Server nätverks protokoll](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Konfigurera Windows- [brandväggen för åtkomst till databas motorn](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En SQL-hanterad instans. Du kan skapa en SQL-hanterad instans genom att följa detalj i artikeln [skapa en ASQL-hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Hämta och installera [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 eller senare.
* En Microsoft Azure Virtual Network som skapats med hjälp av Azure Resource Manager distributions modell, som förser Azure Database Migration Service med plats-till-plats-anslutning till dina lokala käll servrar genom att antingen använda [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* En slutförd utvärdering av din lokala databas och schema-migrering med hjälp av Data Migration Assistant, enligt beskrivningen i artikeln [utföra en utvärdering av SQL Server migrering](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* Hämta och installera `Az.DataMigration` modulen (version 0.7.2 eller senare) från PowerShell-galleriet med hjälp av [PowerShell-cmdleten Install-module](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* För att säkerställa att autentiseringsuppgifterna som används för att ansluta till käll SQL Server instans har behörigheten [kontroll Server](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) .
* För att säkerställa att de autentiseringsuppgifter som används för att ansluta till målets SQL-hanterade instans har behörigheten kontroll databas på SQL-hanterade instans databaser.

    > [!IMPORTANT]
    > För online-migreringar måste du redan ha konfigurerat dina Azure Active Directory autentiseringsuppgifter. Mer information finns i artikeln [använda portalen för att skapa ett Azure AD-program och tjänstens huvud namn som har åtkomst till resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Logga in på din Microsoft Azure prenumeration

Logga in på din Azure-prenumeration med hjälp av PowerShell. Mer information finns i artikeln Logga in [med Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resurs grupp är en logisk behållare där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp med hjälp av [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) kommandot.

I följande exempel skapas en resurs grupp med namnet *myResourceGroup* i regionen *USA, östra* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Skapa en instans av Azure Database Migration Service

Du kan skapa en ny instans av Azure Database Migration Service med hjälp av `New-AzDataMigrationService` cmdleten.
Denna cmdlet förväntar sig följande obligatoriska parametrar:

* *Namn på Azure-resurs gruppen*. Du kan använda [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) kommandot för att skapa en Azure-resurs grupp som tidigare visas och ange dess namn som en parameter.
* *Tjänst namn*. Sträng som motsvarar det önskade unika tjänst namnet för Azure Database Migration Service.
* *Plats*. Anger tjänstens plats. Ange en plats för Azure Data Center, till exempel USA, västra eller Sydostasien.
* *SKU*. Den här parametern motsvarar DMS SKU-namnet. SKU-namn som stöds för närvarande är *Basic_1vCore* *Basic_2vCores* *GeneralPurpose_4vCores*.
* *ID för virtuell undernät*. Du kan använda cmdleten [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) för att skapa ett undernät.

I följande exempel skapas en tjänst med namnet *MyDMS* i resurs gruppen *MyDMSResourceGroup* som finns i regionen *USA, östra* med ett virtuellt nätverk med namnet *MyVNET* och ett undernät med namnet *mitt undernät*.

> [!IMPORTANT]
> Kodfragmentet nedan är för en offline-migrering, vilket inte kräver en instans av Azure Database Migration Service baserat på en Premium-SKU. För en online-migrering måste värdet för parametern-SKU innehålla en Premium-SKU.

```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När du har skapat en Azure Database Migration Service-instans skapar du ett migreringsjobb. Ett Azure Database Migration Service-projekt kräver anslutnings information för både käll-och mål instanserna, samt en lista över databaser som du vill migrera som en del av projektet.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Skapa ett objekt för databas anslutnings information för käll-och mål anslutningarna

Du kan skapa ett informations objekt för databas anslutning med hjälp av `New-AzDmsConnInfo` cmdleten, som förväntar sig följande parametrar:

* *ServerType*. Vilken typ av databas anslutning som begärdes, till exempel SQL, Oracle eller MySQL. Använd SQL för SQL Server och Azure SQL.
* *Data källa*. Namnet eller IP-adressen för en SQL Server instans eller Azure SQL Database instans.
* *AuthType*. Autentiseringstypen för anslutning, som kan vara antingen SqlAuthentication eller WindowsAuthentication.
* *TrustServerCertificate*. Den här parametern anger ett värde som anger om kanalen är krypterad och kringgår certifikat kedjan för att verifiera förtroende. Värdet kan vara `$true` eller `$false` .

I följande exempel skapas ett anslutnings informations objekt för en käll SQL Server som kallas *MySourceSQLServer* med SQL-autentisering:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Nästa exempel visar hur du skapar anslutnings information för en hanterad Azure SQL-instans med namnet "targetmanagedinstance":

```powershell
$targetResourceId = (Get-AzSqlInstance -Name "targetmanagedinstance").Id
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI -MiResourceId $targetResourceId
```

### <a name="provide-databases-for-the-migration-project"></a>Ange databaser för migreringsjobbet

Skapa en lista med `AzDataMigrationDatabaseInfo` objekt som anger databaser som en del av det Azure Database migration service projektet, som kan anges som parameter för att skapa projektet. Du kan använda cmdleten `New-AzDataMigrationDatabaseInfo` för att skapa `AzDataMigrationDatabaseInfo` .

I följande exempel skapas `AzDataMigrationDatabaseInfo` projektet för **AdventureWorks2016** -databasen och läggs till i listan som ska anges som parameter för att skapa projekt.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Skapa ett projekt objekt

Slutligen kan du skapa ett Azure Database Migration Service-projekt med namnet *MyDMSProject* som finns i *östra USA* med `New-AzDataMigrationProject` och lägga till de tidigare skapade käll-och mål anslutningarna och listan över databaser som ska migreras.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Skapa och starta en migrerings uppgift

Skapa och starta sedan en Azure Database Migration Service aktivitet. Den här uppgiften kräver information om autentiseringsuppgifter för både källa och mål, samt listan över databas tabeller som ska migreras och den information som redan finns i projektet som en förutsättning.

### <a name="create-credential-parameters-for-source-and-target"></a>Skapa Credential-parametrar för källa och mål

Skapa anslutnings säkerhets uppgifter som ett [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) -objekt.

I följande exempel visas hur du skapar *PSCredential* -objekt för både käll-och mål anslutningar, vilket ger lösen ord som String-variabler *$sourcePassword* och *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Skapa ett objekt för säkerhets kopiering av FileShare

Skapa nu ett FileShare-objekt som representerar den lokala SMB-nätverks resurs som Azure Database Migration Service kan ta säkerhets kopior av käll databasen med hjälp av `New-AzDmsFileShare` cmdleten.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Skapa markerat databas objekt

Nästa steg är att välja käll-och mål databaserna med hjälp av- `New-AzDmsSelectedDB` cmdleten.

I följande exempel används för att migrera en enskild databas från SQL Server till en Azure SQL-hanterad instans:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Om en hel SQL Server instans behöver en hiss-och-Shift i en hanterad Azure SQL-instans, anges en slinga för att ta alla databaser från källan nedan. I följande exempel, för $Server, $SourceUserName och $SourcePassword, anger du din käll SQL Server information.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>SAS-URI för Azure Storage container

Skapa en variabel som innehåller SAS-URI: n som ger Azure Database Migration Service med åtkomst till lagrings konto behållaren som tjänsten laddar upp säkerhetskopieringsfilerna till.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

> [!NOTE]
> Azure Database Migration Service stöder inte användning av en SAS-token på konto nivå. Du måste använda en SAS-URI för lagrings konto behållaren. [Lär dig hur du hämtar SAS-URI för blobbcontainer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).

### <a name="additional-configuration-requirements"></a>Ytterligare konfigurations krav

Det finns några ytterligare krav som du behöver åtgärda, men de skiljer sig åt beroende på om du utför en offline-eller online-migrering.

#### <a name="offline-migrations"></a>Offline-migrering

Utför följande ytterligare konfigurations åtgärder för offline-migreringar.

* **Välj inloggningar**. Skapa en lista med inloggningar som ska migreras på det sätt som visas i följande exempel:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > För närvarande stöder Azure Database Migration Service bara migrering av SQL-inloggningar.

* **Välj Agent jobb**. Skapa en lista med Agent jobb som ska migreras som visas i följande exempel:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > För närvarande har Azure Database Migration Service endast stöd för jobb med under system för del system för T-SQL.

#### <a name="online-migrations"></a>Online-migreringar

Utför följande ytterligare konfigurations åtgärder endast för online-migreringar.

* **Konfigurera Azure Active Directory app**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Konfigurera lagrings resurs**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Skapa och starta migreringen

Använd `New-AzDataMigrationTask` cmdleten för att skapa och starta en migrering.

#### <a name="specify-parameters"></a>Ange parametrar

##### <a name="common-parameters"></a>Vanliga parametrar

Oavsett om du utför en offline-eller online-migrering `New-AzDataMigrationTask` förväntar cmdleten följande parametrar:

* *TaskType*. Typ av migrering som ska skapas för SQL Server till Azure SQL-hanterad instans *MigrateSqlServerSqlDbMi* förväntas. 
* *Resurs grupps namn*. Namnet på den Azure-resurs grupp där aktiviteten ska skapas.
* *ServiceName*. Azure Database Migration Service instans där du vill skapa uppgiften.
* *ProjectName*. Namnet på Azure Database Migration Service projektet som uppgiften ska skapas i. 
* *Aktivitets*namn. Namn på den uppgift som ska skapas. 
* *SourceConnection*. AzDmsConnInfo-objekt som representerar käll SQL Server anslutning.
* *TargetConnection*. AzDmsConnInfo-objekt som representerar en Azure SQL-hanterad instans anslutning.
* *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) -objekt för att ansluta till käll servern.
* *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) -objekt för att ansluta till mål servern.
* *SelectedDatabase*. AzDataMigrationSelectedDB-objekt som representerar käll-och mål databas mappning.
* *BackupFileShare*. FileShare-objekt som representerar den lokala nätverks resurs som Azure Database Migration Service kan ta säkerhets kopior av käll databasen till.
* *BackupBlobSasUri*. SAS-URI: n som tillhandahåller Azure Database Migration Service med åtkomst till lagrings konto behållaren som tjänsten överför säkerhets kopiorna till. Lär dig hur du hämtar SAS-URI för blobbcontainer.
* *SelectedLogins*. Lista över valda inloggningar att migrera.
* *SelectedAgentJobs*. Lista över valda Agent jobb att migrera.

##### <a name="additional-parameters"></a>Ytterligare parametrar

`New-AzDataMigrationTask`Cmdlet förväntar sig också parametrar som är unika för typen av migrering, offline eller online, som du utför.

* **Offline-migreringar**. För offline-migrering `New-AzDataMigrationTask` förväntar sig cmdleten följande parametrar:

  * *SelectedLogins*. Lista över valda inloggningar att migrera.
  * *SelectedAgentJobs*. Lista över valda Agent jobb att migrera.

* **Online-migreringar**. För online-migrering `New-AzDataMigrationTask` förväntas även följande parametrar för cmdleten.

* *AzureActiveDirectoryApp*. Active Directory program.
* *StorageResourceID*. Resurs-ID för lagrings kontot.

#### <a name="create-and-start-an-offline-migration-task"></a>Skapa och starta en aktivitet för offline-migrering

I följande exempel skapas och startas en offline-migrering med namnet **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

#### <a name="create-and-start-an-online-migration-task"></a>Skapa och starta en migrering online

I följande exempel skapas och startas en online-migrering med namnet **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>Övervaka migreringen

Utför följande uppgifter för att övervaka migreringen.

1. Konsolidera all information om migreringen till en variabel som kallas $CheckTask.

    Använd följande kodfragment för att kombinera information om migrering, till exempel egenskaper, tillstånd och databas information som är associerad med migreringen:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Använd `$CheckTask` variabeln för att hämta det aktuella läget för migreringen.

    Om du vill använda `$CheckTask` variabeln för att hämta det aktuella läget för migreringen kan du övervaka migreringen som körs genom att fråga efter uppgiftens tillstånds egenskap, som visas i följande exempel:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Utföra start punkt (endast online-migreringar)

Med en online-migrering utförs en fullständig säkerhets kopiering och återställning av databaserna och sedan fortsätter återställningen av transaktions loggarna som lagras i BackupFileShare.

När databasen i en Azure SQL-hanterad instans uppdateras med senaste data och är synkroniserad med käll databasen, kan du utföra en start punkt.

I följande exempel utförs cutover\migration. Användarna anropar det här kommandot efter eget gottfinnande.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Tar bort instansen av Azure Database Migration Service

När migreringen är klar kan du ta bort Azure Database Migration Service-instansen:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Ytterligare resurser

Information om ytterligare migrerings scenarier (käll-/mål par) finns i Microsoft [Database migration guide](https://datamigration.microsoft.com/).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om Azure Database Migration Service i artikeln [Vad är Azure Database migration service?](https://docs.microsoft.com/azure/dms/dms-overview).
