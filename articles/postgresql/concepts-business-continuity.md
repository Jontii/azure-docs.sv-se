---
title: Verksamhets kontinuitet – Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs verksamhets kontinuitet (tidpunkt för återställning, data Center avbrott, geo-återställning, repliker) när du använder Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/07/2020
ms.openlocfilehash: 75cd86bd1587a9294caef00efdf973fe8a26c150
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89612022"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Översikt över affärs kontinuitet med Azure Database for PostgreSQL-enskild server

Den här översikten beskriver de funktioner som Azure Database for PostgreSQL tillhandahåller för affärs kontinuitet och haveri beredskap. Lär dig mer om alternativ för att komma åt från störande händelser som kan leda till data förlust eller orsaka att databasen och programmet blir otillgängligt. Lär dig vad du ska göra när ett användar-eller program fel påverkar data integriteten, att en Azure-region har ett avbrott eller att programmet kräver underhåll.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funktioner som du kan använda för att ge affärs kontinuitet

Azure Database for PostgreSQL tillhandahåller funktioner för affärs kontinuitet som inkluderar automatiserade säkerhets kopieringar och användarens möjlighet att initiera geo-återställning. Varje har olika egenskaper för den beräknade återställnings tiden (ERT) och potentiell data förlust. Den uppskattade återställnings tiden (ERT) är den uppskattade varaktigheten för att databasen ska fungera korrekt efter en återställning/redundansväxling. När du har förstått de här alternativen kan du välja bland dem och använda dem tillsammans för olika scenarier. När du utvecklar din verksamhets kontinuitets plan måste du förstå hur lång tid det tar innan programmet återställs fullständigt efter störnings händelsen – detta är ditt återställnings tids mål (RTO). Du måste också förstå den maximala mängden senaste data uppdateringar (tidsintervall) som programmet kan tolerera vid återställning efter en störnings händelse – detta är återställnings punkt målet.

I följande tabell jämförs ERT och återställnings punkt för de tillgängliga funktionerna:

| **Kapacitet** | **Basic** | **Generell användning** | **Minnesoptimerad** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Återställning till tidpunkt från säkerhetskopia | Alla återställnings punkter inom kvarhållningsperioden | Alla återställnings punkter inom kvarhållningsperioden | Alla återställnings punkter inom kvarhållningsperioden |
| Geo-återställning från geo-replikerade säkerhets kopieringar | Stöds inte | ERT < 12 h<br/>Återställnings < 1 h | ERT < 12 h<br/>Återställnings < 1 h |

Du kan också överväga att använda [Läs repliker](concepts-read-replicas.md).

## <a name="recover-a-server-after-a-user-or-application-error"></a>Återställa en server efter ett användar-eller program fel

Du kan använda tjänstens säkerhets kopieringar för att återställa en server från olika störande händelser. En användare kan oavsiktligt ta bort vissa data, oavsiktligt släppa en viktig tabell eller till och med släppa en hel databas. Ett program kan av misstag skriva över bra data med felaktiga data på grund av ett program fel och så vidare.

Du kan utföra en tidpunkts **återställning** för att skapa en kopia av servern till en känd lämplig tidpunkt. Den här tidpunkten måste ligga inom den bevarande period för säkerhets kopior som du har konfigurerat för servern. När data har återställts till den nya servern kan du antingen ersätta den ursprungliga servern med den nyligen återställda servern eller kopiera nödvändiga data från den återställda servern till den ursprungliga servern.

> [!IMPORTANT]
> **Det går inte** att återställa borttagna servrar. Om du tar bort servern tas även alla databaser som tillhör servern bort och kan inte återställas. Använd [Azure Resource lock](../azure-resource-manager/management/lock-resources.md) för att förhindra oavsiktlig borttagning av servern.

## <a name="recover-from-an-azure-data-center-outage"></a>Återställa från ett avbrott i Azure Data Center

Även om det är ovanligt finns risken för ett avbrott på ett Azure-datacenter. När ett avbrott inträffar kan det orsaka ett verksamhets avbrott som bara tar några minuter, men som kan ha varit under några timmar.

Ett alternativ är att vänta tills servern kommer tillbaka online när avbrott i data centret är över. Detta fungerar för program som kan ge servern att vara offline under en viss tids period, till exempel en utvecklings miljö. När ett Data Center har ett avbrott vet du inte hur länge avbrottet kan ta längre tid, så det här alternativet fungerar bara om du inte behöver servern en stund.

## <a name="geo-restore"></a>Geo-återställning

Funktionen geo-Restore återställer servern med geo-redundanta säkerhets kopieringar. Säkerhets kopiorna finns i serverns [kopplade region](../best-practices-availability-paired-regions.md). Du kan återställa från dessa säkerhets kopior till andra regioner. Geo-Restore skapar en ny server med data från säkerhets kopiorna. Lär dig mer om geo-återställning från [artikeln säkerhets kopiering och återställning av begrepp](concepts-backup.md).

> [!IMPORTANT]
> Geo-återställning är bara möjlig om du har upprättat servern med Geo-redundant lagring av säkerhets kopior. Om du vill växla från lokalt redundant till geo-redundanta säkerhets kopieringar för en befintlig server måste du ta en dump med pg_dump av din befintliga server och återställa den till en nyligen skapad server som kon figurer ATS med geo-redundanta säkerhets kopior.

## <a name="cross-region-read-replicas"></a>Läs repliker i flera regioner
Du kan använda en oberoende region för att läsa och förbättra verksamhets kontinuiteten och Disaster Recovery-planeringen. Läs repliker uppdateras asynkront med hjälp av PostgreSQL-teknik för fysisk replikering. Lär dig mer om Läs repliker, tillgängliga regioner och hur du växlar över från [artikeln Läs repliker](concepts-read-replicas.md). 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
### <a name="where-does-azure-database-for-postgresql-store-customer-data"></a>Var lagrar Azure Database for PostgreSQL kund information?
Som standard flyttar Azure Database for PostgreSQL eller lagrar inte kund information från den region som den distribueras i. Kunder kan dock välja att aktivera [geo-redundanta säkerhets kopieringar](concepts-backup.md#backup-redundancy-options) eller skapa en [skrivskyddad replik i flera regioner](concepts-read-replicas.md#cross-region-replication) för att lagra data i en annan region.


## <a name="next-steps"></a>Nästa steg
- Läs mer om de [automatiska säkerhets kopieringarna i Azure Database for PostgreSQL](concepts-backup.md). 
- Lär dig hur du återställer med hjälp [av Azure Portal](howto-restore-server-portal.md) eller [Azure CLI](howto-restore-server-cli.md).
- Läs mer om att [läsa repliker i Azure Database for PostgreSQL](concepts-read-replicas.md).
