---
title: Självstudiekurs – hantera Azure-diskar med Azure CLI
description: I den här självstudien lär du dig hur du använder Azure CLI för att skapa och hantera Azure-diskar för virtuella datorer
author: cynthn
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 11/14/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.subservice: disks
ms.openlocfilehash: 5ebb3883304584570759ea02a2de7187efcdaf26
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718687"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Självstudiekurs – hantera Azure-diskar med Azure CLI

Virtuella Azure-datorer (VM) använder diskar för att lagra de virtuella datorernas operativsystem, program och data. När du skapar en virtuell dator är det viktigt att välja en disk storlek och konfiguration som är lämplig för den förväntade arbets belastningen. Den här kursen visar hur du distribuerar och hanterar diskar för virtuella datorer. Du får lära dig om:

> [!div class="checklist"]
> * OS-diskar och temporära diskar
> * Datadiskar
> * Standard- och Premium-diskar
> * Diskprestanda
> * Ansluta och förbereda datadiskar
> * Ögonblicksbilder


## <a name="default-azure-disks"></a>Azure-standarddiskar

När en virtuell Azure-dator skapas kopplas två diskar automatiskt till den virtuella datorn.

**Operativsystemdisken** – Operativsystemdiskar kan vara upp till 2 TB och innehåller de virtuella datorernas operativsystem. Operativsystemdisken kallas som standard */dev/sda*. OS-diskens cachelagringkonfiguration har optimerats för OS-prestanda. Därför bör OS-disken **inte** användas för program eller data. För program och data använder du datadiskar som beskrivs senare i den här guiden.

**Temporär disk** – Temporära diskar använder en SSD-enhet som finns på samma Azure-värd som den virtuella datorn. Temporära diskar har höga prestanda och kan användas för åtgärder som till exempel tillfällig databearbetning. Men om den virtuella datorn flyttas till en ny värd tas alla data som är lagrade på den temporära disken bort. Storleken på den temporära disken bestäms av VM-storleken. Temporära diskar kallas */dev/sdb* och har monteringspunkten */mnt*.

## <a name="azure-data-disks"></a>Azure-datadiskar

Du kan lägga till ytterligare datadiskar om du behöver installera program och lagra data. Datadiskar används när du behöver hållbar och responsiv datalagring. Storleken på den virtuella datorn avgör hur många datadiskar som kan kopplas till en virtuell dator.

## <a name="vm-disk-types"></a>VM-disktyper

Azure tillhandahåller två disktyper.

**Standard-diskar** – Stöds av hårddiskar och levererar kostnadseffektiv lagring samtidigt som det är högpresterande. Standarddiskar passar perfekt för kostnadseffektiv utveckling och testning av arbetsbelastningar.

**Premium diskar** – backas upp av SSD-baserade diskar med hög prestanda och låg latens. Passar perfekt för virtuella datorer som kör produktionsarbetsbelastningar. VM-storlekar med ett  **S** i [storleks namnet](../vm-naming-conventions.md), vanligt vis stöder Premium Storage. Till exempel DS-serien, DSv2-serien, GS-serien och FS-seriens virtuella datorer stöder Premium Storage. När du väljer en diskstorlek, avrundas värdet uppåt till nästa typ. Om disk storleken till exempel är större än 64 GB men mindre än 128 GB är disk typen P10. 

<br>

[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

När du etablerar en Premium Storage-disk, till skillnad från standard lagring, garanterar du att disken har kapacitet, IOPS och data flöde. Om du till exempel skapar en P50-disk, etablerar Azure 4 095-GB lagrings kapacitet, 7 500 IOPS och 250 MB/s genom strömning för disken. Programmet kan använda hela eller delar av kapaciteten och prestandan. Premium SSD diskar har utformats för att tillhandahålla låg latens i millisekunder och mål-IOPS och data flöde som beskrivs i föregående tabell 99,9% av tiden.

I tabellen ovan visas högsta IOPS per disk, men högre prestanda kan uppnås genom strimling över flera datadiskar. Du kan till exempel koppla 64 datadiskar till en virtuell Standard GS5-dator. Om var och en av dessa diskar har storleken P30 kan du ha högst 80 000 IOPS. Mer information om högsta IOPS per VM finns i [VM-typer och storlekar](../sizes.md).

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du **testa den** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/bash) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="create-and-attach-disks"></a>Skapa och koppla diskar

Datadiskar kan skapas och kopplas när den virtuella datorn skapas eller kopplas till en befintlig virtuell dator.

### <a name="attach-disk-at-vm-creation"></a>Koppla diskar när den virtuella datorn skapas

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create).

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm#az-vm-create). Följande exempel skapar virtuell dator med namnet *myVM*, lägger till ett användarkonto med namnet *azureuser* och genererar SSH-nycklar om de inte redan finns. Argumentet `--datadisk-sizes-gb` används för att ange att ytterligare en disk ska skapas och kopplas till den virtuella datorn. Om du vill skapa och koppla fler än en disk använder du en lista med diskstorlekar separerade med mellanslag. I följande exempel skapas en virtuell dator med två datadiskar som båda är 128 GB. Eftersom diskstorleken är 128 GB konfigureras båda diskarna som P10, vilket ger maximalt 500 IOPS per disk.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Koppla diskar till befintliga virtuella datorer

Skapa och koppla en ny disk till en befintlig virtuell dator med kommandot [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach). Följande exempel skapar en premiumdisk på 128 gigabyte och kopplar den till den virtuella dator som skapades i föregående steg.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --name myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Förbereda datadiskar

När en disk har kopplats till den virtuella datorn måste operativsystemet konfigureras för att använda disken. I följande exempel visas hur du manuellt konfigurerar en disk. Den här processen kan även automatiseras med cloud-init som beskrivs i en [senare självstudiekurs](./tutorial-automate-vm-deployment.md).


Skapa en SSH-anslutning med den virtuella datorn. Ersätt exempel-IP-adressen med den offentliga IP-adressen för den virtuella datorn.

```console
ssh 10.101.10.10
```

Partitionera disken med `fdisk`.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Skapa ett filsystem på partitionen med kommandot `mkfs`.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Montera den nya disken så den är tillgänglig i operativsystemet.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

Disken kan nu kommas åt via monteringspunkten *datadrive* som kan verifieras med kommandot `df -h`.

```bash
df -h
```

Utdata visar att den nya disken är monterad på */datadrive*.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

För att säkerställa att disken monteras efter en omstart måste den läggas till i filen */etc/fstab*. För att göra detta hämtar du diskens UUID med verktyget `blkid`.

```bash
sudo -i blkid
```

Utdata visar diskens UUID, i det här fallet `/dev/sdc1`.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Lägg till en rad som liknar denna i filen */etc/fstab*.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail   1  2
```

Disken har konfigurerats, stäng SSH-sessionen.

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>Ta en ögonblicks bild av disken

När du tar en ögonblicksbild skapar Azure en skrivskyddad kopia av disken vid en viss tidpunkt. Ögonblicksbilder av virtuella Azure-datorer är användbara för att snabbt spara tillståndet hos en virtuell dator innan ändringar i konfigurationen. Om det uppstår ett problem eller fel kan den virtuella datorn återställas med en ögonblicksbild. När en virtuell dator har mer än en disk tas en ögonblicksbild av varje disk oberoende av de andra. Om programkonsekventa säkerhetskopior krävs bör den virtuella datorn stoppas innan ögonblicksbilder tas. Ett annat alternativ är att använda [Azure Backup-tjänsten](../../backup/index.yml) som ger möjlighet till automatiserade säkerhetskopior medan den virtuella datorn körs.

### <a name="create-snapshot"></a>Skapa en ögonblicksbild

Du behöver diskens ID eller namn för att skapa en ögonblicksbild av en virtuell dator. Använd kommandot [AZ VM show](/cli/azure/vm#az-vm-show) för att returnera disk-ID: t. I det här exemplet sparas diskens ID i en variabel så det kan användas i ett senare steg.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

När du har diskens ID skapar du en ögonblicksbild av disken med följande kommando.

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Skapa en disk från en ögonblicksbild

Ögonblicksbilden kan omvandlas till en disk som kan användas för att återskapa den virtuella datorn.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Återställa en virtuell dator från en ögonblicksbild

För att demonstrera återställning av en virtuell dator tar du bort den virtuella datorn.

```azurecli-interactive
az vm delete \
--resource-group myResourceGroupDisk \
--name myVM
```

Skapa en ny virtuell dator från ögonblicksbilddisken.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>Koppla datadisken

Alla datadiskar måste kopplas till den virtuella datorn.

Ta först reda på datadiskens namn med kommandot [az disk list](/cli/azure/disk#az-disk-list). Det här exemplet sparar diskens namn i variabeln *datadisk* som används i nästa steg.

```azurecli-interactive
datadisk=$(az disk list \
   -g myResourceGroupDisk \
   --query "[?contains(name,'myVM')].[id]" \
   -o tsv)
```

Använd kommandot [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) för att koppla disken till den virtuella datorn.

```azurecli-interactive
az vm disk attach \
   –g myResourceGroupDisk \
   --vm-name myVM \
   --name $datadisk
```

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig mer om VM-diskar, till exempel:

> [!div class="checklist"]
> * OS-diskar och temporära diskar
> * Datadiskar
> * Standard- och Premium-diskar
> * Diskprestanda
> * Ansluta och förbereda datadiskar
> * Ögonblicksbilder

Gå vidare till nästa självstudie om du vill lära dig hur du automatiserar konfigurationen av virtuella datorer.

> [!div class="nextstepaction"]
> [Automatisera konfiguration av virtuella datorer](./tutorial-automate-vm-deployment.md)
