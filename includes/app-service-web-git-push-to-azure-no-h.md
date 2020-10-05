---
title: inkludera fil
description: inkludera fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8539696f4521a1b4a2f56fe7d2936b45dec26ec9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "88077720"
---
I det lokala terminalfönstret kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. Ersätt *\<deploymentLocalGitUrl-from-create-step>* med URL: en för den git-fjärrserver som du sparade från [skapa en webbapp](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. När git Credential Manager uppmanas att ange autentiseringsuppgifter, se till att du anger de autentiseringsuppgifter som du skapade i **Konfigurera en distributions användare**, inte de autentiseringsuppgifter som du använder för att logga in på Azure Portal.

```bash
git push azure master
```

Det kan ett par minuter att köra kommandot. Medan det körs visas information liknande den i följande exempel:
