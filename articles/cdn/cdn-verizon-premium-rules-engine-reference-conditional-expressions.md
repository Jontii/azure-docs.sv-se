---
title: Villkors uttryck för Azure CDN-Verizon Premium Rules-motor
description: Referens dokumentation för Azure CDN från Verizon Premium-regel motor matchar villkor och funktioner.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 25b3c1a011e39adea651a6daa1d4ab9aa3a9d186
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "81253517"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Azure CDN från ett villkorligt uttryck i Verizon Premium Rules Engine

Den här artikeln innehåller detaljerade beskrivningar av villkors uttrycken för [regel motorn](cdn-verizon-premium-rules-engine.md)för Azure Content Delivery Network (CDN).

Den första delen av en regel är villkors uttrycket.

Villkors uttryck | Beskrivning
-----------------------|-------------
IF | Ett IF-uttryck är alltid en del av den första satsen i en regel. Precis som alla andra villkors uttryck måste den här IF-instruktionen associeras med en matchning. Om inga ytterligare villkors uttryck har definierats, avgör denna matchning det villkor som måste uppfyllas innan en uppsättning funktioner kan tillämpas på en begäran.
OCH OM | Ett och IF-uttryck får bara läggas till efter följande typer av villkors uttryck: IF, och IF. Det tyder på att det finns ett annat villkor som måste uppfyllas för den inledande IF-instruktionen.
ANNARS OM| Ett ELSE IF-uttryck anger ett alternativt villkor som måste uppfyllas innan en uppsättning funktioner som är speciella för den här ELSE-instruktionen sker. Förekomsten av en ELSE IF-instruktion anger slutet på den föregående instruktionen. Det enda villkors uttryck som kan placeras efter en ELSE IF-instruktion är en annan ELSE IF-instruktion. Det innebär att en ELSE IF-instruktion bara kan användas för att ange ett enda ytterligare villkor som måste uppfyllas.

**Exempel**: ![ CDN match villkor](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > En efterföljande regel kan åsidosätta de åtgärder som anges av en tidigare regel.
   > Exempel: en catch-all-regel skyddar alla begär Anden via Token-Based autentisering. En annan regel kan skapas direkt under den för att göra ett undantag för vissa typer av begär Anden.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure CDN](cdn-overview.md)
- [Regel motor referens](cdn-verizon-premium-rules-engine-reference.md)
- [Regel motor matchnings villkor](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regelmotor – funktioner](cdn-verizon-premium-rules-engine-reference-features.md)
- [Åsidosätta standard-HTTP-beteendet med hjälp av regel motorn](cdn-verizon-premium-rules-engine.md)