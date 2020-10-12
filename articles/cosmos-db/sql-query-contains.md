---
title: Innehåller i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen CONTAINS SQL system i Azure Cosmos DB Returnerar ett booleskt värde som anger om det första sträng uttrycket innehåller det andra
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4877272fc2db521977a4111317118380399d27c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84322711"
---
# <a name="contains-azure-cosmos-db"></a>INNEHÅLLER (Azure Cosmos DB)

Returnerar ett booleskt värde som anger huruvida det första stränguttrycket innehåller det andra.  
  
## <a name="syntax"></a>Syntax
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr1*  
   Är sträng uttrycket som ska genomsökas.  
  
*str_expr2*  
   Är sträng uttrycket som ska hittas.  

*bool_expr* Valfritt värde för att ignorera Skift läge. Om värdet är true, innehåller en Skift läges känslig sökning. Värdet är false när det har angetts.
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel kontrol leras om "ABC" innehåller "AB" och om "ABC" innehåller "A".  
  
```sql
SELECT CONTAINS("abc", "ab", false) AS c1, CONTAINS("abc", "A", false) AS c2, CONTAINS("abc", "A", true) AS c3
```  
  
 Här är resultatuppsättningen.  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>Kommentarer

Den här systemfunktionen kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy).

Användning av RU-förbrukningen i innehåller ökar eftersom egenskapens kardinalitet i systemfunktionen ökar. Om du däremot kontrollerar om ett egenskaps värde innehåller en viss sträng, kommer frågan RU-avgiften att vara beroende av antalet möjliga värden för den egenskapen.

Överväg till exempel två egenskaper: stad och land. Stadens kardinalitet är 5 000 och landets kardinalitet är 200. Här följer två exempel frågor:

```sql
    SELECT * FROM c WHERE CONTAINS(c.town, "Red", false)
```

```sql
    SELECT * FROM c WHERE CONTAINS(c.country, "States", false)
```

Den första frågan kommer förmodligen att använda mer ru: er än den andra frågan eftersom stadens kardinalitet är högre än landet.

Om egenskaps storleken i innehåller är större än 1 KB för vissa dokument måste du läsa in dessa dokument med frågespråket. I detta fall går det inte att göra en fullständig utvärdering av frågespråket som innehåller med ett index. Avgiften för RU för innehåller är hög om du har ett stort antal dokument med egenskaps storlekar på mer än 1 KB.

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
