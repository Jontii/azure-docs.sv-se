---
title: Migrera virtuella VMware-datorer till Azure (utan agent) – PowerShell
description: Lär dig hur du kör en agent lös migrering av virtuella VMware-datorer med Azure Migrate via PowerShell.
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 10/1/2020
ms.author: rahugup
ms.openlocfilehash: eed10f13b9495ab2cccfd9c57ae14ccc5d8e4a63
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043552"
---
# <a name="migrate-vmware-vms-to-azure-agentless---powershell"></a>Migrera virtuella VMware-datorer till Azure (utan agent) – PowerShell

I den här artikeln får du lära dig hur du migrerar identifierade virtuella VMware-datorer med metoden utan agent med hjälp av Azure PowerShell för [Azure Migrate: Server-migrering](migrate-services-overview.md#azure-migrate-server-migration-tool). 

Lär dig att:

> [!div class="checklist"]
> * Hämta identifierade virtuella VMware-datorer i ett Azure Migrate-projekt.
> * Starta replikering av virtuella datorer.
> * Uppdatera egenskaper för replikering av virtuella datorer.
> * Övervaka replikering.
> * Kör en testmigrering för att se till att allt fungerar som förväntat.
> * Kör en fullständig migrering av virtuella datorer.

> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudierna använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="prerequisites"></a>Krav

Innan du börjar de här självstudierna bör du:

1. [Slutför identifierings självstudien](tutorial-discover-vmware.md) för att förbereda Azure och VMware för migrering.
2. Vi rekommenderar att du slutför den andra självstudien för att [utvärdera virtuella VMware-datorer](tutorial-assess-vmware.md) innan du migrerar dem till Azure.
3. Du har Azure PowerShell- `Az` modulen. Om du behöver installera eller uppgradera Azure PowerShell, följ den här [guiden för att installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="install-azure-migrate-powershell-module"></a>Installera Azure Migrate PowerShell-modul

Azure Migrate PowerShell-modulen är tillgänglig som för hands version. Du måste installera PowerShell-modulen med hjälp av följande kommando. 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Logga in på din Microsoft Azure prenumeration

Logga in på din Azure-prenumeration med `Connect-AzAccount` cmdleten.

```azurepowershell
Connect-AzAccount
```

Välj din Azure-prenumeration. Använd `Get-AzSubscription` cmdleten för att hämta listan över Azure-prenumerationer som du har åtkomst till. Välj den Azure-prenumeration där ditt Azure Migrate projekt fungerar med hjälp av `Set-AzContext` cmdleten.

```azurepowershell
Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
```

## <a name="retrieve-the-azure-migrate-project"></a>Hämta Azure Migrate-projektet

Ett Azure Migrate-projekt används för att lagra identifierings-, utvärderings-och migrerings-metadata som samlas in från den miljö som du bedömer eller migrerar.
I ett projekt kan du spåra identifierade till gångar, dirigera utvärderingar och utföra migreringar.

Som en del av förutsättningarna skulle du redan ha skapat ett Azure Migrate-projekt. Använd `Get-AzMigrateProject` cmdleten för att hämta information om ett Azure Migrate-projekt. Du måste ange namnet på Azure Migrate-projektet ( `Name` ) och namnet på Azure Migrate projektets resurs grupp ( `ResourceGroupName` ).

```azurepowershell
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name "MyResourceGroup"

# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name "MyMigrateProject" -ResourceGroupName $ResourceGroup.ResourceGroupName

# View Azure Migrate project details
$MigrateProject | ConvertTo-JSON
```

## <a name="retrieve-discovered-vms-in-an-azure-migrate-project"></a>Hämta identifierade virtuella datorer i ett Azure Migrate-projekt

Azure Migrate använder en förenklad [Azure Migrate-apparat](migrate-appliance-architecture.md). Som en del av förutsättningarna skulle du ha distribuerat Azure Migrate-installationen som en virtuell VMware-dator.

Om du vill hämta en enskild virtuell VMware-dator i ett Azure Migrate-projekt anger du namnet på Azure Migrate projektet ( `ProjectName` ), resurs gruppen för Azure Migrate-projektet ( `ResourceGroupName` ) och namnet på den virtuella datorn ( `DisplayName` ). 

```azurepowershell
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName "MyTestVM"

# View discovered server details
$DiscoveredServer | ConvertTo-JSON
```
Vi migrerar den här virtuella datorn som en del av den här självstudien.

Du kan också hämta alla virtuella VMware-datorer i ett Azure Migrate-projekt med hjälp av `ProjectName` `ResourceGroupName` parametrarna och.

```azurepowershell
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName 
```
Om du har flera enheter i ett Azure Migrate-projekt kan du använda `ProjectName` `ResourceGroupName` parametrarna, och `ApplianceName` för att hämta alla virtuella datorer som har identifierats med en speciell Azure Migrate-installation. 

```azurepowershell
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName "MyMigrateAppliance" |Format-Table DisplayName, Name, Type

```

## <a name="initialize-replication-infrastructure"></a>Initiera infrastruktur för replikering

[Azure Migrate: Server migreringen](migrate-services-overview.md#azure-migrate-server-migration-tool) utnyttjar flera Azure-resurser för att migrera virtuella datorer. Server migrationen etablerar följande resurser i samma resurs grupp som projektet.

- **Service Bus**: Server-migreringen använder Service Bus för att skicka meddelanden om dirigering av replikering till enheten.
- **Gateway Storage-konto**: Server Migration använder Gateway Storage-kontot för att lagra statusinformation om de virtuella datorer som replikeras.
- **Logg lagrings konto**: Azure Migrate-installationen överför replik loggar för virtuella datorer till ett logg lagrings konto. Azure Migrate använder replikeringsinformation på de replik hanterade diskarna.
- **Nyckel valv**: Azure Migrates enheten använder nyckel valvet för att hantera anslutnings strängar för Service Bus och åtkomst nycklar för de lagrings konton som används i replikeringen.

Innan du replikerar den första virtuella datorn i Azure Migrate-projektet kör du följande skript för att etablera infrastrukturen för replikering. Det här skriptet etablerar och konfigurerar de ovannämnda resurserna så att du kan börja migrera dina virtuella VMware-datorer.

> [!NOTE]
> Ett Azure Migrate projekt har endast stöd för migrering till en Azure-region. När du har kört det här skriptet kan du inte ändra den mål region dit du vill migrera dina virtuella VMware-datorer.
> Du måste köra `Initialize-AzMigrateReplicationInfrastructure` skriptet om du konfigurerar en ny installation i Azure Migrate-projektet. 

I artikeln initierar vi infrastrukturen för replikering så att vi kan migrera våra virtuella datorer till `Central US` regionen. Du kan [Hämta filen](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) från GitHub-lagringsplatsen eller köra den med hjälp av följande kodfragment. 

```azurepowershell
# Download the script from Azure Migrate GitHub repository 
Invoke-WebRequest https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-migrate/migrate-at-scale-vmware-agentles/Initialize-AzMigrateReplicationInfrastructure.ps1 -OutFile .\AzMigrateReplicationinfrastructure.ps1

# Run the script for initializing replication infrastructure for the current Migrate project
.\AzMigrateReplicationInfrastructure.ps1 -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject.Name -Scenario agentlessVMware -TargetRegion "CentralUS" 
```


## <a name="replicate-vms"></a>Replikera virtuella datorer

När du har slutfört identifieringen och initierat replikeringen kan du påbörja replikering av virtuella VMware-datorer till Azure. Du kan köra upp till 300 replikeringar samtidigt.

Du kan ange egenskaperna för replikering enligt följande.

- **Mål prenumeration och resurs grupp** – ange den prenumeration och resurs grupp som den virtuella datorn ska migreras till genom att ange resurs grupps-ID: t med hjälp av `TargetResourceGroupId` parametern. 
- **Målets virtuella nätverk och undernät** – ange ID: t för Azure-Virtual Network och namnet på under nätet som den virtuella datorn ska migreras till med `TargetNetworkId` respektive `TargetSubnetName` parametrar. 
- **Namn på virtuell måldator** – ange namnet på den virtuella Azure-dator som ska skapas med hjälp av `TargetVMName` parametern.
- **Storlek på virtuell måldator** – ange storleken på den virtuella Azure-datorn som ska användas för den REPLIKERADE virtuella datorn med hjälp av `TargetVMSize` parametern. Om du till exempel vill migrera en virtuell dator till D2_v2 virtuell dator i Azure anger du värdet `TargetVMSize` som "Standard_D2_v2".  
- **Licens** – om du vill använda Azure Hybrid-förmån för dina Windows Server-datorer som omfattas av aktiva Software Assurance-eller Windows Server-prenumerationer anger du värdet för `LicenseType` parametern "AHUB". Annars anger du värdet för `LicenseType` parametern "NoLicenseType".
- **OS-disk** – ange den unika identifieraren för den disk som innehåller operativ systemets start program och installations program. Det disk-ID som ska användas är egenskapen unik identifierare (UUID) för disken som hämtats med `Get-AzMigrateServer` cmdleten.
- **Typ av disk** – ange värdet för `DiskType` parametern enligt följande.
    - Om du vill använda Premium-hanterade diskar anger du "Premium_LRS" som värde för `DiskType` parametern. 
    - Om du vill använda standard SSD-diskar anger du "StandardSSD_LRS" som värde för `DiskType` parametern. 
    - Om du vill använda standard diskar för hård diskar anger du "Standard_LRS" som värde för `DiskType` parametern. 
- **Redundans för infrastruktur** – ange alternativ för infrastrukturens redundans enligt följande. 
    - Tillgänglighets zon för att fästa den migrerade datorn i en angiven tillgänglighets zon i regionen. Använd det här alternativet för att distribuera servrar som utgör en program nivå med flera noder i Tillgänglighetszoner. Det här alternativet är bara tillgängligt om det valda mål området för migreringen stöder Tillgänglighetszoner. Om du vill använda tillgänglighets zoner anger du värdet tillgänglighets zon för `TargetAvailabilityZone` parametern.
    - Tillgänglighets uppsättning för att placera den migrerade datorn i en tillgänglighets uppsättning. Den valda mål resurs gruppen måste ha en eller flera tillgänglighets uppsättningar för att kunna använda det här alternativet. Ange tillgänglighets uppsättnings-ID för parametern om du vill använda tillgänglighets uppsättning `TargetAvailabilitySet` . 

I den här självstudien replikerar vi alla diskar för den identifierade virtuella datorn och anger ett nytt namn för den virtuella datorn i Azure. Vi anger den första disken i den identifierade servern som OS-disk och migrerar alla diskar som Standard HDD. Operativsystemdisken är den disk där operativsystemets bootloader och installationsprogram finns.

```azurepowershell
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType "Standard_LRS" -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

Du kan också selektivt replikera diskarna för den identifierade virtuella datorn med hjälp av `New-AzMigrateDiskMapping` cmdlet och tillhandahålla den som inmatad till `DiskToInclude` parametern i `New-AzMigrateServerReplication` cmdleten. Du kan också använda `New-AzMigrateDiskMapping` cmdlet för att ange olika mål disk typer för varje enskild disk som ska replikeras. 

Ange värden för följande parametrar för `New-AzMigrateDiskMapping` cmdleten.

- **DiskId** – ange den unika identifieraren för disken som ska migreras. Det disk-ID som ska användas är egenskapen unik identifierare (UUID) för disken som hämtats med `Get-AzMigrateServer` cmdleten.  
- **IsOSDisk** – ange "true" om disken som ska migreras är den virtuella datorns OS-disk, annars "false".
- **DiskType** – ange vilken typ av disk som ska användas i Azure. 

I följande exempel replikerar vi bara två diskar av den identifierade virtuella datorn. vi anger OS-disken och använder olika disk typer för varje disk som ska replikeras.

```azurepowershell
# View disk details of the discovered server
$DiscoveredServer.Disk | ConvertTo-JSON

# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType "StandardSSD_LRS" -IsOSDisk "true"
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType "Premium_LRS" -IsOSDisk "false"

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk 

# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

## <a name="monitor-replication"></a>Övervaka replikering 

Replikeringen sker på följande sätt:

- När jobbet starta replikeringen har slutförts påbörjar datorerna sin inledande replikering till Azure.
- Under den inledande replikeringen skapas en VM-ögonblicksbild. Disk data från ögonblicks bilden replikeras till replikbaserade diskar i Azure.
- När den inledande replikeringen har slutförts börjar delta-replikeringen. Stegvisa ändringar av lokala diskar replikeras regelbundet till replik diskarna i Azure.

Spåra status för replikeringen med hjälp av `Get-AzMigrateServerReplication` cmdleten. 

> [!NOTE]
> Identifierat VM-ID och replikering av VM-ID: n är två olika unika identifierare. Båda dessa identifierare kan användas för att hämta information om en replikerande Server.  

### <a name="monitor-replication-using-discovered-vm-identifier"></a>Övervaka replikering med identifierat VM-ID
```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID
```

### <a name="monitor-replication-using-replicating-vm-identifier"></a>Övervaka replikering med replikering av VM-ID

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id 
```

Du kan spåra egenskaperna "migration State" och "Beskrivning av migrations status" i utdata. 
- För den inledande replikeringen är värdena för egenskaperna för migreringens läge och beskrivning för migreringen "InitialSeedingInProgress" respektive "inledande replikering". 
- Under delta-replikering blir värdena för egenskaper för migrera tillstånd och beskrivning för migreringen "replikeras" och "redo att migrera".
- När du har slutfört migreringen blir värdena för egenskaperna migrera tillstånd och beskrivning för migreringen "migreringen lyckades" och "migreras".

```Output
AllowedOperation            : {DisableMigration, TestMigrate, Migrate}
CurrentJobId                : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationJobs/None
CurrentJobName              : None
CurrentJobStartTime         : 1/1/1753 1:01:01 AM
EventCorrelationId          : 9d435c55-4660-41a5-a8ed-dd74213d85fa
Health                      : Normal
HealthError                 : {}
Id                          : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationFabrics/xxx/replicationProtectionContainers/xxx/
                              replicationMigrationItems/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-
                              39b2-1e14-f90584522703
LastTestMigrationStatus     :
LastTestMigrationTime       :
Location                    :
MachineName                 : MyTestVM
MigrationState              : InitialSeedingInProgress
MigrationStateDescription   : Initial replication
Name                        : 10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-39b2-1e14-f90584522703
PolicyFriendlyName          : xxx
PolicyId                    : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationPolicies/xxx
ProviderSpecificDetail      : Microsoft.Azure.PowerShell.Cmdlets.Migrate.Models.Api20180110.VMwareCbtMigrationDetails
TestMigrateState            : None
TestMigrateStateDescription : None
Type                        : Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems
```

Om du vill ha mer information om replikeringens förlopp kör du följande cmdlet.

```azurepowershell
$replicatingserver.ProviderSpecificDetail | convertto-json
```
Du kan spåra det inledande replikerings förloppet med hjälp av egenskaperna "inledande initierings förlopp i procent" i utdata.

```output
    "DataMoverRunAsAccountId": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OffAzure/VMwareSites/xxx/runasaccounts/xxx",
    "FirmwareType":  "BIOS",
    "InitialSeedingProgressPercentage": 20,
    "InstanceType":  "VMwareCbt",
    "LastRecoveryPointReceived":  "\/Date(1601733591427)\/",
    "LicenseType":  "NoLicenseType",
    "MigrationProgressPercentage":  null,
    "MigrationRecoveryPointId":  null,
    "OSType":  "Windows",
    "PerformAutoResync":  "true",
```

Replikeringen sker på följande sätt:

- När jobbet starta replikeringen har slutförts påbörjar datorerna sin inledande replikering till Azure.
- Under den inledande replikeringen skapas en VM-ögonblicksbild. Disk data från ögonblicks bilden replikeras till replikbaserade diskar i Azure.
- När den inledande replikeringen har slutförts börjar delta-replikeringen. Stegvisa ändringar av lokala diskar replikeras regelbundet till replik diskarna i Azure.

## <a name="update-properties-of-a-replicating-vm"></a>Uppdatera egenskaper för en replikerad virtuell dator

[Azure Migrate: med Server-migrering](migrate-services-overview.md#azure-migrate-server-migration-tool) kan du ändra mål egenskaper, till exempel namn, storlek, resurs grupp, NIC-konfiguration och så vidare, för en REPLIKERAD virtuell dator. 

```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic 
```
Följande egenskaper kan uppdateras för en virtuell dator.

- **VM-namn** – ange namnet på den virtuella Azure-dator som ska skapas med hjälp av `TargetVMName` parametern.
- **VM-storlek** – ange storleken på den virtuella Azure-datorn som ska användas för den REPLIKERADE virtuella datorn med hjälp av `TargetVMSize` parametern. Om du till exempel vill migrera en virtuell dator till D2_v2 virtuell dator i Azure anger du värdet `TargetVMSize` som "Standard_D2_v2".  
- **Virtual Network** -ange ID: t för den Azure-Virtual Network som den virtuella datorn ska migreras till med hjälp av `TargetNetworkId` parametern. 
- **Resurs grupp** – ange ID: t för den resurs grupp som den virtuella datorn ska migreras till genom att ange resurs grupps-ID: t med hjälp av `TargetResourceGroupId` parametern.
- **Nätverks gränssnitt** – NIC-konfiguration kan anges med `New-AzMigrateNicMapping` cmdleten. Objektet skickas sedan in en indatamängd till `NicToUpdate` parametern i `Set-AzMigrateServerReplication` cmdleten. 

    - **Ändra IP-allokering** – ange en statisk IP-adress för ett nätverkskort genom att ange IPv4-adressen som statisk IP för den virtuella datorn med `TargetNicIP` parametern. För att dynamiskt tilldela en IP-adress för ett nätverkskort, ange "Auto" som värde för `TargetNicIP` parametern.
    - Använd värdena "primär", "sekundär" eller "DoNotCreate" för `TargetNicSelectionType` parametern för att ange om nätverkskortet ska vara primärt, sekundärt eller inte ska skapas på den migrerade virtuella datorn. Det går bara att ange ett nätverkskort som primärt nätverkskort för den virtuella datorn. 
    - Om du vill göra ett primärt nätverkskort måste du också ange de andra nätverkskort som ska göras sekundära eller som inte ska skapas på den migrerade virtuella datorn.  
    - Om du vill ändra NÄTVERKSKORTets undernät anger du namnet på under nätet med hjälp av `TargetNicSubnet` parametern.

 - **Tillgänglighets zon** – om du vill använda tillgänglighets zoner anger du parameterns tillgänglighets zon `TargetAvailabilityZone` .
 - **Tillgänglighets uppsättning** – Använd tillgänglighets uppsättningen för att ange ID för tillgänglighets uppsättning för `TargetAvailabilitySet` parameter.

I följande exempel uppdaterar vi NIC-konfigurationen genom att göra det första NÄTVERKSKORTet som primärt och tilldela en statisk IP-adress till den. Vi tar bort det andra NÄTVERKSKORTet för migrering och uppdaterar namnet och storleken på den virtuella mål datorn. 

```azurepowershell
# Specify the NIC properties to be updated for a replicating VM. 
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP "xxx.xxx.xxx.xxx" -TargetNicSelectionType "Primary"
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType "DoNotCreate"

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2

# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize "Standard_DS13_v2" -TargetVMName "MyMigratedVM" -NicToUpdate $NicMapping
```

Du kan också lista alla replikerande servrar i ett Azure Migrate projekt och sedan använda den replikerade VM-identifieraren för att uppdatera VM-egenskaperna.

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id 
```


## <a name="run-a-test-migration"></a>Kör en testmigrering

När delta-replikering börjar kan du köra en testmigrering för de virtuella datorerna innan du kör en fullständig migrering till Azure. Vi rekommenderar starkt att du testar migreringen minst en gång för varje dator innan du migrerar den.

- Kör en test-migrering som ska fungera som förväntat. Testmigreringen påverkar inte den lokala datorn, som fortfarande fungerar och fortsätter att replikeras. 
- Testmigrering simulerar migreringen genom att skapa en virtuell Azure-dator med replikerade data (vanligt vis migrera till ett virtuellt nätverk som inte är i Azure-prenumerationen).
- Du kan använda den replikerade virtuella Azure-datorn för att verifiera migreringen, utföra app-testning och åtgärda eventuella problem före fullständig migrering.

Välj den Azure-Virtual Network som ska användas för testning genom att ange ID: t för det virtuella nätverket med hjälp av `TestNetworkID` parametern.

```azurepowershell
# Retrieve the Azure virtual network created for testing 
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork

# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id
```

När testet är klart kan du rensa testmigreringen med hjälp av `Start-AzMigrateTestMigrationCleanup` cmdleten.

```azurepowershell
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer
```

## <a name="migrate-vms"></a>Migrera virtuella datorer

När du har kontrollerat att testmigreringen fungerar som förväntat kan du migrera den replikerande servern med följande cmdlet.

```azurepowershell
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer 
```
Använd inte parametern om du inte vill stänga av käll servern `TurnOffSourceServer` .

## <a name="complete-the-migration"></a>Slutföra migreringen

1. När migreringen är klar stoppar du replikeringen för den lokala datorn och rensar replikeringstillståndet för den virtuella datorn med hjälp av följande cmdlet.

```azurepowershell
# Stop replication for a migrated server
$StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer 
```

2. Installera [Azure VM-](../virtual-machines/extensions/agent-windows.md) eller [Linux](../virtual-machines/extensions/agent-linux.md) -agenten på de migrerade datorerna.
3. Utför alla finjusteringar av appen efter migreringen som att uppdatera databasanslutningssträngar och webbserverkonfigurationer.
4. Utför slutlig program- och migreringsacceptanstestning på det migrerade programmet som nu körs i Azure.
5. Klipp ut över trafik till den migrerade virtuella Azure-instansen.
6. Ta bort de lokala virtuella datorerna från ditt lokala VM-inventarie.
7. Ta bort de lokala virtuella datorerna från lokala säkerhetskopior.
8. Uppdatera eventuell intern dokumentation för att ange den nya platsen och IP-adressen för de virtuella Azure-datorerna. 

## <a name="post-migration-best-practices"></a>Metod tips för efter migreringen

- För ökat skydd:
    - Skydda data genom att säkerhetskopiera virtuella Azure-datorer med Azure Backup-tjänsten. [Läs mer](../backup/quick-backup-vm-portal.md).
    - Håll arbetsbelastningar i körning och kontinuerligt tillgängliga genom att replikera virtuella Azure-datorer till en sekundär region med Site Recovery. [Läs mer](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- För ökad säkerhet:
    - Lås och begränsa inkommande trafik åtkomst med [Azure Security Center – just-in-Time-administration](../security-center/security-center-just-in-time.md).
    - Begränsa nätverkstrafik till hanteringsslutpunkter med [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).
    - Distribuera [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) för att säkra diskar och skydda data från stöld och obehörig åtkomst.
    - Läs mer om [ att skydda IaaS-resurser](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) och besök [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- För övervakning och hantering:
-  Överväg att distribuera [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) för att övervaka användning och utgifter.



