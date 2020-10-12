---
title: RAND i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function RAND i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ff098da778221868b0eddc17c426d2bf36eec0fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88794330"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Returnerar ett slumpmässigt genererat numeriskt värde från [0, 1).
 
## <a name="syntax"></a>Syntax
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Retur typer

  Returnerar ett numeriskt uttryck.

## <a name="remarks"></a>Kommentarer

  `RAND` är en icke-deterministisk funktion. Upprepade anrop av `RAND` returnerar inte samma resultat. Den här system funktionen kommer inte att använda indexet.


## <a name="examples"></a>Exempel
  
  I följande exempel returneras ett slumpmässigt genererat numeriskt värde.
  
```sql
SELECT RAND() AS rand 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
