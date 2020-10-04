---
title: Beräknings-och lagrings alternativ – Azure Database for MySQL-flexibel Server
description: I den här artikeln beskrivs beräknings-och lagrings alternativen i Azure Database for MySQL-flexibel Server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 8a1b30803494facf6eaabcc3695770d694b4e221
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708687"
---
# <a name="compute-and-storage-options-in-azure-database-for-mysql---flexible-server-preview"></a>Beräknings-och lagrings alternativ i Azure Database for MySQL-flexibel Server (för hands version)

> [!IMPORTANT] 
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Du kan skapa en Azure Database for MySQL flexibel server på någon av tre olika beräknings nivåer: Burst, Generell användning och Minnesoptimerade. Beräknings nivåerna särskiljs av den underliggande VM-SKU: n-serien, D-serien och E-serien. Valet av beräknings nivå och storlek avgör vilken minnes-och virtuella kärnor som är tillgänglig på servern. Samma lagrings teknik används för alla beräknings nivåer. Alla resurser har allokerats på MySQL-server nivå. En server kan ha en eller flera databaser.

| Resurs/nivå | **Burstable** | **Generell användning** | **Minnesoptimerade** |
|:---|:----------|:--------------------|:---------------------|
| VM-serie| B-serien | Ddsv4-serien | Edsv4-serien|
| Virtuella kärnor | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Minne per vCore | Variabel | 4 GiB | 8 GiB * |
| Lagrings storlek | 5 GiB till 16 TiB | 5 GiB till 16 TiB | 5 GiB till 16 TiB |
| Kvarhållningsperiod för databas säkerhets kopior | 1 till 35 dagar | 1 till 35 dagar | 1 till 35 dagar |

\* Med undantag för E64ds_v4 (Minnesoptimerade) SKU, som har 504 GB minne

Om du vill välja en beräknings nivå använder du följande tabell som utgångs punkt.

| Beräknings nivå | Målbelastningar |
|:-------------|:-----------------|
| Burstable | Bäst för arbets belastningar som inte behöver full CPU kontinuerligt. |
| Generell användning | De flesta företags arbets belastningar som kräver balanserade beräkning och minne med skalbart I/O-genomflöde. Några exempel kan vara servrar som är värd för webb- och mobilappar och andra företagsprogram.|
| Minnesoptimerad | Databas arbets belastningar med höga prestanda som kräver minnes prestanda för snabbare bearbetning av transaktioner och högre samtidighet. Exempel på det är servrar för att bearbeta realtidsdata och transaktionsappar eller analysappar med höga prestanda.|

När du har skapat en server ändrades beräknings nivån, beräknings storlek och lagrings storlek. Compute Scaling kräver en omstart och tar mellan 60-120 sekunder, medan lagrings skalning inte kräver omstart. Du kan också självständigt justera säkerhets kopierings perioden upp eller ned. Mer information finns i avsnittet [Scale Resources](#scale-resources) .

## <a name="compute-tiers-size-and-server-types"></a>Beräknings nivåer, storlek och Server typer

Beräknings resurser kan väljas baserat på nivån och storleken. Detta avgör virtuella kärnor och minnes storleken. Virtuella kärnor representerar den underliggande maskin varans logiska processor.

De detaljerade specifikationerna för de tillgängliga Server typerna är följande:

| Beräknings storlek         | Virtuella kärnor | Minnes storlek (GiB) | 
|----------------------|--------|-------------------|
| **Burstable**        |        |                   | 
| B1S                  | 1      | 1                 |  
| B1ms                 | 1      | 2                 | 
| B2s                  | 2      | 4                 |  
| **Generell användning**  |        |                   | 
| D2ds_v4              | 2      | 8                 |  
| D4ds_v4              | 4      | 16                | 
| D8ds_v4              | 8      | 32                | 
| D16ds_v4             | 16     | 64                | 
| D32ds_v4             | 32     | 128               |  
| D48ds_v4             | 48     | 192               |  
| D64ds_v4             | 64     | 256               | 
| **Minnesoptimerade** |        |                   |
| E2ds_v4              | 2      | 16                |
| E4ds_v4              | 4      | 32                |
| E8ds_v4              | 8      | 64                |
| E16ds_v4             | 16     | 128               |
| E32ds_v4             | 32     | 256               |
| E48ds_v4             | 48     | 384               |
| E64ds_v4             | 64     | 504               |

För att få mer information om beräknings serien tillgänglig, se Azure VM-dokumentation för [Burstable (B-serien)](../../virtual-machines/sizes-b-series-burstable.md), [generell användning (Ddsv4-serien)](../../virtual-machines/ddv4-ddsv4-series.md)och [minnesoptimerade (Edsv4-serien)](../../virtual-machines/edv4-edsv4-series.md).

## <a name="storage"></a>Storage

Lagrings utrymmet du tillhandahåller är mängden lagrings kapacitet som är tillgänglig för din flexibla Server. Lagring används för databasfilerna, temporära filer, transaktions loggar och MySQL-server loggarna. I alla beräknings nivåer är den minsta lagring som stöds 5 GiB och max 16 TiB. Lagring skalas i steg om 1 GiB-steg och kan skalas upp när servern har skapats.

>[!NOTE]
> Lagringen kan bara skalas upp, inte nedåt.

Du kan övervaka lagrings användningen i Azure Portal (med Azure Monitor) med hjälp av måtten lagrings gräns, lagrings utrymme och lagring som används. Läs artikeln om [övervakning](./concepts-monitoring.md) för att lära dig mer om Mät värden. 

### <a name="reaching-the-storage-limit"></a>Nått lagrings gränsen

När det förbrukade lagrings utrymmet på servern är nära att nå den etablerade gränsen, sätts servern i skrivskyddat läge för att skydda eventuella förlorade skrivningar på servern. Servrar med mindre än lika med 100 GiB-allokerat lagrings utrymme markeras som skrivskyddade om det lediga lagrings utrymmet är mindre än 5% av den allokerade lagrings storleken. Servrar med fler än 100 GiB etablerade lagrings enheter markeras som skrivskyddade när det lediga lagrings utrymmet är mindre än 5 GiB.

Om du till exempel har etablerad 110 GiB lagring och den faktiska användningen går över 105 GiB, är servern markerad som skrivskyddad. Alternativt, om du har etablerad 5 GiB lagring, markeras servern som skrivskyddad när den kostnads fria lagringen når mindre än 256 MB.

När tjänsten försöker göra så att servern blir skrivskyddad blockeras alla nya skrivtransaktionsbegäranden och befintliga aktiva transaktioner fortsätter att köras. När servern är i skrivskyddat läge misslyckas alla efterföljande skrivåtgärder och transaktioner. Läsfrågor fortsätter att fungera utan avbrott. 

Om du vill få ut servern ur skrivskyddat läge bör du öka den allokerade lagrings utrymmet på servern. Detta kan göras med hjälp av Azure Portal eller Azure CLI. När den har ökat är servern redo att godkänna Skriv transaktioner igen.

Vi rekommenderar att du ställer in en avisering som meddelar dig när Server lagringen närmar sig tröskelvärdet, så att du kan undvika att komma in i skrivskyddat läge. Mer information om tillgängliga mått finns i [övervaknings artikeln](./concepts-monitoring.md) . 

Vi rekommenderar att du <!--turn on storage auto-grow or to--> Konfigurera en avisering som meddelar dig när Server lagringen närmar sig tröskelvärdet, så att du kan undvika att komma in i skrivskyddat läge. Mer information finns i dokumentationen om aviserings dokumentation [så här konfigurerar du en avisering](how-to-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Utöka lagring automatiskt

Automatisk storleks ökning för lagring är inte tillgängligt ännu för Azure Database for MySQL flexibel Server.

## <a name="iops"></a>IOPS
Lägsta effektiva IOPS är 100 över alla beräknings storlekar och högsta effektiva IOPS bestäms av båda följande attribut: 
- Compute: den högsta effektiva IOPS som kan begränsas av den maximala tillgängliga IOPS för den valda beräknings storleken.
- Lagring: i alla beräknings nivåer kan IOPS skalas med den tillhandahållna lagrings storleken i ett 3:1-förhållande.

Du kan skala effektiva IOPS som är tillgängliga genom att öka den etablerade lagringen eller flytta till en större beräknings storlek (om din IOPS begränsas av beräkning). I för hands versionen är den högsta effektiva IOPS som stöds 20 000 IOPS.

Om du vill veta mer om högsta antalet effektiva IOPS per beräknings storlek, kan du använda kombinationen av både beräkning och lagring, nedan: 

| Beräknings storlek         | Högsta effektiva IOPS  | 
|----------------------|---------------------|
| **Burstable**        |                     |
| B1S                  | 320                 |
| B1ms                 | 640                 |
| B2s                  | 1280                | 
| **Generell användning**  |                     |
| D2ds_v4              | 3200                |
| D4ds_v4              | 6400                |
| D8ds_v4              | 12800               |
| D16ds_v4             | 20000               |
| D32ds_v4             | 20000               |
| D48ds_v4             | 20000               | 
| D64ds_v4             | 20000               | 
| **Minnesoptimerade** |                     | 
| E2ds_v4              | 3200                | 
| E4ds_v4              | 6400                | 
| E8ds_v4              | 12800               | 
| E16ds_v4             | 20000               | 
| E32ds_v4             | 20000               | 
| E48ds_v4             | 20000               | 
| E64ds_v4             | 20000               |  

Högsta effektiva IOPS är beroende av Max antalet tillgängliga IOPS per beräknings storlek. Se formeln nedan och referera till *maximalt antal cachelagrade diskar i kolumnen: IOPS/Mbps* i dokumentationen för [B-serien](../../virtual-machines/sizes-b-series-burstable.md), [Ddsv4-serien](../../virtual-machines/ddv4-ddsv4-series.md)och [Edsv4-serien](../../virtual-machines/edv4-edsv4-series.md) .

**Högsta effektiva IOPS** = minimum (*"Max ej cachelagrad disk data flöde: IOPS/Mbit/s"* av beräknings storlek, lagring etablerad i GIB * 3)

Du kan övervaka i/O-förbrukningen i Azure Portal (med Azure Monitor) med [IO-procentens](./concepts-monitoring.md) mått. Om du behöver mer IOPS måste du förstå om du begränsas av beräknings storleken eller det tillhandahållna lagrings utrymmet. Skala serverns beräkning eller lagrings utrymme enligt detta.

## <a name="backup"></a>Backup

Tjänsten tar automatiskt säkerhets kopior av servern. Du kan välja en kvarhållningsperiod från mellan 1 och 35 dagar. Lär dig mer om säkerhets kopieringar i [artikeln säkerhets kopierings-och återställnings begrepp](concepts-backup-restore.md).

## <a name="scale-resources"></a>Skala resurser

När du har skapat servern kan du oberoende ändra beräknings nivån, beräknings storlek (virtuella kärnor och minne) och mängden lagrings utrymme samt kvarhållning av säkerhets kopior. Beräknings storleken kan skalas upp eller ned. Kvarhållningsperioden för säkerhets kopior kan skalas upp eller ned från 1 till 35 dagar. Det går bara att öka lagrings storleken. Skalning av resurserna kan göras via portalen eller Azure CLI.

> [!NOTE]
> Det går bara att öka lagrings storleken. Du kan inte gå tillbaka till en mindre lagrings storlek efter ökningen.

När du ändrar beräknings nivån eller beräknings storleken startas servern om för att den nya server typen ska börja gälla. Under tiden då systemet växlar över till den nya servern kan inga nya anslutningar upprättas, och transaktioner som inte allokerats återställs. Det här fönstret varierar, men i de flesta fall är det mellan 60-120 sekunder. 

Skalning av lagring och ändring av kvarhållning av säkerhets kopior är online-åtgärder och kräver inte omstart av servern.

## <a name="pricing"></a>Prissättning

Den senaste pris informationen finns på [sidan med pris](https://azure.microsoft.com/pricing/details/MySQL/)information för tjänsten. Om du vill se kostnaden för den konfiguration du vill ha, visar [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer/flexibleServers) månads kostnaden på fliken **beräkning + lagring** baserat på de alternativ du väljer. Om du inte har någon Azure-prenumeration kan du använda pris Kalkylatorn för Azure för att få ett uppskattat pris. På webbplatsen för [Azures pris kalkylator](https://azure.microsoft.com/pricing/calculator/) väljer **du Lägg till objekt**, expanderar kategorin **databaser** , väljer **Azure Database for MySQL**och **flexibel Server** som distributions typ för att anpassa alternativen.

Om du vill optimera Server kostnaden kan du överväga följande tips:

- Skala ned beräknings nivån eller beräknings storlek (virtuella kärnor) om Compute är underutnyttjad.
- Överväg att växla till den burst-beräknings nivån om din arbets belastning inte behöver fullständig beräknings kapacitet från Generell användning-och Minnesoptimerade nivåer.
- Stoppa servern när den inte används.
- Minska kvarhållning av säkerhets kopior om en längre kvarhållning av säkerhets kopia inte krävs.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en MySQL-server i portalen](quickstart-create-server-portal.md).
- Läs mer om [tjänst begränsningar](concepts-limitations.md).