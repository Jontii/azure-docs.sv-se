---
title: Samla in information om alla virtuella datorer i en prenumeration med PowerShell
description: Samla in information om alla virtuella datorer i en prenumeration med PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: v-miegge
manager: dcscontentpm
editor: v-miegge
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/01/2019
ms.author: v-miegge
ms.custom: mvc
ms.openlocfilehash: 8d61da9c3a3d575fcbce0b77e1f8b2684df58106
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87171696"
---
# <a name="collect-details-about-all-vms-in-a-subscription-with-powershell"></a>Samla in information om alla virtuella datorer i en prenumeration med PowerShell

Det här skriptet skapar en CSV som innehåller namnet på den virtuella datorn, resurs gruppens namn, region, VM-storlek, Virtual Network, undernät, privat IP-adress, OS-typ och den offentliga IP-adressen för de virtuella datorerna i den angivna prenumerationen.

Om du inte har en [Azure-prenumeration](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="sample-script"></a>Exempelskript

```azurepowershell-interactive
#Provide the subscription Id where the VMs reside
$subscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

#Provide the name of the csv file to be exported
$reportName = "myReport.csv"

Select-AzSubscription $subscriptionId
$report = @()
$vms = Get-AzVM
$publicIps = Get-AzPublicIpAddress 
$nics = Get-AzNetworkInterface | ?{ $_.VirtualMachine -NE $null} 
foreach ($nic in $nics) { 
    $info = "" | Select VmName, ResourceGroupName, Region, VmSize, VirtualNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress, NicName, ApplicationSecurityGroup 
    $vm = $vms | ? -Property Id -eq $nic.VirtualMachine.id 
    foreach($publicIp in $publicIps) { 
        if($nic.IpConfigurations.id -eq $publicIp.ipconfiguration.Id) {
            $info.PublicIPAddress = $publicIp.ipaddress
            } 
        } 
        $info.OsType = $vm.StorageProfile.OsDisk.OsType 
        $info.VMName = $vm.Name 
        $info.ResourceGroupName = $vm.ResourceGroupName 
        $info.Region = $vm.Location 
        $info.VmSize = $vm.HardwareProfile.VmSize
        $info.VirtualNetwork = $nic.IpConfigurations.subnet.Id.Split("/")[-3] 
        $info.Subnet = $nic.IpConfigurations.subnet.Id.Split("/")[-1] 
        $info.PrivateIpAddress = $nic.IpConfigurations.PrivateIpAddress 
        $info.NicName = $nic.Name 
        $info.ApplicationSecurityGroup = $nic.IpConfigurations.ApplicationSecurityGroups.Id 
        $report+=$info 
    } 
$report | ft VmName, ResourceGroupName, Region, VmSize, VirtualNetwork, Subnet, PrivateIpAddress, OsType, PublicIPAddress, NicName, ApplicationSecurityGroup 
$report | Export-CSV "$home/$reportName"
```

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder följande kommandon för att skapa en CSV-export av information om virtuella datorer i en prenumeration. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

|Kommando|Obs!|
|-|-|
|[Select-AzSubscription](/powershell/module/az.accounts/set-azcontext)|Anger klienten, prenumerationen och miljön för de cmdletar som ska användas i den aktuella sessionen.|
|[Get-AzVM](/powershell/module/az.compute/get-azvm)|Hämtar egenskaperna för en virtuell dator.|
|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)|Hämtar en offentlig IP-adress.|
|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface)|Hämtar ett nätverks gränssnitt.|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/powershell-samples.md?toc=/azure/virtual-machines/windows/toc.json).
