---
title: SQL-skript i Azure Synapse Studio (för hands version)
description: Introduktion till Azure Synapse Studio (för hands version) SQL-skript
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 9d130c2a2db9ccead7180b6248398a84fcb34c3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441246"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Använda SQL-skript i Azure Synapse Studio (för hands version)

Azure Synapse Studio (för hands version) innehåller ett webb gränssnitt för SQL-skript där du kan redigera SQL-frågor. Du kan ansluta till SQL-poolen (förhands granskning) eller SQL på begäran (för hands version). 

## <a name="begin-authoring-in-sql-script"></a>Börja redigera i SQL-skript 

Det finns flera sätt att starta redigerings funktionen i SQL-skript. Du kan skapa ett nytt SQL-skript via någon av följande metoder.

1. Från menyn utveckla väljer du ikonen **"+"** och sedan SQL- **skript**.

![nytt SQL-skript](media/author-sql-script/newsqlscript.png)

2. Från menyn **åtgärder** väljer du **nytt SQL-skript**.
> [!div class="mx-imgBorder"]
> ![nytt SQL-skript 2-åtgärder](media/author-sql-script/newsqlscript2actions.png)

Du kan också göra så här: 

3. Välj **Importera** från menyn **åtgärder** under utveckla SQL-skript. Välj ett befintligt SQL-skript från din lokala lagrings plats.
![nytt SQL-skript 3-åtgärder](media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Skapa SQL-skript

1. Välj ett namn för ditt SQL-skript genom att välja **egenskaps** knappen och ersätta standard namnet som tilldelats SQL-skriptet. 
![nytt SQL-skript Byt namn](media/author-sql-script/newsqlscriptrename.png)

2. Välj den aktuella SQL-poolen eller SQL på begäran på den nedrullningsbara menyn **Anslut till** . Eller om det behövs väljer du databasen från **Använd databas**. 
![ny SQL Välj pool](media/author-sql-script/newsqlchoosepool.png)

3. Börja redigera SQL-skript med hjälp av IntelliSense-funktionen.
![nytt SQL-IntelliSense](media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Kör SQL-skriptet

Klicka på knappen **Kör** för att köra SQL-skriptet. Resultaten visas som standard i en tabell.

![ny tabell med SQL-skript resultat](media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Exportera resultaten

Du kan exportera resultaten till din lokala lagring i olika format (inklusive CSV, Excel, JSON, XML) genom att välja "Exportera resultat" och välja tillägget.

Du kan också visualisera SQL-skript resultatet i ett diagram genom att välja **diagram** knappen. Välj kolumnen diagram typ och **kategori**. Du kan exportera diagrammet till en bild genom att välja **Spara som bild**. 

![nytt resultat diagram för SQL-skript](media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Utforska data från en Parquet-fil

Du kan utforska Parquet-filer i ett lagrings konto med hjälp av SQL-skript för att förhandsgranska fil innehållet.

![nytt skript sqlod Parquet](media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL-tabeller, externa tabeller, vyer

Genom att välja menyn **åtgärder** under data kan du välja flera åtgärder, till exempel:

- Nytt SQL-skript
- Markera de 1000 översta raderna
- CREATE
- SLÄPP och skapa 
 
Utforska den tillgängliga gesten genom att högerklicka på noderna i SQL-poolen och SQL på begäran.
 
![ny skript databas](media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du redigerar ett SQL-skript finns i [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).
