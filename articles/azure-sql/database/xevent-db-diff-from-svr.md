---
title: Utökade händelser
description: Beskriver utökade händelser (XEvents) i Azure SQL Database och hur Event-sessioner skiljer sig något från Event-sessioner i Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: c8f73c0789cd0211deeb66af5c7300a81d7b1be0
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619822"
---
# <a name="extended-events-in-azure-sql-database"></a>Utökade händelser i Azure SQL Database 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Funktions uppsättningen för utökade händelser i Azure SQL Database är en robust delmängd av funktionerna i SQL Server och Azure SQL-hanterad instans.

*XEvents* är ett informellt smek namn som ibland används för "utökade händelser" i Bloggar och andra informella platser.

Ytterligare information om utökade händelser finns på:

- [Snabbstart: utökade händelser i SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
- [Utökade händelser](/sql/relational-databases/extended-events/extended-events)

## <a name="prerequisites"></a>Förutsättningar

Det här avsnittet förutsätter att du redan har kunskaper om:

- [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Utökade händelser](/sql/relational-databases/extended-events/extended-events)

- Den stora delen av vår dokumentation om utökade händelser gäller för SQL Server, Azure SQL Database och Azure SQL-hanterad instans.

Tidigare exponering för följande objekt är användbart när du väljer händelse filen som [mål](#AzureXEventsTargets):

- [Azure Storage tjänst](https://azure.microsoft.com/services/storage/)

- [Azure PowerShell med Azure Storage](/powershell/module/az.storage/)

## <a name="code-samples"></a>Kodexempel

Relaterade ämnen innehåller två kod exempel:

- [Ring buffertens mål kod för utökade händelser i Azure SQL Database](xevent-code-ring-buffer.md)

  - Kort enkelt Transact-SQL-skript.
  - Vi betonar i avsnittet kod exempel att när du är färdig med ett ring mål bör du frigöra resurserna genom att köra en Alter-Drop- `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` instruktion. Senare kan du lägga till en annan instans av ringbufferten med `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...` .

- [Mål kod för händelse filen för utökade händelser i Azure SQL Database](xevent-code-event-file.md)

  - Fas 1 är PowerShell för att skapa en Azure Storage-behållare.
  - Fas 2 är Transact-SQL som använder Azure Storage container.

## <a name="transact-sql-differences"></a>Skillnader i Transact-SQL

- När du kör kommandot [skapa händelsesessionen](/sql/t-sql/statements/create-event-session-transact-sql) på SQL Server använder du **on-Server** -satsen. Men Azure SQL Database du använder **on Database** -satsen i stället.
- **On Database** -satsen gäller även Transact-SQL-kommandon för att [ändra HÄNDELSESESSIONEN](/sql/t-sql/statements/alter-event-session-transact-sql) och [släppa Event session](/sql/t-sql/statements/drop-event-session-transact-sql) .

- Ett bra tips är att inkludera alternativet för händelsesessionen för **STARTUP_STATE = på** i sessionen för att **skapa en händelse**  eller **ändra Event-session** .
  - Värdet **= on** har stöd för automatisk omstart efter en omkonfiguration av den logiska databasen på grund av en redundansväxling.

## <a name="new-catalog-views"></a>Nya katalogvyer

Funktionen utökade händelser stöds av flera [katalogvyer](https://msdn.microsoft.com/library/ms174365.aspx). I katalogvyer visas *metadata eller definitioner* för användarsessioner som skapats av användare i den aktuella databasen. Vyer returnerar inte information om instanser av aktiva Event-sessioner.

| Namn på<br/>katalogvy | Beskrivning |
|:--- |:--- |
| **sys. database_event_session_actions** |Returnerar en rad för varje åtgärd vid varje händelse i en användarsession. |
| **sys. database_event_session_events** |Returnerar en rad för varje händelse i en användarsession. |
| **sys. database_event_session_fields** |Returnerar en rad för varje anpassnings aktiv kolumn som uttryckligen angavs för händelser och mål. |
| **sys. database_event_session_targets** |Returnerar en rad för varje händelse mål för en Event-session. |
| **sys. database_event_sessions** |Returnerar en rad för varje händelsesessionen i databasen. |

I Microsoft SQL Server har liknande katalogvy-vyer namn som inkluderar *. Server \_ * i stället för *. \_ Database*. Namn mönstret liknar **sys. server_event_%**.

## <a name="new-dynamic-management-views-dmvs"></a>Nya vyer för dynamisk hantering [(DMV: er)](https://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL Database har [dynamiska Management views (DMV: er)](https://msdn.microsoft.com/library/bb677293.aspx) som stöder utökade händelser. DMV: er anger om *aktiva* Event-sessioner.

| Namn på DMV | Beskrivning |
|:--- |:--- |
| **sys. dm_xe_database_session_event_actions** |Returnerar information om Event session-åtgärder. |
| **sys. dm_xe_database_session_events** |Returnerar information om sessions händelser. |
| **sys. dm_xe_database_session_object_columns** |Visar konfigurations värden för objekt som är kopplade till en session. |
| **sys. dm_xe_database_session_targets** |Returnerar information om mål för sessioner. |
| **sys. dm_xe_database_sessions** |Returnerar en rad för varje händelsesessionen som är begränsad till den aktuella databasen. |

I Microsoft SQL Server får liknande katalogfiler namn utan * \_ databas* delen av namnet, t. ex.:

- **sys. dm_xe_sessions**, i stället för namn<br/>**sys. dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMV: er gemensamt för båda

För utökade händelser finns det ytterligare DMV: er som är gemensamma för Azure SQL Database, Azure SQL-hanterad instans och Microsoft SQL Server:

- **sys. dm_xe_map_values**
- **sys. dm_xe_object_columns**
- **sys. dm_xe_objects**
- **sys. dm_xe_packages**

<a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Hitta tillgängliga utökade händelser, åtgärder och mål

Du kan köra en enkel SQL- **Välj** för att hämta en lista över tillgängliga händelser, åtgärder och mål.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```

<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-azure-sql-database-event-sessions"></a>Mål för dina Azure SQL Database Event-sessioner

Här är mål som kan samla in resultat från dina event-sessioner på Azure SQL Database:

- [Ringbufferten för ringbufferten](https://msdn.microsoft.com/library/ff878182.aspx) innehåller en kort händelse data i minnet.
- [Mål för händelse räknare](https://msdn.microsoft.com/library/ff878025.aspx) – räknar alla händelser som inträffar under en Extended Events-session.
- [Mål för händelse fil](https://msdn.microsoft.com/library/ff878115.aspx) – skriver fullständiga buffertar till en Azure Storage behållare.

[ETW-](https://msdn.microsoft.com/library/ms751538.aspx) API: et är inte tillgängligt för utökade händelser på Azure SQL Database. ETW (Event tracing for Windows)

## <a name="restrictions"></a>Begränsningar

Det finns ett par säkerhetsrelaterade skillnader som rör moln miljön i Azure SQL Database:

- Utökade händelser baseras på isolerings modellen för en enda klient. En händelsesessionen i en databas kan inte komma åt data eller händelser från en annan databas.
- Det går inte att utfärda en **session med Create Event-session** i kontexten för **Master** -databasen.

## <a name="permission-model"></a>Behörighets modell

Du måste ha behörighet att **kontrol lera** databasen för att kunna utfärda en session med att **Skapa event-session** . Databas ägaren (dbo) har **kontroll** behörighet.

### <a name="storage-container-authorizations"></a>Auktorisering av lagrings behållare

SAS-token som du skapar för din Azure Storage-behållare måste ange **RWL** för behörigheterna. **RWL** -värdet har följande behörigheter:

- Läsa
- Skriva
- Lista

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Det finns scenarier där utökade händelser kan ackumulera mer aktivt minne än vad som är felfritt för det övergripande systemet. Det innebär att Azure SQL Database dynamiskt ställer in och justerar gränserna för mängden aktivt minne som kan samlas in av en Event-session. Många faktorer ingår i den dynamiska beräkningen.

Om du får ett fel meddelande om att högsta mängd minne har verkställts kan du utföra några lämpliga åtgärder:

- Kör färre samtidiga Event-sessioner.
- Minska mängden minne som du anger i den **högsta \_ minnes** satsen genom att **skapa** och **ändra** -instruktioner för Event-sessioner.

### <a name="network-latency"></a>Svarstid för nätverk

Målet för **händelse filen** kan påverka nätverks fördröjningen eller-felen och samtidigt spara data för att Azure Storage blobbar. Andra händelser i Azure SQL Database kan bli fördröjda medan de väntar på att nätverkskommunikation ska slutföras. Den här fördröjningen kan sakta ner din arbets belastning.

- Undvik den här prestanda risken genom att undvika att ange alternativet **EVENT_RETENTION_MODE** för att **NO_EVENT_LOSS** i Event session-definitionerna.

## <a name="related-links"></a>Relaterade länkar

- [Använda Azure PowerShell med Azure Storage](/powershell/module/az.storage/).
- [Azure Storage-cmdletar](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Använd Azure PowerShell med Azure Storage](/powershell/module/az.storage/)
- [Använda Blob Storage från .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Skapa autentiseringsuppgift (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [Skapa HÄNDELSESESSIONEN (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Jonathan Kehayiass blogg inlägg om utökade händelser i Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)
- Webb sidan för Azure *service updates* , begränsas av parametern till Azure SQL Database:
  - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
