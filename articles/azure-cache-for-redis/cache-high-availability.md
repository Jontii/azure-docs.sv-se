---
title: Hög tillgänglighet för Azure cache för Redis
description: Lär dig mer om Azure cache för Redis funktioner och alternativ för hög tillgänglighet
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: yegu
ms.openlocfilehash: 145be11436eb4d0c4f6b892e5239ccacd838d780
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654221"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Hög tillgänglighet för Azure cache för Redis

Azure cache för Redis har inbyggd hög tillgänglighet. Målet med en arkitektur med hög tillgänglighet är att se till att den hanterade Redis-instansen fungerar även när de underliggande virtuella datorerna (VM) påverkas av planerade eller oplanerade avbrott. Det ger mycket högre pris taxa än vad som kan uppnås genom att vara värd för Redis på en enda virtuell dator.

Azure cache för Redis implementerar hög tillgänglighet genom att använda flera virtuella datorer, som kallas *noder*, för en cache. Den konfigurerar noderna så att datareplikering och redundans sker på koordinerade sätt. Den dirigerar också underhålls åtgärder som Redis-program uppdatering. Det finns olika alternativ för hög tillgänglighet på nivån standard och Premium:

| Alternativ | Beskrivning | Tillgänglighet | Standard | Premium |
| ------------------- | ------- | ------- | :------: | :---: |
| [Standardreplikering](#standard-replication)| Replikerad konfiguration med dubbla noder i ett enda data Center eller tillgänglighets zon (AZ) med automatisk redundans | 99,9 % |✔|✔|
| [Flera repliker](#multiple-replicas) | Replikerad konfiguration med flera noder i en eller flera AZs med automatisk redundans | 99,95% (med zon redundans) |-|✔|
| [Zonredundans](#zone-redundancy) | Replikerad konfiguration med flera noder över AZs, med automatisk redundans | 99,95% (med flera repliker) |-|✔|
| [Geo-replikering](#geo-replication) | Länkade cache-instanser i två regioner med användarspecifik redundans | 99,9% (för en enskild region) |-|✔|

## <a name="standard-replication"></a>Standardreplikering

En Azure-cache för Redis på standard-eller Premium-nivån körs på ett par Redis-servrar som standard. De två servrarna finns på dedikerade virtuella datorer. Med Redis med öppen källkod kan endast en server hantera data skrivnings begär Anden. Den här servern är den *primära* noden, medan den andra *repliken*. När den har etablerat Server noderna tilldelar Azure cache för Redis primär-och replik roller till dem. Den primära noden ansvarar vanligt vis för att underhålla Skriv-och Läs begär Anden från Redis-klienter. Vid en Skriv åtgärd, allokerar den en ny nyckel och en nyckel uppdatering till dess interna minne och svarar omedelbart på klienten. Den vidarebefordrar åtgärden till repliken asynkront.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Installation av datareplikering":::
   
>[!NOTE]
>Normalt kommunicerar en Redis-klient med den primära noden i ett Redis-cacheminne för alla Läs-och skriv förfrågningar. Vissa redis-klienter kan konfigureras att läsa från noden replikering.
>
>

Om den primära noden i en Redis-cache inte är tillgänglig, kommer repliken att befordra sig själv för att bli den nya primära servern automatiskt. Den här processen kallas för en *redundansväxling*. Repliken väntar i tillräckligt lång tid innan den tar över om den primära noden återställs snabbt. När en redundansväxling sker etablerar Azure cache för Redis en ny virtuell dator och kopplar den till cachen som noden replik. Repliken utför en fullständig datasynkronisering med den primära så att den har en annan kopia av data i cachen.

En primär nod kan gå ur drift som en del av en planerad underhålls aktivitet, till exempel Redis program vara eller operativ Systems uppdatering. Det kan också sluta fungera på grund av oplanerade händelser, till exempel fel i underliggande maskin vara, program vara eller nätverk. [Redundans och korrigering för Azure cache för Redis](cache-failover.md) innehåller en detaljerad förklaring av olika typer av Redis-redundans. En Azure-cache för Redis kommer att gå igenom många olika redundans under dess livs längd. Arkitekturen för hög tillgänglighet är utformad för att göra dessa ändringar i en cache som transparent för dess klienter som möjligt.

## <a name="multiple-replicas"></a>Flera repliker

>[!NOTE]
>Detta är tillgängligt som en för hands version.
>
>

Azure cache för Redis ger ytterligare noder i Premium-nivån. En [cachelagring med flera repliker](cache-how-to-multi-replicas.md) kan konfigureras med upp till tre noder i replikeringen. Att ha fler repliker förbättrar ofta återhämtnings förmågan på grund av de ytterligare noderna som säkerhetskopierar den primära. Även om du har fler repliker kan en Azure-cache för Redis-instans fortfarande påverkas allvarligt av ett Data Center eller AZ. Du kan öka cachens tillgänglighet genom att använda flera repliker tillsammans med [zon redundans](#zone-redundancy).

## <a name="zone-redundancy"></a>Zonredundans

>[!NOTE]
>Detta är tillgängligt som en för hands version.
>
>

Azure cache för Redis stöder Zone-redundanta konfigurationer på Premium-nivån. En [redundant cache i zonen](cache-how-to-zone-redundancy.md) kan placera sina noder i olika [Azure-tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview) i samma region. Den eliminerar Data Center eller AZ avbrott som en enskild felpunkt och ökar den övergripande tillgängligheten för din cache.

Följande diagram illustrerar den redundanta zonens konfiguration:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Installation av datareplikering":::
   
Azure cache för Redis distribuerar noder i en redundant cache för zonen i ett avrundnings sätt över den AZs som du har valt. Den avgör också vilken nod som ska fungera som primär första.

En redundant cache i zonen ger automatisk redundans. När den aktuella primära noden inte är tillgänglig tas en av replikerna över. Programmet kan få högre svars tid för cachen om den nya primära noden finns i en annan AZ. AZs är geografiskt åtskilda. Om du växlar från en AZ till en annan ändras det fysiska avståndet mellan var programmet och cachen finns. Den här ändringen påverkar fördröjning av nätverks fördröjning från ditt program till cacheminnet. Den extra svars tiden förväntas ligga inom ett acceptabelt intervall för de flesta program. Vi rekommenderar att du testar ditt program för att säkerställa att det fungerar bra med en zon-redundant cache.

## <a name="geo-replication"></a>Geo-replikering

Geo-replikering har utformats huvudsakligen för haveri beredskap. Det ger dig möjlighet att konfigurera en Azure-cache för Redis-instansen, i en annan Azure-region, för att säkerhetskopiera det primära cacheminnet. [Konfigurera geo-replikering för Azure cache för Redis](cache-how-to-geo-replication.md) ger en detaljerad förklaring om hur geo-replikering fungerar.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du konfigurerar Azure cache för Redis-alternativ med hög tillgänglighet.

* [Azure cache för Redis Premium service-nivåer](cache-overview.md#service-tiers)
* [Lägga till repliker i Azure cache för Redis](cache-how-to-multi-replicas.md)
* [Aktivera zon redundans för Azure cache för Redis](cache-how-to-zone-redundancy.md)
* [Konfigurera geo-replikering för Azure cache för Redis](cache-how-to-geo-replication.md)
