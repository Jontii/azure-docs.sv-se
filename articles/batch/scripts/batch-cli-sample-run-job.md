---
title: Exempel på Azure CLI-skript – köra ett batch-jobb
description: Det här skriptet skapar ett Batch-jobb och lägger till en rad uppgifter i jobbet. Det visar även hur du övervakar ett jobb och dess uppgifter.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: d8efce1bc8dc32070b770769104b7fdc38e683cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500240"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>CLI-exempel: Köra ett jobb och uppgifter med Azure Batch

Det här skriptet skapar ett Batch-jobb och lägger till en rad uppgifter i jobbet. Det visar även hur du övervakar ett jobb och dess uppgifter. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.20 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Skapar Batch-kontot. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentiserar mot det angivna Batch-kontot för ytterligare CLI-interaktion.  |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Skapar en pool med beräkningsnoder.  |
| [az batch job create](/cli/azure/batch/job#az-batch-job-create) | Skapar ett Batch-jobb.  |
| [az batch task create](/cli/azure/batch/task#az-batch-task-create) | Lägger till en aktivitet i angivet Batch-jobb.  |
| [az batch job set](/cli/azure/batch/job#az-batch-job-set) | Uppdaterar egenskaperna för ett Batch-jobb.  |
| [az batch job show](/cli/azure/batch/job#az-batch-job-show) | Hämtar information om ett angivet Batch-jobb.  |
| [az batch task show](/cli/azure/batch/task#az-batch-task-show) | Hämtar information om en uppgift från det angivna Batch-jobbet.  |
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
