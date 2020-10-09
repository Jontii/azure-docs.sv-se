---
title: Skript exempel för Azure CLI – arbeta med nyckel värden i app Configuration Store
titleSuffix: Azure App Configuration
description: Använd Azure CLI-skript för att skapa, Visa, uppdatera och ta bort nyckel värden från konfigurations lager för appar
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: e3407a1da469bf4f31e5d54d83ee837bddbafffc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494765"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Arbeta med nyckelvärden i ett Azure App Configuration-arkiv

Det här exempel skriptet visar hur du:
* Skapa ett nytt nyckel/värde-par
* Lista alla befintliga nyckel/värde-par
* Uppdatera värdet för en nyligen skapad nyckel
* Ta bort det nya nyckel/värde-paret

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Den här artikeln kräver Azure CLI version 2,0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

I den här tabellen visas de kommandon som används i vårt exempel skript. 

| Kommando | Obs! |
|---|---|
| [az appconfig kv set](/cli/azure/appconfig/kv#az-appconfig-kv-set) | Skapa eller uppdatera ett nyckel/värde-par. |
| [az appconfig kv list](/cli/azure/appconfig/kv#az-appconfig-kv-list) | Visa en lista med nyckel/värde-par i ett konfigurations Arkiv för appar. |
| [az appconfig kv delete](/cli/azure/appconfig/kv#az-appconfig-kv-delete) | Ta bort ett nyckel/värde-par. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare skript exempel för programkonfigurations-CLI finns i [Azure App konfigurations-CLI-exempel](../cli-samples.md).
