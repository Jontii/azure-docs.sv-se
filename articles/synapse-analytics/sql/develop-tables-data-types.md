---
title: Tabell data typer i Synapse SQL
description: Rekommendationer för att definiera tabell data typer i Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: dec5d73c0c121a1e4995bd66500fc08fde3f2f10
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91288757"
---
# <a name="table-data-types-in-synapse-sql"></a>Tabell data typer i Synapse SQL

I den här artikeln hittar du rekommendationer för att definiera tabell data typer i Synapse SQL. 

## <a name="data-types"></a>Datatyper

Synapse SQL stöder de vanligaste data typerna. En lista över data typer som stöds finns i [data typer](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes&preserve-view=true) i instruktionen CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimera rad längd

Genom att minimera storleken på data typerna förkortas rad längden, vilket leder till bättre prestanda för frågor. Använd den minsta data typen som används för dina data.

- Undvik att definiera tecken kolumner med en stor standard längd. Om det längsta värdet till exempel är 25 tecken definierar du kolumnen som VARCHAR (25).
- Undvik att använda [NVARCHAR] [NVARCHAR] när du bara behöver VARCHAR.
- När det är möjligt använder du NVARCHAR (4000) eller VARCHAR (8000) i stället för NVARCHAR (MAX) eller VARCHAR (MAX).

> [!NOTE]
> Om du använder PolyBase-externa tabeller för att läsa in tabeller med SQL-pooler får inte tabell radens definierade längd överstiga 1 MB. När en rad med data för variabel längd överskrider 1 MB kan du läsa in raden med BCP, men inte med PolyBase.

## <a name="identify-unsupported-data-types"></a>Identifiera data typer som inte stöds

Om du migrerar din databas från en annan SQL-databas kan du stöta på data typer som inte stöds i Synapse SQL. Använd den här frågan för att identifiera data typer som inte stöds i det befintliga SQL-schemat.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Lösningar för data typer som inte stöds

I följande lista visas de data typer som Synapse SQL inte stöder och innehåller alternativ som du kan använda i stället för data typerna som inte stöds.

| Datatyp som inte stöds | Lösning |
| --- | --- |
| [geometri](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [geography](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(4000) |
| [avbildning](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [information](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Dela upp kolumnen i flera kolumner med strikt typ. |
| [partitionstabell](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Om du använder SQL-pool kan du konvertera till temporära tabeller. Om du använder SQL (för hands version) kan du överväga att lagra data till lagring med [CETAS](../sql/develop-tables-cetas.md). |
| [tidsstämpel](/sql/t-sql/data-types/date-and-time-types) |Återwork-koden för att använda [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) och funktionen [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) . Endast konstanter stöds som standard, och därför kan current_timestamp inte definieras som en standard begränsning. Om du behöver migrera rad versions värden från en kolumn med tidstämpel, använder du [Binary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(8) eller [varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(8) för värden som inte är null eller null. |
| [fil](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [användardefinierad typ](/sql/relational-databases/native-client/features/using-user-defined-types&preserve-view=true) |Konvertera tillbaka till den interna data typen när det är möjligt. |
| standardvärden | Standardvärden stöder bara litteraler och konstanter. |

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar tabeller finns i [tabell översikt](develop-overview.md).
