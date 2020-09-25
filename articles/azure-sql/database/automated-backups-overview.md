---
title: Automatisk och Geo-redundant säkerhets kopiering
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database och Azure SQL-hanterad instans skapar automatiskt en lokal databas säkerhets kopia med några minuters mellanrum och använder Azure Read-Access Geo-redundant lagring för GEO-redundans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: references_regions
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein, danil
ms.date: 08/04/2020
ms.openlocfilehash: 24611853749a5fa675b8c26d5e27c18e44590eaf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91284745"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Automatiserade säkerhets kopieringar – Azure SQL Database & SQL-hanterad instans

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>Vad är en databas säkerhets kopia?

Databas säkerhets kopieringar är en viktig del av en affärs kontinuitet och katastrof återställnings strategi, eftersom de skyddar dina data från att skadas eller tas bort. Dessa säkerhets kopior aktiverar återställning av databasen till en tidpunkt inom den konfigurerade kvarhållningsperioden. Om dina data skydds regler kräver att dina säkerhets kopior är tillgängliga under en längre tid (upp till 10 år) kan du konfigurera [långsiktig kvarhållning](long-term-retention-overview.md) för både enkla databaser och databaser i pooler.

### <a name="backup-frequency"></a>Säkerhetskopieringsfrekvens

Både SQL Database-och SQL-hanterad instans använder SQL Server teknik för att skapa [fullständiga säkerhets kopior](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) varje vecka, [differentiella säkerhets kopior](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) var 12-24 timme och [säkerhets kopieringar av transaktions loggar](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) var 5 till 10 minuter. Frekvensen för säkerhets kopiering av transaktions loggar baseras på beräknings storlek och mängden databas aktivitet.

När du återställer en databas fastställer tjänsten vilka säkerhets kopior av fullständig, differentiell och transaktions logg som måste återställas.

### <a name="backup-storage-redundancy"></a>Redundans för lagring av säkerhets kopior

Som standard är SQL Database och SQL-hanterad instans lagra data i Geo-redundanta (RA-GRS) [lagrings-blobar](../../storage/common/storage-redundancy.md) som replikeras till en [kopplad region](../../best-practices-availability-paired-regions.md). Detta hjälper till att skydda mot avbrott som påverkar säkerhets kopierings lagringen i den primära regionen och gör att du kan återställa servern till en annan region i händelse av en katastrof. 

SQL-hanterad instans ger möjlighet att ändra lagrings redundans till antingen lokalt redundant (LRS) eller zoner som är redundant (ZRS) för att säkerställa att dina data ligger inom samma region som din hanterade instans distribueras till. Metoder för redundans för lagring lagrar flera kopior av dina data så att de skyddas från planerade och oplanerade händelser, inklusive tillfälligt maskin varu haveri, nätverks-eller strömavbrott, eller massiv natur katastrofer. 

Alternativet för att konfigurera redundans för säkerhets kopiering ger flexibiliteten att välja mellan LRS-, ZRS-eller RA-GRS-lagringsmatriser för en SQL-hanterad instans. Konfigurera redundans för säkerhets kopierings lagring under processen för skapande av hanterade instanser när resursen har skapats går det inte längre att ändra lagrings redundansen. (Zon-redundant lagring (ZRS) är för närvarande endast tillgängligt i [vissa regioner](../../storage/common/storage-redundancy.md#zone-redundant-storage)).


> [!IMPORTANT]
> I SQL-hanterad instans tillämpas den konfigurerade redundansen för säkerhets kopiering på både kortsiktiga säkerhets kopior som används för återställnings punkt i tid (PITR) och säkerhets kopior med långsiktig kvarhållning som används för långsiktiga säkerhets kopior (brv).


> [!NOTE]
> Azure SQL Database konfigurerbar redundans för säkerhets kopiering är för närvarande tillgängligt som en begränsad privat för hands version för vissa kunder i Sydostasien Azure-region. Om du vill ta hänsyn till registreringen i den här privata förhands granskningen kontaktar du [sqlbackuppreview@microsoft.com](mailto:sqlbackuppreview@microsoft.com) . 

Om dina data skydds regler kräver att dina säkerhets kopior är tillgängliga under en längre tid (upp till 10 år) kan du konfigurera [långsiktig kvarhållning](long-term-retention-overview.md) för både enkla databaser och databaser i pooler.

### <a name="backup-usage"></a>Säkerhets kopierings användning


Du kan använda de här säkerhetskopiorna för att:

- **Återställning av den befintliga databasen**  -  vid en viss tidpunkt [Återställ en befintlig databas till en tidpunkt som tidigare varit](recovery-using-backups.md#point-in-time-restore) inom kvarhållningsperioden med hjälp av Azure Portal, Azure PowerShell, Azure CLI eller REST API. För SQL Database skapar den här åtgärden en ny databas på samma server som den ursprungliga databasen, men använder ett annat namn för att undvika att skriva över den ursprungliga databasen. När återställningen är klar kan du ta bort den ursprungliga databasen. Alternativt kan du [byta namn](https://docs.microsoft.com/sql/relational-databases/databases/rename-a-database) på både den ursprungliga databasen och byta namn på den återställda databasen till det ursprungliga databas namnet. På samma sätt skapar den här åtgärden en kopia av databasen på samma eller en annan hanterad instans i samma prenumeration och samma region för SQL-hanterad instans.
- **Återställning av borttagen tidpunkt för borttagen databas**  -  [Återställ en borttagen databas till tiden för borttagning](recovery-using-backups.md#deleted-database-restore) eller till någon tidpunkt inom kvarhållningsperioden. Den borttagna databasen kan bara återställas på samma server eller hanterade instans där den ursprungliga databasen skapades. När du tar bort en databas tar tjänsten en slutgiltig säkerhets kopia av transaktions loggen innan den tas bort, för att förhindra data förlust.
- **Geo-återställning**  -  [Återställ en databas till en annan geografisk region](recovery-using-backups.md#geo-restore). Med geo-återställning kan du återställa från en geografisk katastrof när du inte kan komma åt databasen eller säkerhets kopiorna i den primära regionen. Den skapar en ny databas på en befintlig server eller hanterad instans, i valfri Azure-region.
   > [!IMPORTANT]
   > Geo-återställning är endast tillgängligt för hanterade instanser med konfigurerad Geo-redundant (RA-GRS) säkerhets kopierings lagring.
- **Återställ från långsiktig säkerhets kopia**  -  [Återställ en databas från en viss långsiktig säkerhets kopia](long-term-retention-overview.md) av en databas eller databas i pooler, om databasen har kon figurer ATS med en långsiktig bevarande princip (brv). Med LTR kan du återställa en gammal version av databasen med hjälp av [Azure Portal](long-term-backup-retention-configure.md#using-the-azure-portal) eller [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) för att uppfylla en begäran om efterlevnad eller köra en gammal version av programmet. Mer information finns i avsnittet om [långsiktig kvarhållning](long-term-retention-overview.md).

Information om hur du utför en återställning finns i [återställa databasen från säkerhets kopior](recovery-using-backups.md).

> [!NOTE]
> I Azure Storage refererar termen *replikering* till att kopiera blobbar från en plats till en annan. I SQL syftar *databasreplikering* på olika tekniker som används för att hålla flera sekundära databaser synkroniserade med en primär databas.

Du kan prova att säkerhetskopiera konfigurationen och återställnings åtgärderna med hjälp av följande exempel:

| Åtgärd | Azure Portal | Azure PowerShell |
|---|---|---|
| **Ändra kvarhållning av säkerhets kopior** | [SQL Database](automated-backups-overview.md?tabs=single-database#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [SQL-hanterad instans](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [SQL Database](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[SQL-hanterad instans](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **Ändra långsiktig kvarhållning av säkerhets kopior** | [SQL Database](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>SQL-hanterad instans-saknas  | [SQL Database](long-term-backup-retention-configure.md)<br/>[SQL-hanterad instans](../managed-instance/long-term-backup-retention-configure.md)  |
| **Återställa en databas från en tidpunkt** | [SQL Database](recovery-using-backups.md#point-in-time-restore)<br>[SQL-hanterad instans](../managed-instance/point-in-time-restore.md) | [SQL Database](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [SQL-hanterad instans](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **Återställa en borttagen databas** | [SQL Database](recovery-using-backups.md)<br>[SQL-hanterad instans](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [SQL Database](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [SQL-hanterad instans](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **Återställa en databas från Azure Blob Storage** | SQL Database-saknas <br/>SQL-hanterad instans-saknas  | SQL Database-saknas <br/>[SQL-hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-scheduling"></a>Schemalägg säkerhets kopiering

Den första fullständiga säkerhets kopieringen schemaläggs direkt efter att en ny databas har skapats eller återställts. Den här säkerhets kopian slutförs vanligt vis inom 30 minuter, men det kan ta längre tid när databasen är stor. Den första säkerhets kopieringen kan till exempel ta längre tid på en återställd databas eller en databas kopia som normalt skulle vara större än en ny databas. Efter den första fullständiga säkerhets kopieringen schemaläggs och hanteras alla ytterligare säkerhets kopieringar automatiskt. Den exakta tiden för alla säkerhets kopieringar av databasen bestäms av SQL Database-eller SQL-hanterade instans tjänsten eftersom den balanserar den övergripande system belastningen. Du kan inte ändra schemat för säkerhets kopierings jobb eller inaktivera dem.

> [!IMPORTANT]
> För en ny, återställd eller kopierad databas blir återställnings funktionen för tidpunkt tillgänglig från den tidpunkt då den inledande säkerhets kopian av transaktions loggen som följer den första fullständiga säkerhets kopieringen skapas.

## <a name="backup-storage-consumption"></a>Lagrings förbrukning för säkerhets kopiering

Med SQL Server säkerhets kopiering och återställning måste du återställa en databas till en tidpunkt som består av en fullständig säkerhets kopia, eventuellt en differentiell säkerhets kopia och en eller flera säkerhets kopior av transaktions loggen. Säkerhets kopierings schema för SQL Database och SQL-hanterad instans innehåller en fullständig säkerhets kopia varje vecka. För att aktivera PITR inom hela kvarhållningsperioden måste systemet lagra ytterligare fullständiga, differentiella och transaktions logg säkerhets kopior i upp till en vecka längre än den konfigurerade kvarhållningsperioden. 

Med andra ord, för alla tidpunkter under kvarhållningsperioden, måste det finnas en fullständig säkerhets kopia som är äldre än den äldsta tiden för kvarhållningsperioden, samt en oavbruten kedja av differentiella och säkerhets kopior av transaktions loggar från den fullständiga säkerhets kopian fram till nästa fullständiga säkerhets kopiering.

> [!NOTE]
> Om du vill aktivera PITR lagras ytterligare säkerhets kopior i upp till en vecka längre än den konfigurerade kvarhållningsperioden. Lagring av säkerhets kopior debiteras till samma pris för alla säkerhets kopieringar. 

Säkerhets kopieringar som inte längre behövs för att tillhandahålla PITR-funktioner tas bort automatiskt. Eftersom differentiella säkerhets kopior och logg säkerhets kopior kräver en tidigare fullständig säkerhets kopiering för att bli återställas rensas alla tre säkerhets kopierings typerna tillsammans i vecko uppsättningar.

För alla databaser, inklusive [TDE-krypterade](transparent-data-encryption-tde-overview.md) databaser, komprimeras säkerhets kopior för att minska lagrings komprimering och kostnader för säkerhets kopiering. Genomsnittlig komprimerings kvot för säkerhets kopiering är 3-4 gånger, men det kan vara betydligt lägre eller högre beroende på datans beskaffenhet och om data komprimering används i-databasen.

SQL Database-och SQL-hanterad instans beräknar din totala använda lagring av säkerhets kopior som ett ackumulerat värde. Varje timme rapporteras det här värdet till Azures fakturerings pipeline, som är ansvarig för att aggregera denna användning per timme för att beräkna förbrukningen i slutet av varje månad. När databasen har tagits bort minskar förbrukningen när säkerhets kopiorna åldras och tas bort. När alla säkerhets kopior har tagits bort och PITR inte längre är möjliga, stoppas faktureringen.
   
> [!IMPORTANT]
> Säkerhets kopieringar av en databas behålls för att aktivera PITR även om databasen har tagits bort. När du tar bort och återskapar en databas kan du spara lagrings-och beräknings kostnader, men det kan öka kostnaderna för lagring av säkerhets kopior, eftersom tjänsten behåller säkerhets kopiorna för varje borttagen databas varje gång den tas bort. 

### <a name="monitor-consumption"></a>Övervaka förbrukning

För vCore-databaser rapporteras det lagrings utrymme som förbrukas av varje typ av säkerhets kopiering (fullständig, differentiell och logg) på bladet databas övervakning som ett separat mått. Följande diagram visar hur du övervakar användningen av lagrings utrymme för säkerhets kopior för en enskild databas. Den här funktionen är för närvarande inte tillgänglig för hanterade instanser.

![Övervaka användningen av säkerhets kopiering av databasen i Azure Portal](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Finjustera lagrings förbrukning för säkerhets kopiering

Lagrings förbrukningen för säkerhets kopiering upp till den maximala data storleken för en databas debiteras inte. Överförbrukning av lagrings utrymme för säkerhets kopiering beror på arbets belastningen och den maximala storleken på de enskilda databaserna. Överväg några av följande justerings metoder för att minska användningen av lagrings utrymme för säkerhets kopior:

- Minska [lagrings perioden för säkerhets kopior](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) till det minimum som krävs för dina behov.
- Undvik att utföra stora Skriv åtgärder, till exempel indexera återskapare, oftare än du behöver.
- För stora data inläsnings åtgärder bör du överväga att använda [grupperade columnstore-index](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) och följande relaterade [metod tips](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)och/eller minska antalet icke-grupperade index.
- På den Generell användning tjänst nivån är den allokerade data lagringen billigare än priset för lagring av säkerhets kopior. Om du har kontinuerligt höga kostnader för säkerhets kopierings lagring kan du överväga att öka lagrings utrymmet för säkerhets kopieringen.
- Använd TempDB i stället för permanenta tabeller i program logiken för att lagra tillfälliga resultat och/eller tillfälliga data.
- Använd lokalt redundant lagring av säkerhets kopior när det är möjligt (till exempel dev/test-miljöer)

## <a name="backup-retention"></a>Kvarhållningsperiod för säkerhetskopior

För alla nya, återställda och kopierade databaser behåller Azure SQL Database och Azure SQL-hanterade instanser tillräckligt med säkerhets kopior för att tillåta PITR inom de senaste 7 dagarna som standard. Med undantag för storskaliga databaser kan du [ändra kvarhållningsperioden för säkerhets kopior](#change-the-pitr-backup-retention-period) per aktiv databas inom intervallet 1-35 dag. Som det beskrivs i [säkerhets kopierings lagrings förbrukningen](#backup-storage-consumption)kan säkerhets kopior som lagras för att aktivera PITR vara äldre än kvarhållningsperioden. Endast för Azure SQL-hanterad instans är det möjligt att ange PITR säkerhets kopierings frekvens när en databas har tagits bort inom intervallet 0-35 dagar. 

Om du tar bort en databas behåller systemet säkerhets kopior på samma sätt som en online-databas med den angivna kvarhållningsperioden. Det går inte att ändra kvarhållningsperioden för säkerhets kopior för en borttagen databas.

> [!IMPORTANT]
> Om du tar bort en server eller en hanterad instans, tas även alla databaser på servern eller den hanterade instansen bort och kan inte återställas. Det går inte att återställa en borttagen Server eller hanterad instans. Men om du har konfigurerat långsiktig kvarhållning (brv) för en databas eller hanterad instans, raderas inte säkerhets kopiorna för långsiktig kvarhållning och kan användas för att återställa databaser på en annan server eller hanterad instans i samma prenumeration, till en tidpunkt då en säkerhets kopia av långsiktig kvarhållning gjordes.

Kvarhållning av säkerhets kopior för PITR inom de senaste 1-35 dagarna kallas ibland kvarhållning av kortsiktig säkerhets kopia. Om du behöver behålla säkerhets kopiorna längre än den högsta kortsiktiga kvarhållningsperioden på 35 dagar kan du aktivera [långsiktig kvarhållning](long-term-retention-overview.md).

### <a name="long-term-retention"></a>Långsiktig kvarhållning

För både SQL Database-och SQL-hanterad instans kan du konfigurera fullständig säkerhets kopiering långsiktig kvarhållning (brv) i upp till 10 år i Azure Blob Storage. När LTR-principen har kon figurer ATS kopieras fullständiga säkerhets kopior automatiskt till en annan lagrings behållare varje vecka. För att uppfylla olika krav på efterlevnad kan du välja olika bevarande perioder för varje vecka, månads vis och/eller årligen fullständiga säkerhets kopieringar. Lagrings förbrukningen beror på den valda frekvensen och kvarhållningsperioden för säkerhets kopiering från LTR. Du kan använda pris listan för [vanlig pris sättning](https://azure.microsoft.com/pricing/calculator/?service=sql-database) för att beräkna kostnaden för LTR Storage.

För ytterligare information om LTR, se [långsiktig kvarhållning av säkerhets kopior](long-term-retention-overview.md).

## <a name="storage-costs"></a>Lagringskostnader

Priset för lagring av säkerhets kopior varierar och beror på inköps modellen (DTU eller vCore), valt alternativ för lagring av säkerhets kopior och även på din region. Lagring av säkerhets kopior debiteras per GB/månad för prissättningen se [Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) prissättnings sida och pris sida för [Azure SQL-hanterad instans](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/) .

### <a name="dtu-model"></a>DTU-modell

I DTU-modellen finns det ingen extra kostnad för lagring av säkerhets kopior för databaser och elastiska pooler. Priset för lagring av säkerhets kopior är en del av priset för databaser eller pooler.

### <a name="vcore-model"></a>vCore-modellen

För enskilda databaser i SQL Database ges ett lagrings utrymme för säkerhets kopior som motsvarar 100 procent av den maximala data lagrings storleken för databasen utan extra kostnad. För elastiska pooler och hanterade instanser tillhandahålls en lagrings mängd för säkerhets kopior som motsvarar 100 procent av den maximala data lagringen för poolen eller den maximala instans lagrings storleken. 

För enskilda databaser används den här ekvationen för att beräkna den totala mängden fakturerbara säkerhets kopierings lagring:

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

För databaser i pooler aggregeras den totala mängden fakturerbara lagrings utrymmet på Poolnivå och beräknas enligt följande:

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

För hanterade instanser aggregeras den totala mängden fakturerbara lagrings utrymmet på instans nivån och beräknas på följande sätt:

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

Totalt antal fakturerbara lagrings enheter för säkerhets kopior debiteras i GB/månad. Den här lagrings förbrukningen för säkerhets kopiering beror på arbets belastningen och storleken på enskilda databaser, elastiska pooler och hanterade instanser. Kraftigt ändrade databaser har större differentiella och logg säkerhets kopior, eftersom storleken på dessa säkerhets kopieringar är proportionell mot mängden data som ändras. Därför kommer sådana databaser att ha högre kostnader för säkerhets kopiering.

SQL Database-och SQL-hanterad instans beräknar den totala fakturerbara säkerhets kopierings lagringen som ett ackumulerat värde för alla säkerhetskopieringsfiler. Varje timme rapporteras det här värdet till Azures fakturerings pipeline, som sammanställer den här Tim användningen för att hämta din användning av lagrings utrymme för säkerhets kopior i slutet av varje månad. Om en databas tas bort minskar lagrings förbrukningen för säkerhets kopiering gradvis när äldre säkerhets kopieringar åldras och tas bort. Eftersom differentiella säkerhets kopior och logg säkerhets kopior kräver en tidigare fullständig säkerhets kopiering för att bli återställas rensas alla tre säkerhets kopierings typerna tillsammans i vecko uppsättningar. När alla säkerhets kopior tas bort stoppas faktureringen. 

Som ett förenklat exempel förutsätter vi att en databas har samlat 744 GB lagrings utrymme för säkerhets kopior och att denna mängd förblir konstant under en hel månad eftersom databasen är helt inaktiv. Om du vill konvertera den här kumulativa lagrings användningen till per timme delar du den med 744,0 (31 dagar per månad * 24 timmar per dag). SQL Database rapporterar till Azure-fakturerings pipelinen att databasen förbrukade 1 GB av PITR-säkerhetskopiering varje timme enligt en konstant taxa. Azure-faktureringen sammanställer den här förbrukningen och visar en användning på 744 GB för hela månaden. Kostnaden baseras på pris/GB/månads pris i din region.

Nu är ett mer avancerat exempel. Anta att samma inaktiva databas har kvarhållning från 7 dagar till 14 dagar i mitten av månaden. Den här ökningen resulterar i att den totala lagringen av säkerhets kopior dubbleras till 1 488 GB. SQL Database skulle rapportera 1 GB användning i timmar 1 till 372 (första halvåret i månaden). Den rapporterar användningen som 2 GB för timmar 373 till 744 (andra halvan av månaden). Den här användningen sammanställs till en sista faktura på 1 116 GB/månad.

De faktiska fakturerings scenarierna för säkerhets kopiering är mer komplexa. Eftersom antalet ändringar i databasen beror på arbets belastningen och är variabel över tid, varierar storleken på varje differentiell och logg säkerhets kopia också, vilket gör att lagrings förbrukningen per timme kan variera efter detta. Dessutom innehåller varje differentiell säkerhets kopia alla ändringar som har gjorts i databasen sedan den senaste fullständiga säkerhets kopieringen, vilket innebär att den totala storleken på alla differentiella säkerhets kopieringar gradvis ökar under en vecka och sedan sjunker skarpt när en äldre uppsättning fullständiga, differentiella och loggade säkerhets kopieringar åldras. Om t. ex. en tung Skriv aktivitet, till exempel index återskapning, har körts precis efter att en fullständig säkerhets kopiering har slutförts, kommer ändringarna som gjorts vid indexet att tas med i transaktions loggens säkerhets kopior som tas under återskapandet, i nästa differentiella säkerhets kopiering och i varje differentiell säkerhets kopia som gjorts förrän nästa fullständiga säkerhets kopiering sker. För det senare scenariot i större databaser, skapar en optimering i tjänsten en fullständig säkerhets kopia i stället för en differentiell säkerhets kopia om en differentiell säkerhets kopia skulle vara alltför stor i övrigt. Detta minskar storleken på alla differentiella säkerhets kopior före följande fullständiga säkerhets kopiering.

Du kan övervaka den totala lagrings förbrukningen för säkerhets kopiering för varje säkerhets kopierings typ (fullständig, differentiell transaktions logg) över tid enligt beskrivningen i [övervaka förbrukning](#monitor-consumption).

### <a name="backup-storage-redundancy"></a>Redundans för lagring av säkerhets kopior

Redundans för säkerhets kopierings lagring påverkar säkerhets kopierings kostnaderna på följande sätt:
- LRS-pris = x
- ZRS-pris = 1,25 x
- RA-GRS pris = 2x

Mer information om priser för säkerhets kopierings lagring Azure SQL Database prissättnings [sidan](https://azure.microsoft.com/pricing/details/sql-database/single/) och [pris sidan för Azure SQL-hanterad instans](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

> [!IMPORTANT]
> Konfigurerbar redundans för säkerhets kopieringar är för närvarande bara tillgängligt för SQL-hanterad instans och kan bara anges under processen för att skapa hanterade instanser. När resursen har allokerats kan du inte ändra redundans alternativet för lagring av säkerhets kopior.

### <a name="monitor-costs"></a>Övervaka kostnader

Om du vill förstå kostnader för säkerhets kopierings lagring går du till **Cost Management + fakturering** i Azure Portal, väljer **Cost Management**och väljer sedan **kostnads analys**. Välj önskad prenumeration som **omfång**och filtrera sedan efter den tids period och tjänst som du är intresse rad av.

Lägg till ett filter för **tjänst namn**och välj sedan **SQL-databas** i list rutan. Använd filtret under **kategori för mätning** för att välja fakturerings räknare för din tjänst. För en enskild databas eller en Elastic Database-pool väljer du **Single/elastisk pool pitr backup Storage**. För en hanterad instans väljer du **mi pitr backup Storage**. Under kategorierna för **lagring** och **beräkning** kan intresserar dig också, men de är inte kopplade till reserv lagrings kostnader.

![Kostnads analys för lagring av säkerhets kopior](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > Mätare är bara synliga för räknare som används för närvarande. Om en räknare inte är tillgänglig är det troligt att kategorin inte används för närvarande. Räknare för hanterade instanser är till exempel inte tillgängliga för kunder som inte har distribuerat en hanterad instans. På samma sätt visas inte lagrings räknare för resurser som inte konsumerar lagring. 

## <a name="encrypted-backups"></a>Krypterade säkerhets kopior

Om databasen är krypterad med TDE krypteras säkerhets kopiorna automatiskt i vila, inklusive LTR-säkerhetskopieringar. Alla nya databaser i Azure SQL konfigureras med TDE aktiverat som standard. Mer information om TDE finns i  [Transparent datakryptering med SQL Database & SQL-hanterad instans](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Säkerhets kopierings integritet

Azure SQL Engineering-teamet kommer fort löp ande att testa återställning av automatiserade säkerhets kopior av databasen automatiskt. (Den här testningen är för närvarande inte tillgänglig i SQL-hanterad instans.) Vid tidpunkts återställning erhåller-databaser också integritets kontroller för DBCC CHECKDB.

Eventuella problem som hittas under integritets kontrollen resulterar i en avisering till teknik teamet. Mer information finns [i data integritet i SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

Alla databas säkerhets kopior tas med alternativet kontroll summa för att ge ytterligare säkerhets kopierings integritet.

## <a name="compliance"></a>Efterlevnad

När du migrerar databasen från en DTU-baserad tjänst nivå till en vCore tjänst nivå bevaras PITR kvarhållning för att säkerställa att programmets data återställnings princip inte komprometteras. Om Standardkvarhållning inte uppfyller dina krav kan du ändra PITR bevarande period. Mer information finns i [ändra lagrings perioden för PITR-säkerhetskopiering](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Ändra kvarhållningsperioden för säkerhets kopiering av PITR

Du kan ändra standard lagrings perioden för PITR-säkerhetskopiering med hjälp av Azure Portal, PowerShell eller REST API. I följande exempel visas hur du ändrar PITR-kvarhållning till 28 dagar.

> [!WARNING]
> Om du minskar den aktuella kvarhållningsperioden förlorar du möjligheten att återställa till punkter i tid som är äldre än den nya kvarhållningsperioden. Säkerhets kopieringar som inte längre behövs för att tillhandahålla PITR inom den nya kvarhållningsperioden tas bort. Om du ökar den aktuella kvarhållningsperioden får du inte omedelbart möjlighet att återställa till äldre tidpunkter inom den nya kvarhållningsperioden. Du får den funktionen över tid, eftersom systemet börjar bevara säkerhets kopiorna längre.

> [!NOTE]
> Dessa API: er påverkar endast PITR bevarande period. Om du har konfigurerat LTR för databasen påverkas den inte. Information om hur du ändrar kvarhållning av LTR-perioder finns i [långsiktig kvarhållning](long-term-retention-overview.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Ändra lagrings perioden för PITR-säkerhetskopiering med hjälp av Azure Portal

Om du vill ändra kvarhållning av PITR för aktiva databaser med hjälp av Azure Portal går du till servern eller den hanterade instansen med de databaser vars kvarhållningsperiod du vill ändra. 

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Ändringar av PITR-kvarhållning av säkerhets kopior för SQL Database görs på Server sidan i portalen. Om du vill ändra PITR kvarhållning för databaser på en server går du till bladet Server översikt. Välj **hantera säkerhets kopior** i den vänstra rutan, Välj databaserna i omfånget för ändringen och välj sedan **Konfigurera kvarhållning** överst på skärmen:

![Ändra PITR-kvarhållning, server nivå](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL-hanterad instans](#tab/managed-instance)

Ändringar av PITR för säkerhets kopiering för SQL Managed instance görs på en enskild databas nivå. Om du vill ändra PITR för säkerhets kopiering för en instans databas från Azure Portal går du till bladet individuell databas översikt. Välj sedan **Konfigurera kvarhållning av säkerhets kopior** överst på skärmen:

![Ändra PITR kvarhållning, hanterad instans](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Ändra kvarhållningsperioden för säkerhets kopiering av PITR med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell AzureRM-modulen stöds fortfarande av SQL Database-och SQL-hanterad instans, men all framtida utveckling är för AZ. SQL-modulen. Mer information finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen är i stort sett identiska med de i AzureRm-modulerna.

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Använd följande PowerShell-exempel om du vill ändra kvarhållning av PITR för aktiva Azure SQL-databaser.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

#### <a name="sql-managed-instance"></a>[SQL-hanterad instans](#tab/managed-instance)

Använd följande PowerShell-exempel om du vill ändra kvarhållning av PITR för en **enskild aktiv** SQL-hanterad instans databas.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

Om du vill ändra kvarhållning av PITR för **alla aktiva** SQL-hanterade instans databaser använder du följande PowerShell-exempel.

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

Använd följande PowerShell-exempel om du vill ändra kvarhållning av PITR för en **enskild borttagen** SQL-hanterad instans databas.
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Använd följande PowerShell-exempel om du vill ändra kvarhållning av PITR för **alla borttagna** SQL-hanterade instans databaser.

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Noll (0) dagar kvarhållning anger att säkerhets kopian tas bort omedelbart och inte längre sparas i en borttagen databas.
När PITR för säkerhets kopiering har minskat för en borttagen databas går det inte längre att öka.

---

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Ändra lagrings perioden för PITR-säkerhetskopiering med hjälp av REST API

#### <a name="sample-request"></a>Exempel förfrågan

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Begärandetext

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Exempelsvar

Status kod: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Mer information finns i [kvarhållning av säkerhets kopior REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

#### <a name="sample-request"></a>Exempel förfrågan

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Begärandetext

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Exempelsvar

Status kod: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Mer information finns i [kvarhållning av säkerhets kopior REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="configure-backup-storage-redundancy"></a>Konfigurera redundans för lagring av säkerhets kopior

> [!NOTE]
> Konfigurerbar redundans för säkerhets kopieringar är för närvarande bara tillgängligt för SQL-hanterad instans och kan bara anges under processen för att skapa hanterade instanser. När resursen har allokerats kan du inte ändra redundans alternativet för lagring av säkerhets kopior.

Det går bara att ange en redundans för säkerhets kopiering av en hanterad instans när en instans skapas. Standardvärdet är Geo-redundant lagring (RA-GRS). Om du vill ha skillnader i prissättningen mellan lokalt redundant (LRS), zon-redundant (ZRS) och Geo-redundant (RA-GRS) säkerhets kopierings lagring besöker du [pris sidan för hanterade instanser](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>Konfigurera redundans för lagring av säkerhets kopior med hjälp av Azure Portal

I Azure Portal finns alternativet för att ändra redundans för lagring av säkerhets kopior i bladet **Compute + Storage** tillgängligt från alternativet **Konfigurera hanterad instans** på fliken **grundläggande** när du skapar en SQL-hanterad instans.
![Öppna beräkning + lagrings konfiguration – bladet](./media/automated-backups-overview/open-configuration-blade-mi.png)

Hitta alternativet för att välja redundans för säkerhets kopiering på bladet **Compute + Storage** .
![Konfigurera redundans för lagring av säkerhets kopior](./media/automated-backups-overview/select-backup-storage-redundancy-mi.png)

## <a name="next-steps"></a>Nästa steg

- Databas säkerhets kopieringar är en viktig del av en strategi för affärs kontinuitet och haveri beredskap eftersom de skyddar dina data från oavsiktlig skada eller borttagning. Mer information om andra SQL Database affärs kontinuitets lösningar finns i [Översikt över affärs kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- Få mer information om hur du [återställer en databas till en tidpunkt med hjälp av Azure Portal](recovery-using-backups.md).
- Få mer information om hur du [återställer en databas till en tidpunkt med hjälp av PowerShell](scripts/restore-database-powershell.md).
- Information om hur du konfigurerar, hanterar och återställer från långsiktig kvarhållning av automatiserade säkerhets kopieringar i Azure Blob Storage med hjälp av Azure Portal finns i [Hantera långsiktig kvarhållning av säkerhets kopior med hjälp av Azure Portal](long-term-backup-retention-configure.md).
- Information om hur du konfigurerar, hanterar och återställer från långsiktig kvarhållning av automatiserade säkerhets kopieringar i Azure Blob Storage med hjälp av PowerShell finns i [Hantera långsiktig kvarhållning av säkerhets kopior med hjälp av PowerShell](long-term-backup-retention-configure.md).
- Information om hur du finjusterar lagring av säkerhets kopior och kostnader för Azure SQL-hanterad instans finns i [finjustera lagrings kostnader för säkerhets kopiering på den hanterade instansen](https://aka.ms/mi-backup-tuning).
