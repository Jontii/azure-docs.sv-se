---
title: 'CLI: återställa en app från en säkerhets kopia'
description: Lär dig hur du använder Azure CLI för att automatisera distribution och hantering av din App Service-app. Det här exemplet visar hur du återställer en app från en säkerhets kopia.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/07/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18
ms.openlocfilehash: 8c3c0f4f57d21e5c07867a2c43ce3c3114f79a9b
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "80058860"
---
# <a name="restore-a-web-app-from-a-backup-using-cli"></a>Återställa en webbapp från en säkerhetskopia med CLI

Det här exempelskriptet skapar en webbapp i App Service med dess relaterade resurser, och skapar sedan en tidsbegränsad säkerhetskopia för webbappen. 

Om du vill köra skriptet behöver du en befintlig säkerhetskopia för en webbapp. Information om hur du skapar en sådan finns i [Säkerhetskopiera en webbapp](cli-backup-onetime.md) eller [Skapa en schemalagd säkerhetskopiering för en webbapp](cli-backup-scheduled.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI-gränssnittet lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-restore/backup-restore.sh?highlight=3-4,9 "Restore a web app from a backup")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-list) | Hämtar en lista över säkerhetskopior för en webbapp. |
| [`az webapp config backup restore`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-restore) | Återställer en webbapp från en säkerhetskopia. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Fler App Service CLI-skriptexempel finns i [dokumentationen för Azure App Service](../samples-cli.md).
