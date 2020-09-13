---
title: T-SQL-vyer med Synapse SQL
description: Tips för att använda T-SQL-vyer och utveckla lösningar med Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: fafa0c2e1b02cc49bfb852ed7770b0927b0e9334
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032732"
---
# <a name="t-sql-views-using-synapse-sql"></a>T-SQL-vyer med Synapse SQL
I den här artikeln hittar du tips för att använda T-SQL-vyer och utveckla lösningar med Synapse SQL. 

## <a name="why-use-views"></a>Varför ska jag använda vyer

Vyer kan användas på ett antal olika sätt för att förbättra kvaliteten på din lösning.  I den här artikeln beskrivs några exempel på hur du kan utöka din lösning med vyer och innehåller de begränsningar som måste beaktas.

### <a name="sql-pool---create-view"></a>SQL-pool – skapa vy

> [!NOTE]
> **SQL-pool**: syntaxen för Create View beskrivs inte i den här artikeln. Mer information finns i dokumentationen för att [Skapa vy](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) .

### <a name="sql-on-demand-preview---create-view"></a>SQL på begäran (för hands version) – skapa vy

> [!NOTE]
> **SQL på begäran**: syntaxen för Create View beskrivs inte i den här artikeln. Mer information finns i dokumentationen för att [Skapa vy](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="architectural-abstraction"></a>Arkitektur abstraktion

Ett vanligt program mönster är att återskapa tabeller med [CREATE TABLE som Select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS), som följs av ett objekt som byter namn på ett mönster när data läses in.

I följande exempel läggs nya datum poster till i en datum dimension. Observera att en ny tabell, DimDate_New, först skapas och sedan byter namn till den ursprungliga versionen av tabellen.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Tänk på att den här metoden kan resultera i att tabeller visas och visas i en användares vy, och meddelande om att tabellen inte finns i fel meddelanden. Vyer kan användas för att ge användare ett konsekvent presentations lager medan de underliggande objekten byter namn.

Genom att ge åtkomst till data via vyer behöver användarna inte insyn i de underliggande tabellerna. Förutom en konsekvent användar upplevelse ser det här lagret till att Analytics designers kan utveckla data modellen. Möjligheten att utveckla de underliggande tabellerna innebär att designers kan använda CTAS för att maximera prestanda under inläsningen av data.

## <a name="performance-optimization"></a>Prestandaoptimering

Vyer kan också användas för att upprätthålla prestanda optimerade kopplingar mellan tabeller. En vy kan till exempel innehålla en redundant distributions nyckel som en del av villkoren för att minimera data flytten.

Att tvinga fram en viss fråga eller ett kopplings tips är en annan fördel med T-SQL-vyer. Det innebär att funktionerna för vyer säkerställer att kopplingar alltid utförs på ett optimalt sätt. Du undviker att användarna måste komma ihåg rätt konstruktion för deras kopplingar.

## <a name="limitations"></a>Begränsningar

Vyer i Synapse SQL lagras endast som metadata. Det innebär att följande alternativ inte är tillgängliga:

* Det finns inget schema bindnings alternativ
* Bas tabeller kan inte uppdateras via vyn
* Det går inte att skapa vyer över temporära tabeller
* Det finns inget stöd för utöknings-och NOEXPAND-tips
* Det finns inga indexerade vyer i Synapse SQL

## <a name="next-steps"></a>Nästa steg

Mer utvecklings tips finns i [Översikt över SYNAPSE SQL-utveckling](develop-overview.md).



