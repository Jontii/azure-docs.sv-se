---
title: 'Självstudie: ansluta SQL på begäran till Power BI Desktop & Skapa rapport'
description: I den här självstudien får du lära dig hur du ansluter SQL på begäran i Azure Synapse Analytics till Power BI Desktop och skapar en demo rapport som baseras på en vy.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5c86825d6dce8681e114ec930add751b6beae085
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91539562"
---
# <a name="tutorial-use-sql-on-demand-with-power-bi-desktop--create-a-report"></a>Självstudie: använda SQL på begäran med Power BI Desktop & skapa en rapport

I den här självstudien får du lära dig att:

> [!div class="checklist"]
>
> - Skapa demo databas
> - Skapa vy som används för rapport
> - Anslut Power BI Desktop till SQL på begäran
> - Skapa rapport baserat på vy

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien, finns följande förhandskrav:

- [Power BI Desktop](https://powerbi.microsoft.com/downloads/) – behövs för att visualisera data och skapa en rapport.
- [Azure Synapse-arbetsyta](https://docs.microsoft.com/azure/synapse-analytics/quickstart-synapse-studio) – behövs för att skapa databas, extern data källa och Visa.

Valfritt:

- Ett SQL-frågefönster, till exempel [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)eller [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

Värden för följande parametrar:

| Parameter                                 | Beskrivning                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Slut punkts adress för SQL-tjänst på begäran    | Används som server namn                                   |
| Tjänstens slut punkts region för SQL på begäran     | Används för att fastställa lagringen som används i exemplen |
| Användar namn och lösen ord för slut punkts åtkomst | Används för att komma åt slut punkten                               |
| Databas som du ska använda för att skapa vyer     | Databasen som används som start punkt i exemplen       |

## <a name="1---create-database"></a>1 – skapa databas

Skapa din egen demo databas för demo miljön. Du använder den här databasen för att visa metadata och inte lagra faktiska data.

Skapa demo databasen (och släpp en befintlig databas om det behövs) genom att köra följande skript för Transact-SQL (T-SQL):

```sql
-- Drop database if it exists
DROP DATABASE IF EXISTS Demo
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-data-source"></a>2 – Skapa data Källa

Det krävs en data källa för SQL-tjänsten på begäran för att komma åt filer i lagringen. Skapa data källan för ett lagrings konto som finns i samma region som din slut punkt. Även om SQL på begäran kan komma åt lagrings konton från olika regioner, ger lagring och slut punkt i samma region bättre prestanda.

Skapa data källan genom att köra följande skript för Transact-SQL (T-SQL):

```sql
-- There is no credential in data surce. We are using public storage account which doesn't need a secret.
CREATE EXTERNAL DATA SOURCE AzureOpenData
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/')
```

## <a name="3---prepare-view"></a>3 – Förbered vy

Skapa vyn baserat på externa demonstrations data för Power BI att använda genom att köra följande Transact-SQL-skript (T-SQL):

Skapa vyn `usPopulationView` i databasen `Demo` med följande fråga:

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS uspv;
```

Demonstrations data innehåller följande data uppsättningar:

AMERIKANSKA befolkning efter kön och tävling för varje amerikansk region från 2000 och 2010 decennial-inventering i Parquet-format.

| Mappsökväg                                                  | Beskrivning                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /Release                                                    | Överordnad mapp för data i demonstrations lagrings konto               |
| /Release/us_population_county/                               | US population-datafiler i Parquet-format, partitionerade efter år med Hive/Hadoop partitionerings schema. |

## <a name="4---create-power-bi-report"></a>4 – Skapa Power BI rapport

Skapa rapporten för Power BI Desktop med hjälp av följande steg:

1. Öppna Power BI Desktop programmet och välj **Hämta data**.

   ![Öppna Power BI Desktop-programmet och välj Hämta data.](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Välj **Azure**-  >  **Azure SQL Database**. 

   ![Välj data källa.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Skriv namnet på servern där-databasen finns i fältet **Server** och skriv sedan `Demo` in namnet på databasen. Välj alternativet **Importera** och välj sedan **OK**. 

   ![Välj databas på slut punkten.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Välj önskad autentiseringsmetod:

    - Exempel på AAD 
  
        ![Klicka på Logga in.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Exempel på SQL-inloggning – ange ditt användar namn och lösen ord.

        ![Använd SQL-inloggning.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Välj vyn `usPopulationView` och välj sedan **load**. 

   ![Välj en vy i den valda databasen.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Vänta tills åtgärden har slutförts och sedan visas ett popup-meddelande `There are pending changes in your queries that haven't been applied` . Välj **tillämpa ändringar**. 

   ![Klicka på tillämpa ändringar.](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Vänta tills dialog rutan **tillämpa ändringar** försvinner, vilket kan ta några minuter. 

   ![Vänta tills en fråga har slutförts.](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. När inläsningen är klar väljer du följande kolumner i den här ordningen för att skapa rapporten:
   - countyName
   - ifyllnad
   - stateName

   ![Välj kolumner av intresse för att generera en kart rapport.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här rapporten tar du bort resurserna med följande steg:

1. Ta bort autentiseringsuppgiften för lagrings kontot

   ```sql
   DROP EXTENAL DATA SOURCE AzureOpenData
   ```

2. Ta bort vyn

   ```sql
   DROP VIEW usPopulationView;
   ```

3. Ta bort databasen

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>Nästa steg

Gå vidare till [fråge-lagringsfiler](develop-storage-files-overview.md) för att lära dig hur du frågar Storage-filer med Synapse SQL.
