---
title: Övervaka och finjustera skalning (citus) – Azure Database for PostgreSQL
description: I den här artikeln beskrivs övervaknings-och justerings funktionerna i Azure Database for PostgreSQL-storskalig (citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: dcce4485e00415f9caa706966cac1c936c1f15f6
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690416"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Övervaka och finjustera Azure Database for PostgreSQL-storskalig (citus)

Genom att övervaka data om dina servrar kan du felsöka och optimera för din arbets belastning. Citus (storskalig) tillhandahåller olika övervaknings alternativ för att ge inblick i funktions sättet för noder i en Server grupp.

## <a name="metrics"></a>Mått

Citus (storskalig) tillhandahåller mått för varje nod i en Server grupp. Måtten ger inblick i beteendet för stöd resurser. Varje mått genereras med en minuters frekvens och har upp till 30 dagars historik.

Förutom att visa diagram över måtten kan du konfigurera aviseringar. Steg för steg-anvisningar finns i [så här konfigurerar du aviseringar](howto-hyperscale-alert-on-metric.md).  Andra uppgifter är att ställa in automatiserade åtgärder, köra avancerad analys och lagrings historik. Mer information finns i [Översikt över Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista över mått

De här måtten är tillgängliga för citus-noder (storskalig):

|Mått|Mått visnings namn|Enhet|Beskrivning|
|---|---|---|---|
|active_connections|Aktiva anslutningar|Antal|Antalet aktiva anslutningar till servern.|
|cpu_percent|CPU-procent|Procent|Procent andelen CPU som används.|
|IOPS|IOPS|Antal|Se [IOPS-definitionen](../virtual-machines/premium-storage-performance.md#iops) och [storskalig data flöde](concepts-hyperscale-configuration-options.md)|
|memory_percent|Minnes procent|Procent|Procent andelen minne som används.|
|network_bytes_ingress|Nätverk – inkommande|Byte|Nätverk i över aktiva anslutningar.|
|network_bytes_egress|Nätverk – utgående|Byte|Nätverk ut över aktiva anslutningar.|
|storage_percent|Lagrings procent|Procent|Procent andelen lagring som används av serverns högsta värde.|
|storage_used|Använt lagrings utrymme|Byte|Mängden lagring som används. Lagrings utrymmet som används av tjänsten kan omfatta databasfilerna, transaktions loggarna och Server loggarna.|

Azure levererar inga mängd mått för klustret som helhet, men mått för flera noder kan placeras i samma diagram.

## <a name="next-steps"></a>Nästa steg

- Se [hur du ställer in aviseringar](howto-hyperscale-alert-on-metric.md) för vägledning om hur du skapar en avisering på ett mått.
