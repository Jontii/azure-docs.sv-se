---
title: Aktivera VM-tillägg med Azure CLI
description: Den här artikeln beskriver hur du distribuerar tillägg för virtuella datorer till Azure Arc-aktiverade servrar som körs i hybrid moln miljöer med hjälp av Azure CLI.
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: bf0a3e0940efc7e79adbe9f763ffdf34ea690fac
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94833273"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Aktivera Azure VM-tillägg med hjälp av Azure CLI

Den här artikeln visar hur du distribuerar och avinstallerar virtuella Azure-tillägg, som stöds av Azure Arc-aktiverade servrar, till en Linux-eller Windows hybrid-dator med hjälp av Azure CLI.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Installera Azure CLI-tillägget

ConnectedMachine-kommandona levereras inte som en del av Azure CLI. Innan du använder Azure CLI för att hantera VM-tillägg på hybrid servern som hanteras av Arc-aktiverade servrar måste du läsa in ConnectedMachine-tillägget. Kör följande kommando för att hämta det:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Aktivera tillägg

Om du vill aktivera ett VM-tillägg på din ARC-aktiverade Server använder du [AZ connectedmachine Machine-Extension Create](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_create) med parametrarna,,,, `--machine-name` `--extension-name` `--location` `--type` `settings` och `--publisher` .

I följande exempel aktive ras Log Analytics VM-tillägget på en ARC-aktiverad Linux-server:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "OmsAgentforLinux" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.EnterpriseCloud.Monitoring" --settings "{\"workspaceId\":\"workspaceId"}" --protected-settings "{\workspaceKey\":"\workspaceKey"} --type-handler-version "1.10" --resource-group "myResourceGroup"
```

I följande exempel aktive ras det anpassade skript tillägget på en ARC-aktiverad server:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

I följande exempel aktive ras Key Vault VM-tillägget (för hands version) på en ARC-aktiverad server:

```azurecli
az connectedmachine machine-extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>List tillägg har installerats

Om du vill hämta en lista över VM-tilläggen på din ARC-aktiverad server använder du [AZ connectedmachine Machine-Extension List](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_list) med `--machine-name` `--resource-group` parametrarna och.

Exempel:

```azurecli
az connectedmachine machine-extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Som standard är utdata för Azure CLI-kommandon i JSON (JavaScript Object Notation). Om du vill ändra standardutdata till en lista eller tabell använder du till exempel [AZ Configure--output](/cli/azure/reference-index). Du kan också lägga till ett `--output` kommando för en tids ändring i utdataformat.

I följande exempel visas de partiella JSON-utdata från `az connectedmachine machine-extension -list` kommandot:

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>Ta bort ett installerat tillägg

Om du vill ta bort ett installerat VM-tillägg på din ARC-aktiverade Server använder du [AZ connectedmachine Machine-Extension Delete](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_delete) med `--extension-name` `--machine-name` parametrarna, och `--resource-group` .

Om du till exempel vill ta bort Log Analytics VM-tillägget för Linux kör du följande kommando:

```azurecli
az connectedmachine machine-extension delete --machine-name "myMachineName" --name "OmsAgentforLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

- Du kan distribuera, hantera och ta bort VM-tillägg med hjälp av [Azure PowerShell](manage-vm-extensions-powershell.md), från [Azure Portal](manage-vm-extensions-portal.md)eller [Azure Resource Manager mallar](manage-vm-extensions-template.md).

- Felsöknings information finns i [fel söknings guiden för VM-tillägg](troubleshoot-vm-extensions.md).
