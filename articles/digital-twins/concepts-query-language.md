---
title: Frågespråk
titleSuffix: Azure Digital Twins
description: Lär dig grunderna för det digitala Azure-språket för frågor.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperfq2
ms.openlocfilehash: 89e95b0c56ce5603096fb1ac9af74cb0ad53ee6b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955233"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Om frågespråket för Azure Digitals dubbla

Kom ihåg att mitten av Azure Digitals flätas är den [dubbla grafen](concepts-twins-graph.md), som är konstruerad från digitala dubbla och relationer. 

Det här diagrammet kan tillfrågas om du vill ha information om de digitala dubbla och relationer som den innehåller. Dessa frågor skrivs i ett anpassat SQL-liknande frågespråk, som kallas **Azure Digitals**-frågespråket. Detta liknar det [IoT Hub frågespråket](../iot-hub/iot-hub-devguide-query-language.md) med många jämförbara funktioner.

I den här artikeln beskrivs grunderna för frågespråket och dess funktioner. Mer detaljerade exempel på frågesyntax och hur du kör fråge förfrågningar finns i [*instruktion: fråga det dubbla diagrammet*](how-to-query-graph.md).

## <a name="about-the-queries"></a>Om frågorna

Du kan använda Azures digitala dubbla frågespråk för att hämta digitala enheter enligt deras...
* egenskaper (inklusive [Taggegenskaper](how-to-use-tags.md))
* modeller
* relationer
  - egenskaper för relationerna

Om du vill skicka en fråga till tjänsten från en klient app använder du Azure Digital- [**fråge-API: et**](/rest/api/digital-twins/dataplane/query). Ett sätt att använda API: et är via en av [SDK: erna](how-to-use-apis-sdks.md#overview-data-plane-apis) för Azure Digitals.

## <a name="reference-expressions-and-conditions"></a>Referens: uttryck och villkor

I det här avsnittet beskrivs de operatorer och funktioner som är tillgängliga för att skriva Azure Digitals dubbla frågor. Till exempel frågor som illustrerar användningen av dessa funktioner, se [*anvisningar: fråga det dubbla diagrammet*](how-to-query-graph.md).

> [!NOTE]
> Alla frågor om Azure Digitals-frågor är Skift läges känsliga, så ta hänsyn till de exakta namnen som definieras i modellerna. Om egenskaps namnen är felstavade eller felaktigt bokstäver, är resultat uppsättningen tom utan att några fel returneras.

### <a name="operators"></a>Operatorer

Följande operatorer stöds:

| Familj | Operatorer |
| --- | --- |
| Logisk |`AND`, `OR`, `NOT` |
| Jämförelse | `=`, `!=`, `<`, `>`, `<=`, `>=` |
| Innehåller | `IN`, `NIN` |

### <a name="functions"></a>Functions

Följande typ av kontroll och data typs funktioner stöds:

| Funktion | Beskrivning |
| -------- | ----------- |
| `IS_DEFINED` | Returnerar ett booleskt värde som anger huruvida egenskapen har tilldelats ett värde. Detta stöds endast när värdet är en primitiv typ. Primitiva typer är sträng, boolesk, numerisk eller `null` . `DateTime`, objekt typer och matriser stöds inte. |
| `IS_OF_MODEL` | Returnerar ett booleskt värde som anger om den angivna dubbla matchar den angivna modell typen |
| `IS_BOOL` | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är ett booleskt värde. |
| `IS_NUMBER` | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är ett tal. |
| `IS_STRING` | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är en sträng. |
| `IS_NULL` | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är null. |
| `IS_PRIMITIVE` | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är en primitiv (sträng, boolesk, numerisk eller `null` ). |
| `IS_OBJECT` | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är ett JSON-objekt. |

Följande sträng funktioner stöds:

| Funktion | Beskrivning |
| -------- | ----------- |
| `STARTSWITH(x, y)` | Returnerar ett booleskt värde som anger om det första sträng uttrycket börjar med det andra. |
| `ENDSWITH(x, y)` | Returnerar ett booleskt värde som anger om det första sträng uttrycket slutar med det andra. |

## <a name="query-limitations"></a>Frågornas begränsningar

I det här avsnittet beskrivs begränsningar för frågespråket.

* Timing: det kan finnas en fördröjning på upp till 10 sekunder innan ändringar i din instans visas i frågor. Om du till exempel utför en åtgärd som att skapa eller ta bort dubbla med DigitalTwins-API: t kan det hända att resultatet inte omedelbart avspeglas i frågor som API-begäranden. Det bör finnas tillräckligt med väntan på en kort period för att lösa problemet.
* Det finns inte stöd för under frågor i `FROM` instruktionen.
* `OUTER JOIN` semantik stöds inte, vilket innebär att om relationen har en rangordning på noll, elimineras hela raden rad från resultat uppsättningen utdata.
* Diagram över gångs djupet är begränsat till fem `JOIN` nivåer per fråga.
* Källan för `JOIN` åtgärder är begränsad: frågan måste deklarera de dubbla platser där frågan börjar.

## <a name="next-steps"></a>Nästa steg

Lär dig att skriva frågor och se exempel på klient kod i [*anvisningar: fråga det dubbla diagrammet*](how-to-query-graph.md).