---
title: TicksToDateTime i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function TicksToDateTime i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 89a8dba97725049b86fc6b38c09e0dd125bb48d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88608844"
---
# <a name="tickstodatetime-azure-cosmos-db"></a>TicksToDateTime (Azure Cosmos DB)

Konverterar angivet Ticket-värde till ett DateTime-värde.
  
## <a name="syntax"></a>Syntax
  
```sql
TicksToDateTime (<Ticks>)
```

## <a name="arguments"></a>Argument

*Ticks*  

Ett signerat numeriskt värde, det aktuella antalet 100 nanosekunder som har förflutit sedan UNIX-epoken. Med andra ord är det antalet 100 nanosekunder som har förflutit sedan 00:00:00 torsdag, 1 januari 1970.

## <a name="return-types"></a>Retur typer

Returnerar UTC-datum och tid i ett ISO 8601-sträng värde i formatet `YYYY-MM-DDThh:mm:ss.fffffffZ` där:
  
  |Format|Beskrivning|
  |-|-|
  |MMMM|fyrsiffrigt år|
  |MM|tvåsiffrig månad (01 = januari osv.)|
  |FORMATET|fyrsiffrigt dag i månad (01 till 31)|
  |T|indikerare för start av tids element|
  |hh|Dubbels siffrig timme (00 – 23)|
  |mm|två-siffriga minuter (00 till 59)|
  |ss|tvåsiffriga sekunder (00 till 59)|
  |.fffffff|sju siffrors decimal sekunder|
  |Z|UTC-beteckning (Coordinated Universal Time)||
  
  Mer information om ISO 8601-formatet finns i [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Kommentarer

TicksToDateTime kommer att returneras `undefined` om det angivna Ticket-värdet är ogiltigt.

## <a name="examples"></a>Exempel
  
I följande exempel konverteras skalstrecken till en DateTime:

```sql
SELECT TicksToDateTime(15943368134575530) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-09T23:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Nästa steg

- [Datum-och tids funktioner Azure Cosmos DB](sql-query-date-time-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
