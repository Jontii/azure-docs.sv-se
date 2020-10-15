---
title: Importera eller exportera data med Azure App-konfiguration
description: Lär dig hur du importerar eller exporterar konfigurations data till eller från Azure App konfiguration. Exchange-data mellan appens konfigurations lager och kod projekt.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 0e127a11f3821325602a78d46c69b2330bfb8293
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074625"
---
# <a name="import-or-export-configuration-data"></a>Importera eller exportera konfigurationsdata

Azure App konfiguration stöder åtgärder för import och export av data. Använd dessa åtgärder för att arbeta med konfigurations data i Mass-och Exchange-data mellan appens konfigurations lagring och kod projekt. Du kan till exempel konfigurera ett konfigurations lager för en app för testning och en annan för produktion. Du kan kopiera program inställningar mellan dem så att du inte behöver ange data två gånger.

Den här artikeln innehåller en guide för att importera och exportera data med app-konfiguration. Ta en titt på vår [GitHub-åtgärd](./concept-github-action.md)om du vill konfigurera en pågående synkronisering med din GitHub-lagrings platsen.

## <a name="import-data"></a>Importera data

Import hämtar konfigurations data till ett konfigurations lager för appar från en befintlig källa. Använd funktionen Importera för att migrera data till ett konfigurations lager för appar eller samla in data från flera källor. Konfiguration av appar stöder import från en JSON-, YAML-eller egenskaps fil.

Importera data med hjälp av antingen [Azure Portal](https://portal.azure.com) eller [Azure CLI](./scripts/cli-import.md). Följ de här stegen i Azure Portal:

1. Bläddra till appens konfigurations Arkiv och välj **Importera/exportera** på menyn **åtgärder** .

1. På fliken **Importera** väljer du **käll tjänst**  >  **konfigurations fil**.

1. Välj **för språk** och välj önskad Indatatyp.

1. Välj **mappikonen** och bläddra till den fil som ska importeras.

    ![Importera fil](./media/import-file.png)

1. Välj en **avgränsare**och ange ett **prefix** som ska användas för importerade nyckel namn.

1. Du kan också välja en **etikett**.

1. Välj **tillämpa** för att slutföra importen.

    ![Import filen är färdig](./media/import-file-complete.png)

## <a name="export-data"></a>Exportera data

Exportera skriver konfigurations data som lagras i app-konfigurationen till ett annat mål. Använd funktionen export, till exempel för att spara data i ett konfigurations lager för appar till en fil som är inbäddad med program koden under distributionen.

Exportera data med hjälp av antingen [Azure Portal](https://portal.azure.com) eller [Azure CLI](./scripts/cli-export.md). Följ de här stegen i Azure Portal:

1. Bläddra till appens konfigurations Arkiv och välj **Importera/exportera**.

1. På fliken **Exportera** väljer du **Target service**  >  **konfigurations filen**för mål tjänsten.

1. Du kan också ange ett **prefix** och välja en **etikett** och en tidpunkt för nycklar som ska exporteras.

1. Välj en **fil typs**  >  **avgränsare**.

1. Välj **tillämpa** för att slutföra exporten.

    ![Export filen är färdig](./media/export-file-complete.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ASP.NET Core-webbapp](./quickstart-aspnet-core-app.md)