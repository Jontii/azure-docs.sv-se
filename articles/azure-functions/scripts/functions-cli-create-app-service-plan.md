---
title: Skapa en Funktionsapp i en App Service plan – Azure CLI
description: Azure CLI-skriptexempel – Skapa en funktionsapp i en App Service plan
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a2faef6162a1a9fe09082330bf52f25dde5f75f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87498590"
---
# <a name="create-a-function-app-in-an-app-service-plan"></a>Skapa en funktionsapp i en App Service plan

Det här exempelskriptet för Azure Functions skapar en funktionsapp som blir container åt dina funktioner. Funktionsappen som skapas använder en dedikerad App Service plan, vilket innebär att dina serverresurser alltid är på.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar en Azure Function-app som använder en dedikerad [App Service plan](../functions-scale.md#app-service-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Varje kommando i tabellen länkar till kommandospecifik dokumentation. I det här skriptet används följande kommandon:

| Kommando | Obs! |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Konfigurerar ett Azure Storage-konto. |
| [AZ functionapp plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) | Skapar en Premium-plan. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Skapar en funktionsapp i App Service-planen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).
