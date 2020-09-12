---
title: Azure-front dörr – URL-omskrivning | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Azure-frontend gör att URL skrivs om för dina vägar, om den har kon figurer ATS.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 8f4a6283f762d9792f50651b9caee17795df6d55
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89398945"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL-omskrivning (anpassad sökväg för vidarebefordran)
Azures front dörr stöder URL-omskrivning genom att låta dig konfigurera en valfri **anpassad vidarebefordrings Sök väg** som ska användas för att skapa begäran om att vidarebefordra den till Server delen. Om ingen sökväg för vidarebefordran har angetts kopierar Front Door som standard den inkommande URL-sökvägen till den URL som används i den vidarebefordrade begäran. Värdhuvudet som används i den vidarebefordrade begäran ser ut så som det konfigurerats för den valda serverdelen. Läs [värd rubriken för Server delen](front-door-backend-pool.md#hostheader) för att lära dig vad det gör och hur du kan konfigurera den.

Den kraftfulla delen av URL-omskrivning med anpassad vidarebefordrings Sök väg är att den kopierar någon del av den inkommande sökvägen som matchar en sökväg med jokertecken till den vidarebefordrade sökvägen (dessa Sök vägs segment är de **gröna** segmenten i exemplet nedan):
</br>
![URL-omskrivning i Azures frontend-dörr][1]

## <a name="url-rewrite-example"></a>Exempel på URL-omskrivning
Överväg en regel för routning med följande klient dels värdar och konfigurerade sökvägar:

| Värdar      | Sökvägar       |
|------------|-------------|
| www- \. contoso.com | /\*         |
|            | /foo        |
|            | foo\*     |
|            | /foo/bar/\* |

Den första kolumnen i tabellen nedan visar exempel på inkommande begär Anden och den andra kolumnen visar vad som skulle vara den "mest aktuella" matchande väg Sök väg ".  De tredje och efterföljande kolumnerna i den första raden i tabellen är exempel på konfigurerade **anpassade vidarebefordrande sökvägar**, med resten av raderna i dessa kolumner som representerar exempel på vad den vidarebefordrade begär ande sökvägen skulle vara om den matchade med begäran på raden.

Om vi t. ex. läser på den andra raden, säger vi att för inkommande begäran `www.contoso.com/sub` , om den anpassade sökvägen för vidarebefordran var `/` , så skulle den vidarebefordrande sökvägen vara `/sub` . Om den anpassade vidarebefordrande sökvägen var `/fwd/` , är den vidarebefordrade sökvägen `/fwd/sub` . Och så vidare, för de återstående kolumnerna. De **betonade** delarna av Sök vägarna nedan representerar de delar som är en del av matchningen av jokertecken.


| Inkommande begäran       | Mest speciell matchnings Sök väg | /          | /fwd/          | foo          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www- \. contoso.com/            | /\*                      | /          | /fwd/          | foo          | /foo/bar/          |
| www- \. contoso.com/**Sub**     | /\*                      | /**Build**   | /FWD/**Sub**   | /foo/**Sub**   | /foo/bar/**Sub**   |
| www \. **-contoso.com/a/b/c**   | /\*                      | /**a/b/c** | /FWD/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www- \. contoso.com/foo         | /foo                     | /          | /fwd/          | foo          | /foo/bar/          |
| www- \. contoso.com/foo/        | foo\*                  | /          | /fwd/          | foo          | /foo/bar/          |
| www \. contoso.com/foo/-**stapel** | foo\*                  | /**Valle**   | /FWD/-**fält**   | /foo/-**fält**   | /foo/bar/-**fält**   |


## <a name="optional-settings"></a>Valfria inställningar
Det finns ytterligare valfria inställningar som du kan ange för alla inställningar för routningsregler:

* **Cache-konfiguration** – om det är inaktiverat eller ej angivet kommer begär Anden som matchar den här regeln inte att försöka använda cachelagrat innehåll. i stället hämtas de alltid från Server delen. Läs mer om [cachelagring med front dörren](front-door-caching.md).



## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg