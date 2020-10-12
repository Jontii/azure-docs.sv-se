---
title: SQL-konstanter i Azure Cosmos DB
description: Läs om hur SQL Query-konstanterna i Azure Cosmos DB används för att representera ett särskilt data värde
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: cca62c358037dbe99fd16746ee081b1540161df2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "74873428"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB SQL Query-konstanter  

 En konstant, som även kallas en literal eller ett skalärt värde, är en symbol som representerar ett visst data värde. Formatet på en konstant beror på data typen för värdet som den representerar.  
  
 **Skalära data typer som stöds:**  
  
|**Typ**|**Värden ordning**|  
|-|-|  
|**Odefinierad**|Enskilt värde: **odefinierat**|  
|**Null**|Enskilt värde: **Null**|  
|**Boolesk**|Värden: **false**, **True**.|  
|**Nummer**|Ett flyttal med dubbel precision, IEEE 754-standard.|  
|**Sträng**|En sekvens med noll eller flera Unicode-tecken. Strängar måste omges av enkla eller dubbla citat tecken.|  
|**Lagringsmatriser**|En sekvens med noll eller flera element. Varje-element kan vara ett värde av vilken skalär datatyp som helst, förutom **Odefinierad**.|  
|**Jobbobjektet**|En oordnad uppsättning med noll eller flera namn/värde-par. Namnet är en Unicode-sträng, värdet kan vara av vilken skalär datatyp som helst, förutom **Odefinierad**.|  
  
## <a name="syntax"></a><a name="bk_syntax"></a>Syntax
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="arguments"></a><a name="bk_arguments"></a> Ogiltiga
  
* `<undefined_constant>; Undefined`  
  
  Representerar ett odefinierat värde av typen undefined.  
  
* `<null_constant>; null`  
  
  Representerar **Null** -värde av typen **Null**.  
  
* `<boolean_constant>`  
  
  Representerar en konstant av typen Boolean.  
  
* `false`  
  
  Representerar ett **falskt** värde av typen Boolean.  
  
* `true`  
  
  Representerar det **sanna** värdet av typen Boolean.  
  
* `<number_constant>`  
  
  Representerar en konstant.  
  
* `decimal_literal`  
  
  Decimal-litteraler är tal som representeras med decimal tecken eller matematisk notation.  
  
* `hexadecimal_literal`  
  
  Hexadecimala litteraler är tal som representeras med prefixet 0x följt av en eller flera hexadecimala siffror.  
  
* `<string_constant>`  
  
  Representerar en konstant av typen sträng.  
  
* `string _literal`  
  
  Sträng litteraler är Unicode-strängar som representeras av en sekvens med noll eller flera Unicode-tecken eller escape-sekvenser. Sträng litteraler omges av enkla citat tecken (apostrof: ') eller dubbla citat tecken (citat tecken: ").  
  
  Följande escape-sekvenser är tillåtna:  
  
|**Escape-sekvens**|**Beskrivning**|**Unicode-tecken**|  
|-|-|-|  
|\\'|apostrof (')|U + 0027|  
|\\"|citat tecken (")|U + 0022|  
|\\\ |omvänt snedstreck ( \\ )|U + 005C|  
|\\/|snedstreck (/)|U + 002F|  
|\b|Backsteg|U + 0008|  
|f|formulär inmatning|U + 000C|  
|\n|rad matning|U + 000A|  
|\r|vagn retur|U + 000D|  
|\|tabbtecken|U + 0009|  
|\uXXXX|Ett Unicode-tecken som definieras av 4 hexadecimala siffror.|U + XXXX|  

## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelldokumentdata](modeling-data.md)
