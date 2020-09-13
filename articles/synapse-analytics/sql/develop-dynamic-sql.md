---
title: Använd dynamisk SQL i Synapse SQL
description: Tips för att använda dynamisk SQL i Synapse SQL.
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
ms.openlocfilehash: 4a4ce92278d5f6704fec4a5b9d30b44ddf89b0ad
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033446"
---
# <a name="dynamic-sql-in-synapse-sql"></a>Dynamisk SQL i Synapse SQL
I den här artikeln hittar du tips om hur du använder dynamiska SQL och utvecklar lösningar med Synapse SQL.

## <a name="dynamic-sql-example"></a>Dynamiskt SQL-exempel

När du utvecklar program kod kan du behöva använda dynamisk SQL för att leverera flexibla, generiska och modulära lösningar.

> [!NOTE]
> SQL-poolen stöder inte BLOB-datatyper för tillfället. Om du inte stöder BLOB-datatyper kan du begränsa storleken på dina strängar eftersom BLOB-datatyper inkluderar båda typerna varchar (max) och nvarchar (max). Om du har använt dessa typer i program koden för att bygga stora strängar måste du dela upp koden i segment och använda EXEC-instruktionen i stället.

Ett enkelt exempel:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Om strängen är kort kan du använda [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) som normalt.

> [!NOTE]
> Instruktioner som körs i dynamisk SQL kommer fortfarande att omfattas av alla verifierings regler för T-SQL.

## <a name="next-steps"></a>Nästa steg

Mer utvecklings tips finns i [utvecklings översikt](develop-overview.md).
