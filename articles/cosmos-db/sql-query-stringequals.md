---
title: StringEquals i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen StringEquals i SQL system i Azure Cosmos DB Returnerar ett booleskt värde som anger om det första sträng uttrycket matchar det andra
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 57d124421082e1c38fab4d982687a8e6c970505e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "83853801"
---
# <a name="stringequals-azure-cosmos-db"></a>STRINGEQUALS (Azure Cosmos DB)

 Returnerar ett booleskt värde som anger om det första sträng uttrycket matchar det andra.  
  
## <a name="syntax"></a>Syntax
  
```sql
STRINGEQUALS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr1*  
   Är det första sträng uttryck som ska jämföras.  
  
*str_expr2*  
   Är det andra sträng uttryck som ska jämföras.  

*bool_expr* Valfritt värde för att ignorera Skift läge. När värdet är true kommer StringEquals att göra en Skift läges känslig sökning. Värdet är false när det har angetts.
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel kontrol leras om "ABC" matchar "ABC" och om "ABC" matchar "ABC".  
  
```sql
SELECT STRINGEQUALS("abc", "abc", false) AS c1, STRINGEQUALS("abc", "ABC", false) AS c2,  STRINGEQUALS("abc", "ABC", true) AS c3
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

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
