---
title: Azure PowerShell – aktivera Kundhanterade nycklar med SSE-hanterade diskar
description: Aktivera kryptering på Server sidan med Kundhanterade nycklar på dina hanterade diskar med Azure PowerShell.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 2eed2ee11f3a90e81d9ee845af2aa28620567603
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488330"
---
# <a name="azure-powershell---enable-customer-managed-keys-with-server-side-encryption---managed-disks"></a>Azure PowerShell – aktivera Kundhanterade nycklar med krypterings hanterade diskar på Server Sidan

Med Azure-disklagring kan du hantera dina egna nycklar när du använder SSE (Server Side Encryption) för hanterade diskar, om du väljer. För konceptuell information om SSE med Kundhanterade nycklar, samt andra typer av hanterade disk krypterings typer, se avsnittet [Kundhanterade nycklar](../disk-encryption.md#customer-managed-keys) i artikeln om disk kryptering.

## <a name="restrictions"></a>Begränsningar

För närvarande har Kundhanterade nycklar följande begränsningar:

- Om den här funktionen är aktive rad för disken kan du inte inaktivera den.
    Om du behöver kringgå detta måste du [Kopiera alla data](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) till en helt annan hanterad disk som inte använder Kundhanterade nycklar.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

## <a name="set-up-your-azure-key-vault-and-diskencryptionset"></a>Konfigurera din Azure Key Vault och DiskEncryptionSet

Om du vill använda Kundhanterade nycklar med SSE måste du konfigurera en Azure Key Vault och en DiskEncryptionSet-resurs.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

## <a name="examples"></a>Exempel

Nu när du har skapat och konfigurerat dessa resurser kan du använda dem för att skydda dina hanterade diskar. Följande är exempel på skript, var och en med respektive scenario, som du kan använda för att skydda dina hanterade diskar.

### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Skapa en virtuell dator med en Marketplace-avbildning, kryptera operativ system och data diskar med Kundhanterade nycklar

Kopiera skriptet, Ersätt alla exempel värden med dina egna parametrar och kör det sedan.

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "yourRegion"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "yourVMSize"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Skapa en tom disk som är krypterad med kryptering på Server sidan med Kundhanterade nycklar och koppla den till en virtuell dator

Kopiera skriptet, Ersätt alla exempel värden med dina egna parametrar och kör det sedan.

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

### <a name="encrypt-existing-managed-disks"></a>Kryptera befintliga hanterade diskar 

Dina befintliga diskar får inte anslutas till en virtuell dator som körs för att du ska kunna kryptera dem med följande skript:

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Skapa en skalnings uppsättning för virtuella datorer med en Marketplace-avbildning, kryptera operativ system och data diskar med Kundhanterade nycklar

Kopiera skriptet, Ersätt alla exempel värden med dina egna parametrar och kör det sedan.

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Ändra nyckeln för en DiskEncryptionSet för att rotera nyckeln för alla resurser som refererar till DiskEncryptionSet

Kopiera skriptet, Ersätt alla exempel värden med dina egna parametrar och kör det sedan.

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Hitta statusen för kryptering på Server sidan för en disk

[!INCLUDE [virtual-machines-disks-encryption-status-powershell](../../../includes/virtual-machines-disks-encryption-status-powershell.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). När du konfigurerar Kundhanterade nycklar, tilldelas en hanterad identitet automatiskt till dina resurser under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller den hanterade disken från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till de hanterade diskarna till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i [överföra en prenumeration mellan Azure AD-kataloger](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Nästa steg

- [Utforska Azure Resource Manager mallar för att skapa krypterade diskar med Kundhanterade nycklar](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Replikera datorer med Kundhanterade nycklar aktiverade diskar](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Konfigurera haveriberedskap för virtuella VMware-datorer till Azure med PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Konfigurera haveriberedskap till Azure för virtuella Hyper-V-datorer med PowerShell och Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)