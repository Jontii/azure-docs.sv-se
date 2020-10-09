---
title: Självstudie – Skapa virtuella datorer som kör en SQL-, IIS-, .NET-stack i Azure
description: I den här självstudien lär du dig hur du installerar Azure SQL, IIS, .NET-stacken på en virtuell Windows-dator i Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1c53194bd345c18ac582acd538f1e8f8e1e34d54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87027860"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>Självstudie: Installera SQL-, IIS-, .NET-stacken på en virtuell Windows-dator med Azure PowerShell

I den här självstudien installerar vi en SQL-, IIS-, .NET-stack med Azure PowerShell. Stacken består av två virtuella datorer som kör Windows Server 2016, en med IIS och .NET och den andra med SQL Server.

> [!div class="checklist"]
> * Skapa en virtuell dator 
> * Installera IIS och .NET Core SDK på den virtuella datorn
> * Skapa en virtuell dator som kör SQL Server
> * Installera SQL Server-tillägget

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="create-an-iis-vm"></a>Skapa en virtuell IIS-dator 

I det här exemplet använder vi cmdleten [New-AzVM](/powershell/module/az.compute/new-azvm) i PowerShell Cloud Shell för att snabbt skapa en virtuell dator i Windows Server 2016 och sedan installera IIS och .NET Framework. De virtuella IIS- och SQL-datorerna delar en resursgrupp och ett virtuellt nätverk, så vi skapar variabler för de namnen.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

Installera IIS och .NET Framework med det anpassade skripttillägget med cmdleten [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension).

```azurepowershell-interactive
Set-AzVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Skapa ett nytt undernät

Skapa ett till undernät för den virtuella SQL-datorn. Hämta vNet med hjälp av [Get-AzVirtualNetwork]{/powershell/module/az.network/get-azvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Skapa en konfiguration för undernätet med hjälp av [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Uppdatera vNet med den nya undernätsinformationen med hjälp av [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)
   
```azurepowershell-interactive   
$vNet | Set-AzVirtualNetwork
```

## <a name="azure-sql-vm"></a>Virtuell Azure SQL-dator

Använd en förkonfigurerad Azure Marketplace-avbildning av en SQL-server för att skapa den virtuella SQL-datorn. Först skapar vi den virtuella datorn och sedan installerar vi SQL Server-tillägget på den virtuella datorn. 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

Använd [Set-AzVMSqlServerExtension](/powershell/module/az.compute/set-azvmsqlserverextension) för att lägga till [SQL Server-tillägget](../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md) till den virtuella SQL-datorn.

```azurepowershell-interactive
Set-AzVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du installerat en SQL&#92;IIS&#92;.NET-stack med Azure PowerShell. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en virtuell dator 
> * Installera IIS och .NET Core SDK på den virtuella datorn
> * Skapa en virtuell dator som kör SQL Server
> * Installera SQL Server-tillägget

Gå vidare till nästa självstudie och lär dig hur du skyddar IIS-webbservern med TLS/SSL-certifikat.

> [!div class="nextstepaction"]
> [Skydda IIS-webbservern med TLS/SSL-certifikat](tutorial-secure-web-server.md)
