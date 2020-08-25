---
title: Ultra disks för virtuella datorer – Azure Managed disks
description: Läs mer om Ultra disks för virtuella Azure-datorer
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 05/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4f49d4e78cd58d16627a0b6fda1957baa9f1f085
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752079"
---
# <a name="using-azure-ultra-disks"></a>Använda Azure Ultra disks

Den här artikeln beskriver hur du distribuerar och använder en mycket stor disk, för konceptuell information om Ultra disks, se [vilka disk typer som är tillgängliga i Azure?](disks-types.md#ultra-disk).

Azure Ultra disks erbjuder högt data flöde, hög IOPS och konsekvent låg latens disk lagring för virtuella Azure IaaS-datorer (VM). Det nya erbjudandet ger överst i linje prestanda på samma tillgänglighets nivå som våra befintliga diskar. En stor fördel med Ultra disks är möjligheten att dynamiskt ändra prestanda för SSD tillsammans med dina arbets belastningar utan att behöva starta om dina virtuella datorer. Ultradiskar är lämpliga för dataintensiva arbetsbelastningar som SAP HANA, databaser på toppnivå och transaktionskrävande arbetsbelastningar.

## <a name="ga-scope-and-limitations"></a>Allmän omfattning och begränsningar

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Bestämma VM-storlek och region tillgänglighet

### <a name="vms-using-availability-zones"></a>Virtuella datorer med tillgänglighets zoner

Om du vill använda Ultra disks måste du bestämma vilken tillgänglighets zon du befinner dig i. Alla regioner stöder inte varje VM-storlek med Ultra disks. Du kan kontrol lera om din region, zon och VM-storlek stöder Ultra disks genom att köra något av följande kommandon, se till att ersätta **regions**-, **vmSize**-och **prenumerations** värden först:

#### <a name="cli"></a>CLI

```azurecli
subscription="<yourSubID>"
# example value is southeastasia
region="<yourLocation>"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

Svaret liknar det formulär som visas nedan, där X är den zon som ska användas för att distribuera i den valda regionen. X kan vara antingen 1, 2 eller 3.

Behåll **zonens värde,** det motsvarar din tillgänglighets zon och du behöver den för att kunna distribuera en Ultra-disk.

|ResourceType  |Namn  |Plats  |Zoner  |Begränsning  |Kapacitet  |Värde  |
|---------|---------|---------|---------|---------|---------|---------|
|disk     |UltraSSD_LRS         |usaöstra2         |X         |         |         |         |

> [!NOTE]
> Om det inte fanns något svar från kommandot, stöds inte den valda virtuella dator storleken med Ultra disks i den valda regionen.

Nu när du vet vilken zon du ska distribuera till, följer du distributions stegen i den här artikeln för att antingen distribuera en virtuell dator med en mycket disk ansluten eller ansluta en Ultra disk till en befintlig virtuell dator.

### <a name="vms-with-no-redundancy-options"></a>Virtuella datorer utan redundans alternativ

Ultra disks som distribueras i västra USA måste distribueras utan några alternativ för redundans för tillfället. Alla disk storlekar som stöder Ultra disks kan dock vara i den här regionen. Du kan ta reda på vilka som har stöd för Ultra disks i västra USA genom att använda något av följande kodfragment. Se till att ersätta `vmSize` värdena och `subscription` innan värdena:

```azurecli
subscription="<yourSubID>"
region="westus"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

Svaret liknar följande formulär, `UltraSSDAvailable   True` anger om den virtuella dator storleken har stöd för Ultra disks i den här regionen.

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Distribuera en Ultra disk med Azure Resource Manager

Ta först reda på vilken VM-storlek som ska distribueras. En lista över VM-storlekar som stöds finns i avsnittet allmän [omfattning och begränsningar](#ga-scope-and-limitations) .

Om du vill skapa en virtuell dator med flera Ultra disks, se exemplet [skapa en virtuell dator med flera Ultra-diskar](https://aka.ms/ultradiskArmTemplate).

Om du avser att använda din egen mall kontrollerar du att **API version** för `Microsoft.Compute/virtualMachines` och `Microsoft.Compute/Disks` har angetts som `2018-06-01` (eller senare).

Ange disk-SKU: n till **UltraSSD_LRS**, ange disk kapacitet, IOPS, tillgänglighets zon och data flöde i Mbit/s för att skapa en Ultra-disk.

När den virtuella datorn har allokerats kan du partitionera och formatera data diskarna och konfigurera dem för dina arbets belastningar.


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Distribuera en Ultra disk med hjälp av Azure Portal

Det här avsnittet beskriver hur du distribuerar en virtuell dator som är utrustad med en Ultra disk som en data disk. Det förutsätter att du har erfarenhet av att distribuera en virtuell dator, om du inte gör det, se vår [snabb start: skapa en virtuell Windows-dator i Azure Portal](~/articles/virtual-machines/windows/quick-create-portal.md).

- Logga in på [Azure Portal](https://portal.azure.com/) och navigera för att distribuera en virtuell dator (VM).
- Se till att välja en [VM-storlek och-region som stöds](#ga-scope-and-limitations).
- Välj **tillgänglighets zon** i **tillgänglighets alternativ**.
- Fyll i de återstående posterna med val som du väljer.
- Välj **Diskar**.

![create-ultra-disk-enabled-vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- På bladet diskar väljer du **Ja** för **att aktivera Ultra disk Compatibility**.
- Välj **skapa och Anslut en ny disk** för att ansluta en Ultra disk nu.

![enable-and-attach-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- På bladet **skapa en ny disk** anger du ett namn och väljer sedan **ändra storlek**.
- Ändra **konto typen** till **Ultra disk**.
- Ändra värdena för **anpassad disk storlek (GIB)**, **disk-IOPS**och **disk data flöde** som du väljer själv.
- Välj **OK** på båda bladen.
- Fortsätt med distributionen av virtuella datorer. det är detsamma som du distribuerar andra virtuella datorer.

![create-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Ansluta en Ultra disk med hjälp av Azure Portal

Alternativt, om din befintliga virtuella dator finns i en region/tillgänglighets zon som kan använda Ultra disks, kan du använda Ultra disks utan att behöva skapa en ny virtuell dator. Aktivera Ultra disks på den befintliga virtuella datorn genom att ansluta dem som data diskar.

- Gå till den virtuella datorn och välj **diskar**.
- Välj **Redigera**.

![options-selector-ultra-disks.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Välj **Ja** om du vill **Aktivera Ultra disk Compatibility**.

![ultra-options-yes-enable.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Välj **Spara**.
- Välj **Lägg till datadisk** och välj sedan **skapa disk**i list rutan för **namn** .

![create-and-attach-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Fyll i ett namn för din nya disk och välj sedan **ändra storlek**.
- Ändra **konto typen** till **Ultra disk**.
- Ändra värdena för **anpassad disk storlek (GIB)**, **disk-IOPS**och **disk data flöde** som du väljer själv.
- Välj **OK** och välj sedan **skapa**.

![making-a-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- När du har kommit tillbaka till din disks blad väljer du **Spara**.

![saving-and-attaching-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>Justera prestanda för en Ultra disk med hjälp av Azure Portal

Ultra disks erbjuder en unik funktion som gör att du kan justera prestandan. Du kan göra dessa justeringar från Azure Portal på själva diskarna.

- Gå till den virtuella datorn och välj **diskar**.
- Välj den Ultra disk som du vill ändra prestanda för.

![selecting-ultra-disk-to-modify.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Välj **konfiguration** och gör dina ändringar.
- Välj **Spara**.

![configuring-ultra-disk-performance-and-size.png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>Distribuera en Ultra disk med CLI

Ta först reda på vilken VM-storlek som ska distribueras. I avsnittet allmän [omfattning och begränsningar](#ga-scope-and-limitations) finns en lista över VM-storlekar som stöds.

Du måste skapa en virtuell dator som kan använda Ultra disks för att ansluta en Ultra disk.

Ersätt eller ange **$VMName**, **$rgname**, **$diskname**, **$location**, **$Password**, **$User** variabler med dina egna värden. Ange **$Zone**  till värdet för din tillgänglighets zon som du fick från [början av den här artikeln](#determine-vm-size-and-region-availability). Kör sedan följande CLI-kommando för att skapa en ultra-aktiverad virtuell dator:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Aktivera Ultra disk-kompatibilitet på en befintlig virtuell dator

Om din virtuella dator uppfyller de krav som beskrivs i allmän [omfattning och begränsningar](#ga-scope-and-limitations) och är i [lämplig zon för ditt konto](#determine-vm-size-and-region-availability), kan du aktivera Ultra disk-kompatibilitet på den virtuella datorn.

Om du vill aktivera Ultra disk-kompatibilitet måste du stoppa den virtuella datorn. När du har stoppat den virtuella datorn kan du aktivera kompatibilitet, ansluta en Ultra disk och sedan starta om den virtuella datorn:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk-using-cli"></a>Skapa en Ultra disk med CLI

Nu när du har en virtuell dator som kan ansluta till Ultra disks kan du skapa och ansluta en Ultra disk till den.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Ansluta en Ultra disk till en virtuell dator med CLI

Alternativt, om din befintliga virtuella dator finns i en region/tillgänglighets zon som kan använda Ultra disks, kan du använda Ultra disks utan att behöva skapa en ny virtuell dator.

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
diskName="<yourDiskName>"
subscriptionId="<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Justera prestanda för en Ultra disk med CLI

Ultra disks är en unik funktion som du kan använda för att justera prestandan, följande kommando visar hur du använder den här funktionen:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Distribuera en Ultra disk med PowerShell

Ta först reda på vilken VM-storlek som ska distribueras. I avsnittet allmän [omfattning och begränsningar](#ga-scope-and-limitations) finns en lista över VM-storlekar som stöds.

Om du vill använda Ultra disks måste du skapa en virtuell dator som kan använda Ultra disks. Ersätt eller ange **$resourcegroup** och **$vmName** variabler med dina egna värden. Ange **$Zone** till värdet för din tillgänglighets zon som du fick från [början av den här artikeln](#determine-vm-size-and-region-availability). Kör sedan följande [New-AzVm-](/powershell/module/az.compute/new-azvm) kommando för att skapa en ultra-aktiverad virtuell dator:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD $true `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Aktivera Ultra disk-kompatibilitet på en befintlig virtuell dator

Om din virtuella dator uppfyller de krav som beskrivs i allmän [omfattning och begränsningar](#ga-scope-and-limitations) och är i [lämplig zon för ditt konto](#determine-vm-size-and-region-availability), kan du aktivera Ultra disk-kompatibilitet på den virtuella datorn.

Om du vill aktivera Ultra disk-kompatibilitet måste du stoppa den virtuella datorn. När du har stoppat den virtuella datorn kan du aktivera kompatibilitet, ansluta en Ultra disk och sedan starta om den virtuella datorn:

```azurepowershell
#stop the VM
$vm1 = Get-AzureRMVM -name $vmName -ResourceGroupName $rgName
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled 1
#start the VM
```

### <a name="create-an-ultra-disk-using-powershell"></a>Skapa en Ultra disk med PowerShell

Nu när du har en virtuell dator som kan använda Ultra disks kan du skapa och ansluta en Ultra disk till den:

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Ansluta en Ultra disk till en virtuell dator med hjälp av PowerShell

Alternativt, om din befintliga virtuella dator finns i en region/tillgänglighets zon som kan använda Ultra disks, kan du använda Ultra disks utan att behöva skapa en ny virtuell dator.

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Justera prestanda för en Ultra disk med hjälp av PowerShell

Ultra disks har en unik funktion som gör att du kan justera prestandan, följande kommando är ett exempel som justerar prestandan utan att behöva koppla från disken:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```
## <a name="next-steps"></a>Nästa steg

Se [använda Azure Ultra disks på Azure Kubernetes service (för hands version)](../aks/use-ultra-disks.md).
