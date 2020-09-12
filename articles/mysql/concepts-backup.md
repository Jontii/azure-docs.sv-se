---
title: Säkerhets kopiering och återställning – Azure Database for MySQL
description: Lär dig mer om automatisk säkerhets kopiering och att återställa Azure Database for MySQL-servern.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 4a6f6a052269bbfef6cafb359626031692a7d9c6
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2020
ms.locfileid: "89418593"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>Säkerhets kopiering och återställning i Azure Database for MySQL

Azure Database for MySQL skapar automatiskt Server säkerhets kopior och lagrar dem i användar konfiguration lokalt redundant eller Geo-redundant lagring. Säkerhetskopieringar kan användas för att återställa servern till en vald tidpunkt. Säkerhets kopiering och återställning är en viktig del av en strategi för affärs kontinuitet eftersom de skyddar dina data från oavsiktlig skada eller borttagning.

## <a name="backups"></a>Säkerhetskopior

Azure Database for MySQL säkerhetskopierar datafilerna och transaktions loggen. Beroende på den maximala lagrings storleken som stöds tar vi antingen fullständiga och differentiella säkerhets kopieringar (4 TB max lagrings servrar) eller säkerhets kopior av ögonblicks bilder (upp till 16 TB max lagrings servrar). Med dessa säkerhets kopieringar kan du återställa en server till alla tidpunkter inom den konfigurerade kvarhållningsperioden för säkerhets kopior. Standard kvarhållningsperioden för säkerhets kopiering är sju dagar. Du kan [också konfigurera det](howto-restore-server-portal.md#set-backup-configuration) upp till 35 dagar. Alla säkerhets kopior krypteras med AES 256-bitars kryptering.

De här säkerhetskopierade filerna är inte användare-exponerade och kan inte exporteras. Dessa säkerhets kopior kan bara användas för återställnings åtgärder i Azure Database for MySQL. Du kan använda [mysqldump](concepts-migrate-dump-restore.md) för att kopiera en databas.

### <a name="backup-frequency"></a>Säkerhetskopieringsfrekvens

#### <a name="servers-with-up-to-4-tb-storage"></a>Servrar med upp till 4 TB lagrings utrymme

För servrar som har stöd för upp till 4 TB högsta lagrings utrymme sker fullständiga säkerhets kopieringar varje vecka. Differentiella säkerhets kopieringar sker två gånger om dagen. Säkerhets kopieringar av transaktions loggar sker var femte minut.

#### <a name="servers-with-up-to-16-tb-storage"></a>Servrar med upp till 16 TB lagring
I en delmängd av [Azure-regioner](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage)kan alla nyligen etablerade servrar stödja upp till 16 TB lagring. Säkerhets kopieringar på dessa stora lagrings servrar är Snapshot-baserade. Den första fullständiga säkerhets kopieringen schemaläggs omedelbart efter att en server har skapats. Den första fullständiga säkerhets kopieringen behålls som serverns grundläggande säkerhets kopiering. Efterföljande ögonblicks säkerhets kopieringar är bara differentiella säkerhets kopieringar. 

Differentiella ögonblicks bild säkerhets kopieringar sker minst en gång per dag. Säkerhets kopiering av differentiella ögonblicks bilder sker inte enligt ett fast schema. Säkerhets kopiering av differentiella ögonblicks bilder sker var 24: e timme om transaktions loggen (BinLog i MySQL) överskrider 50 GB sedan den senaste differentiella säkerhets kopieringen. I en dag tillåts högst sex differentiella ögonblicks bilder. 

Säkerhets kopieringar av transaktions loggar sker var femte minut. 

### <a name="backup-retention"></a>Kvarhållningsperiod för säkerhetskopior

Säkerhets kopior bevaras baserat på inställningen för kvarhållning av säkerhets kopior på servern. Du kan välja en kvarhållningsperiod på 7 till 35 dagar. Standard kvarhållningsperioden är 7 dagar. Du kan ställa in kvarhållningsperioden när servern skapas eller senare genom att uppdatera säkerhets kopierings konfigurationen med [Azure Portal](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal#set-backup-configuration) eller [Azure CLI](https://docs.microsoft.com/azure/mysql/howto-restore-server-cli#set-backup-configuration). 

Kvarhållningsperioden för säkerhets kopior styr hur långt tillbaka i tiden en tidpunkts återställning kan hämtas, eftersom den baseras på tillgängliga säkerhets kopior. Kvarhållningsperioden för säkerhets kopior kan också behandlas som ett återställnings fönster från ett återställnings perspektiv. Alla säkerhets kopior som krävs för att utföra en återställning efter en viss tidpunkt inom kvarhållning av säkerhets kopior bevaras i säkerhets kopierings lagringen. Om säkerhets kopierings perioden till exempel är 7 dagar, betraktas återställnings fönstret de senaste 7 dagarna. I det här scenariot behålls alla säkerhets kopior som krävs för att återställa servern under de senaste 7 dagarna. Med ett fönster för kvarhållning av säkerhets kopior av sju dagar:
- Servrar med upp till 4 TB lagrings utrymme behåller upp till 2 fullständiga säkerhets kopieringar av databaser, alla differentiella säkerhets kopieringar och säkerhets kopieringar av transaktions loggar som utförs sedan den tidigaste fullständiga säkerhets kopieringen
-   Servrar med upp till 16 TB lagring behåller den fullständiga ögonblicks bilden av databasen, alla differentiella ögonblicks bilder och säkerhets kopieringar av transaktions loggar de senaste 8 dagarna.

### <a name="backup-redundancy-options"></a>Alternativ för redundans för säkerhets kopiering

Azure Database for MySQL ger flexibiliteten att välja mellan lokalt redundant eller Geo-redundant lagring av säkerhets kopior i Generell användning och minnesoptimerade nivåer. När säkerhets kopiorna lagras i Geo-redundant lagring av säkerhets kopior lagras de inte bara i den region där servern finns, men replikeras också till ett [parat Data Center](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Detta ger bättre skydd och möjlighet att återställa servern i en annan region i händelse av en katastrof. Basic-nivån erbjuder endast lokalt redundant säkerhets kopierings lagring.

> [!IMPORTANT]
> Det går bara att konfigurera lokalt redundant eller Geo-redundant lagring för säkerhets kopiering när servern skapas. När servern har tillhandahållits kan du inte ändra redundans alternativet för lagring av säkerhets kopior.

### <a name="backup-storage-cost"></a>Reserv lagrings kostnad

Azure Database for MySQL tillhandahåller upp till 100% av din etablerade Server lagring som säkerhets kopierings lagring utan extra kostnad. Ytterligare lagrings utrymme för säkerhets kopior debiteras i GB per månad. Om du till exempel har etablerad en server med 250 GB lagrings utrymme har du 250 GB ytterligare lagrings utrymme för Server säkerhets kopior utan extra kostnad. Förbrukad lagring för säkerhets kopieringar över 250 GB debiteras enligt [pris sättnings modellen](https://azure.microsoft.com/pricing/details/mysql/). 

Du kan använda måttet [säkerhets kopierings lagring som används](concepts-monitoring.md) i Azure Monitor tillgängligt via Azure Portal för att övervaka säkerhets kopierings lagringen som används av en server. Måttet mått för säkerhets kopierings lagring representerar summan av lagrings utrymme som förbrukas av alla fullständiga säkerhets kopior av databasen, differentiella säkerhets kopior och logg säkerhets kopior som bevaras baserat på den kvarhållna säkerhets kopierings perioden för servern. Säkerhets kopierings frekvensen är tjänsten hanterad och förklaras tidigare. Tung transaktions aktivitet på servern kan orsaka att lagrings användningen av säkerhets kopieringen ökar oberoende av databasens totala storlek. För Geo-redundant lagring är lagrings utrymmet för säkerhets kopiering två gånger för det lokalt redundanta lagrings utrymmet. 

Det främsta sättet att kontrol lera lagrings kostnaden för säkerhets kopiering är genom att ställa in lämplig kvarhållningsperiod för säkerhets kopior och välja rätt alternativ för säkerhets kopiering för att uppfylla önskade återställnings mål. Du kan välja en kvarhållningsperiod från mellan 7 och 35 dagar. Generell användning-och Minnesoptimerade servrar kan välja att ha Geo-redundant lagring för säkerhets kopiering.

## <a name="restore"></a>Återställ

I Azure Database for MySQL skapar en återställning en ny server från den ursprungliga serverns säkerhets kopior och återställer alla databaser som finns på servern.

Det finns två typer av återställning:

- **Återställning på plats-till-tid** är tillgängligt med alternativ för redundans och skapar en ny server i samma region som den ursprungliga servern som använder kombinationen av fullständiga säkerhets kopieringar och säkerhets kopieringar av transaktions loggar.
- **Geo-återställning** är bara tillgängligt om du har konfigurerat servern för Geo-redundant lagring och du kan återställa servern till en annan region som använder den senaste säkerhets kopian.

Den uppskattade återställnings tiden beror på flera faktorer, till exempel databasens storlek, transaktions loggens storlek, nätverks bandbredden och det totala antalet databaser som återställs i samma region på samma tid. Återställnings tiden är vanligt vis mindre än 12 timmar.

> [!IMPORTANT]
> **Det går inte** att återställa borttagna servrar. Om du tar bort servern tas även alla databaser som tillhör servern bort och kan inte återställas. För att skydda server resurser, efter distribution, från oavsiktlig borttagning eller oväntade ändringar, kan administratörer utnyttja [hanterings lås](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Återställning från tidpunkt

Oberoende av ditt alternativ för säkerhets kopiering kan du utföra en återställning till vilken tidpunkt som helst inom lagrings perioden för säkerhets kopiorna. En ny server skapas i samma Azure-region som den ursprungliga servern. Den skapas med den ursprungliga serverns konfiguration för pris nivån, beräknings generering, antalet virtuella kärnor, lagrings storlek, kvarhållning av säkerhets kopior och alternativet för redundans.

> [!NOTE]
> Det finns två server parametrar som återställs till standardvärdena (och kopieras inte över från den primära servern) efter återställnings åtgärden
> * time_zone – det här värdet ställs in på standardvärdet **system**
> * event_scheduler – event_scheduler har angetts till **av** på den återställda servern
>
> Du måste ange dessa Server parametrar genom att konfigurera om [Server parametern](howto-server-parameters.md)

Återställning av tidpunkt är användbart i flera scenarier. Till exempel när en användare oavsiktligt tar bort data, släpper en viktig tabell eller databas, eller om ett program av misstag skriver över bra data med felaktiga data på grund av ett program fel.

Du kan behöva vänta tills nästa säkerhets kopiering av transaktions loggen tas innan du kan återställa till en tidpunkt under de senaste fem minuterna.

### <a name="geo-restore"></a>Geo-återställning

Du kan återställa en server till en annan Azure-region där tjänsten är tillgänglig om du har konfigurerat servern för geo-redundanta säkerhets kopieringar. Servrar som har stöd för upp till 4 TB lagrings utrymme kan återställas till den geo-kopplade regionen eller till en region som har stöd för upp till 16 TB lagring. För servrar som har stöd för upp till 16 TB lagrings utrymme kan geo-säkerhetskopieringar återställas i valfri region som har stöd för 16 TB-servrar. Granska [Azure Database for MySQL pris nivåer](concepts-pricing-tiers.md) för listan över regioner som stöds.

Geo-återställning är standard alternativet för återställning när servern inte är tillgänglig på grund av en incident i den region där-servern finns. Om en storskalig incident i en region resulterar i att databas programmet inte är tillgängligt, kan du återställa en server från de geo-redundanta säkerhets kopieringarna till en server i någon annan region. Geo-återställning använder den senaste säkerhets kopian av servern. Det uppstår en fördröjning mellan när en säkerhets kopia tas och när den replikeras till en annan region. Den här fördröjningen kan vara upp till en timme, så om en katastrof inträffar kan det vara upp till en timmes data förlust.

Vid geo-återställning kan de serverkonfigurationer som kan ändras omfatta beräknings generering, vCore, bevarande period för säkerhets kopior och alternativ för säkerhets kopiering. Det finns inte stöd för att ändra pris nivå (Basic, Generell användning eller Minnesoptimerade) eller lagrings storlek under geo-återställning.

### <a name="perform-post-restore-tasks"></a>Utföra uppgifter efter återställning

Efter en återställning från återställnings metoden bör du utföra följande uppgifter för att få dina användare och program att säkerhetskopiera och köra dem:

- Om den nya servern är avsedd att ersätta den ursprungliga servern omdirigerar du klienter och klient program till den nya servern
- Se till att det finns lämpliga VNet-regler för att användare ska kunna ansluta. De här reglerna kopieras inte från den ursprungliga servern.
- Se till att lämpliga inloggningar och behörigheter på databas nivå är på plats
- Konfigurera aviseringar efter behov

## <a name="next-steps"></a>Nästa steg

- Mer information om verksamhets kontinuitet finns i [Översikt över affärs kontinuitet](concepts-business-continuity.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure Portal, se [restore server till en tidpunkt med hjälp av Azure Portal](howto-restore-server-portal.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure CLI kan du se [restore Server to Point-in-Time-using CLI](howto-restore-server-cli.md).
