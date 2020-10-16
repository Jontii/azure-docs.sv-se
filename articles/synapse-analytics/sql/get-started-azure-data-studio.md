---
title: Ansluta till Synapse SQL med Azure Data Studio (för hands version)
description: Använd Azure Data Studio (för hands version) för att ansluta till och fråga Synapse SQL i Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: c3c1b61cb4f799b79ca00fc92ffadc2374a61c03
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132473"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio-preview"></a>Ansluta till Synapse SQL med Azure Data Studio (för hands version)

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [SQLCMD](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Du kan använda [Azure Data Studio (för hands version)](/sql/azure-data-studio/download-azure-data-studio?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för att ansluta till och fråga Synapse SQL i Azure Synapse Analytics. 

## <a name="connect"></a>Anslut

Om du vill ansluta till Synapse SQL öppnar du Azure Data Studio och väljer **ny anslutning**.

![Öppna Azure Data Studio](./media/get-started-azure-data-studio/1-start.png)

Välj **Microsoft SQL Server** som **Anslutnings typ**.

Anslutningen kräver följande parametrar:

* **Server:** Server i formatet `<Azure Synapse workspace name>` -OnDemand.SQL.azuresynapse.net
* **Databas:** Databas namn

> [!NOTE]
> Om du vill använda **SQL på begäran (för hands version)** bör URL: en se ut så här:
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> Om du vill använda SQL- **poolen** bör URL: en se ut så här:
>
> - `<Azure Synapse workspace name>`. sql.azuresynapse.net

Välj **Windows-autentisering**, **Azure Active Directory**eller **SQL-inloggning** som **Autentiseringstyp**.

Om du vill använda **SQL-inloggning** som autentiseringstyp lägger du till användar namn/lösen ord-parametrar:

* **Användare:** Server användare i formuläret `<User>`
* **Lösen ord:** Lösen ord kopplat till användaren

Om du vill använda Azure Active Directory måste du välja autentiseringstypen som krävs.

![AAD-autentisering](./media/get-started-azure-data-studio/3-aad-auth.png)

Följande skärm bild visar **anslutnings information** för **Windows-autentisering**:

![Windows-autentisering](./media/get-started-azure-data-studio/3-windows-auth.png)

Följande skärm bild visar **anslutnings information** med **SQL-inloggning**:

![SQL-inloggning](./media/get-started-azure-data-studio/2-database-details.png)

Efter en lyckad inloggning bör du se en instrument panel så här: ![ instrument panelen](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Söka i data

När du har anslutit kan du fråga Synapse SQL med stöd för [Transact-SQL-uttryck (T-SQL)](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) som stöds mot instansen. Kom igång genom att välja **ny fråga** från vyn instrument panel.

![Ny fråga](./media/get-started-azure-data-studio/5-new-query.png)

Du kan till exempel använda följande Transact-SQL-uttryck för att [fråga Parquet-filer](query-parquet-files.md) med SQL på begäran:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Nästa steg 
Utforska andra sätt att ansluta till Synapse SQL: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [SQLCMD](get-started-connect-sqlcmd.md)

Mer information finns på [använd Azure Data Studio för att ansluta och fråga efter data med en dedikerad SQL-pool i Azure Synapse Analytics](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-dw). 
