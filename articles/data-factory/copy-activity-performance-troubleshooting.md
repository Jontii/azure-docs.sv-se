---
title: Felsöka prestanda för kopierings aktivitet
description: Lär dig mer om hur du felsöker kopierings aktivitets prestanda i Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: d464124c6841cb2e3186d521b93d7ae08f94c9e9
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2020
ms.locfileid: "89440532"
---
# <a name="troubleshoot-copy-activity-performance"></a>Felsöka prestanda för kopierings aktivitet

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du kan felsöka prestanda problem med kopierings aktiviteter i Azure Data Factory. 

När du har kört en kopierings aktivitet kan du samla in körnings resultat-och prestanda statistik i vyn för [övervakning av kopierings aktiviteter](copy-activity-monitoring.md) . Följande är ett exempel.

![Övervaka körnings information för kopierings aktivitet](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Tips vid prestandajustering

När du kör en kopierings aktivitet i Data Factory visas i vissa fall **"prestanda justerings tips"** överst, som du ser i exemplet ovan. Tipsen visar vilken Flask hals som identifieras av ADF för den här kopian, tillsammans med förslag på hur kopierings data flödet ökar. Försök att göra om ändringen och kör sedan kopian igen.

Som en referens ger för närvarande prestanda justerings tips förslag på följande fall:

| Kategori              | Tips vid prestandajustering                                      |
| --------------------- | ------------------------------------------------------------ |
| Data lager som är speciell   | Läsa in data i **Azure Synpase Analytics (tidigare SQL DW)**: föreslå användning av PolyBase-eller Copy-instruktionen om den inte används. |
| &nbsp;                | Kopiera data från/till **Azure SQL Database**: när DTU är under hög användning rekommenderar vi att du uppgraderar till en högre nivå. |
| &nbsp;                | Kopiera data från/till **Azure Cosmos DB**: när ru är under hög användning bör du föreslå att uppgradera till större ru. |
|                       | Kopiera data från **SAP-tabellen**: vid kopiering av stora mängder data kan du föreslå att utnyttja alternativ för SAP-kopplingens partition för att aktivera parallell belastning och öka Max antalet partitioner. |
| &nbsp;                | Mata in data från **Amazon RedShift**: föreslå att använda Unload om det inte används. |
| Data lagrings begränsning | Om ett antal Läs/skriv-åtgärder begränsas av data lagret under kopieringen, föreslår vi kontroll och ökar den tillåtna begär ande frekvensen för data lagret eller minskar den samtidiga arbets belastningen. |
| Integration runtime  | Om du använder en **egen värd integration Runtime (IR)** och kopierings aktiviteten väntar länge i kön tills IR-resursen är tillgänglig, föreslår vi att skala ut/upp IR. |
| &nbsp;                | Om du använder en **Azure integration runtime** som är i en icke-optimal region som resulterar i långsam läsning/skrivning, rekommenderar vi att du konfigurerar för att använda en IR i en annan region. |
| Feltolerans       | Om du konfigurerar fel tolerans och hoppar över inkompatibla rader resulterar i långsamma prestanda, och du bör föreslå att käll-och mottagar data är kompatibla. |
| Mellanlagrad kopia           | Om mellanlagrad kopiering har kon figurer ATS men inte användbart för ditt käll-Sink-par, rekommenderar vi att du tar bort det. |
| Återuppta                | När kopierings aktiviteten återupptas från den senaste felpunkten men du råkar ändra DIU-inställningen efter den ursprungliga körningen, Observera att den nya inställningen för DIU inte träder i bruk. |

## <a name="understand-copy-activity-execution-details"></a>Förstå körnings information för kopierings aktivitet

Körnings informationen och varaktigheterna längst ned i vyn kopierings aktivitets övervakning beskriver de viktiga steg som din kopierings aktivitet går igenom (se exemplet i början av den här artikeln), vilket är särskilt användbart för att felsöka kopierings prestanda. Flask halsen för din kopierings körning är den som har den längsta varaktigheten. Se följande tabell i varje Stadiums definition och lär dig hur du [felsöker kopierings aktivitet på Azure IR](#troubleshoot-copy-activity-on-azure-ir) och [felsöker kopierings aktivitet på egen värd-IR](#troubleshoot-copy-activity-on-self-hosted-ir) med sådan information.

| Fas           | Beskrivning                                                  |
| --------------- | ------------------------------------------------------------ |
| Kö           | Den tid som förflutit tills kopierings aktiviteten faktiskt börjar på integration Runtime. |
| Skript före kopiering | Tiden mellan kopierings aktiviteten som startar på IR-och kopierings aktiviteten som kör skriptet för att kopiera i mottagar data lagret. Använd när du konfigurerar skriptet för att kopiera för databas handfat, t. ex. När du skriver data i Azure SQL Database rensa innan du kopierar nya data. |
| Överföring        | Tiden i slutet av föregående steg och IR-överföring av alla data från källan till Sink. <br/>Observera under stegen under överförings körning parallellt, och vissa åtgärder visas inte nu, t. ex. tolkning/generering av fil format.<br><br/>- **Tid till första byte:** Tiden som förflutit mellan föregående stegs slut och den tid då IR tar emot den första byten från käll data lagret. Gäller icke-filbaserade källor.<br>- **Registrerings Källa:** Hur lång tid det tog att räkna upp källfiler eller datapartitioner. Den senare gäller när du konfigurerar partitionsalternativ för databas källor, t. ex. När du kopierar data från databaser som Oracle/SAP HANA/Teradata/Netezza/etc.<br/>-**Läser från Källa:** Hur lång tid det tar att hämta data från käll data lagret.<br/>- **Skriver till Sink:** Hur lång tid det tog att skriva data till data lagret för mottagare. Observera att vissa anslutningar inte har detta mått för tillfället, inklusive Azure Kognitiv sökning, Azure Datautforskaren, Azure Table Storage, Oracle, SQL Server, Common Data Service, Dynamics 365, Dynamics CRM, Salesforce/Salesforce-tjänstemolnet. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Felsöka kopierings aktivitet på Azure IR

Följ [prestanda justerings stegen](copy-activity-performance.md#performance-tuning-steps) för att planera och genomföra prestandatest för ditt scenario. 

Om kopierings aktivitetens prestanda inte uppfyller din förväntad händelse för fel sökning av en enskild kopierings aktivitet som körs på Azure Integration Runtime, ska du använda förslaget och försöka igen om du ser [tips för prestanda justering](#performance-tuning-tips) som visas i vyn kopiera övervakning. Annars kan du [förstå körnings informationen för kopierings aktiviteten](#understand-copy-activity-execution-details), kontrol lera vilken fas som har den **längsta** varaktigheten och använda rikt linjerna nedan för att öka kopierings prestandan:

- **"För kopierings skriptet" upptäckte lång varaktighet:** det tar lång tid att köra skriptet före kopiering på mottagar databasen. Förbättra prestanda genom att justera den angivna skript logiken för att kopiera. Om du behöver ytterligare hjälp med att förbättra skriptet kan du kontakta ditt databas team.

- **"Överförings tid till första byte" upplevde lång arbets tid**: din käll fråga tar lång tid att returnera data. Kontrol lera och optimera frågan eller servern. Om du behöver ytterligare hjälp kan du kontakta ditt data lager team.

- **"Överförings registrerings källa" har haft lång arbets tid**: det är långsamt att räkna upp källfiler eller data partitioner för käll databasen.
  - När du kopierar data från filbaserad källa, om du använder **Wildcard-filter** på mappsökväg eller fil namn ( `wildcardFolderPath` eller `wildcardFileName` ), eller om du använder **filter för senaste ändrings tid** ( `modifiedDatetimeStart` eller `modifiedDatetimeEnd` ), så resulterar det i att kopierings aktiviteten visar alla filer under den angivna mappen på klient sidan och tillämpar sedan filtret. Sådan fil uppräkning kan bli Flask hals, särskilt när bara små mängder filer uppfyllde filter regeln.

    - Kontrol lera om du kan [Kopiera filer baserat på sökväg eller namn för datetime-partitionerad fil](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). På så sätt går det inte att ta bördan på käll sidan.

    - Kontrol lera om du kan använda data lagrets interna filter i stället, särskilt "**prefix**" för Amazon S3 och Azure blob. Prefixet filter är ett filter för data lager på Server sidan och har mycket bättre prestanda.

    - Överväg att dela upp en enda stor data uppsättning i flera mindre data mängder, och låt dessa kopierings jobb köras samtidigt, så att varje data mängd förvaras. Du kan göra detta med lookup/GetMetadata + framifrån och efter kopiering. Se [Kopiera filer från flera behållare](solution-template-copy-files-multiple-containers.md) eller [migrera data från Amazon S3 till ADLS Gen2](solution-template-migration-s3-azure.md) Solution-mallar som ett allmänt exempel.

  - Kontrol lera om ADF rapporterar eventuella begränsnings fel på källan eller om data lagret har hög användnings status. I så fall kan du antingen minska arbets belastningarna på data lagret eller försöka kontakta data lager administratören för att öka begränsnings gränsen eller tillgänglig resurs.

  - Använd Azure IR i samma eller nära käll data lagrets region.

- **"Överförings läsning från källa" har länge arbets tid**: 

  - Använd bästa praxis för anslutnings data inläsning om detta gäller. Om du till exempel kopierar data från [Amazon-RedShift](connector-amazon-redshift.md)konfigurerar du att använda RedShift-inläsning.

  - Kontrol lera om ADF rapporterar eventuella begränsnings fel på källan eller om data lagret har hög belastning. I så fall kan du antingen minska arbets belastningarna på data lagret eller försöka kontakta data lager administratören för att öka begränsnings gränsen eller tillgänglig resurs.

  - Kontrol lera din kopia av käll-och mottagar mönster: 

    - Om ditt kopierings mönster stöder större än 4 data integrerings enheter (DIUs) – Läs mer i [det här avsnittet](copy-activity-performance-features.md#data-integration-units) om information, vanligt vis kan du prova att öka DIUs för att få bättre prestanda. 

    - I annat fall bör du överväga att dela upp stora data mängder i flera mindre data uppsättningar och låta dessa kopierings jobb köras samtidigt, så att alla kan hantera en del av data. Du kan göra detta med lookup/GetMetadata + framifrån och efter kopiering. Läs mer om att [Kopiera filer från flera behållare](solution-template-copy-files-multiple-containers.md), [migrera data från Amazon S3 till ADLS Gen2](solution-template-migration-s3-azure.md)eller [Mass kopiering med en mall för kontroll tabells](solution-template-bulk-copy-with-control-table.md) lösningar som ett allmänt exempel.

  - Använd Azure IR i samma eller nära käll data lagrets region.

- **"Överförings skrivning till mottagare" som har haft lång arbets tid**:

  - Använd bästa praxis för anslutnings data inläsning om detta gäller. Exempel: när du kopierar data till [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (tidigare SQL DW) använder du PolyBase eller Copy-instruktionen. 

  - Kontrol lera om ADF rapporterar eventuella begränsnings fel vid Sink eller om data lagret har hög belastning. I så fall kan du antingen minska arbets belastningarna på data lagret eller försöka kontakta data lager administratören för att öka begränsnings gränsen eller tillgänglig resurs.

  - Kontrol lera din kopia av käll-och mottagar mönster: 

    - Om ditt kopierings mönster stöder större än 4 data integrerings enheter (DIUs) – Läs mer i [det här avsnittet](copy-activity-performance-features.md#data-integration-units) om information, vanligt vis kan du prova att öka DIUs för att få bättre prestanda. 

    - Annars kan du gradvis justera de [parallella kopiorna](copy-activity-performance-features.md), Observera att för många parallella kopior kan till och med försämra prestandan.

  - Använd Azure IR i samma eller nära mottagarens data lager region.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>Felsöka kopierings aktivitet på IR med egen värd

Följ [prestanda justerings stegen](copy-activity-performance.md#performance-tuning-steps) för att planera och genomföra prestandatest för ditt scenario. 

Om kopierings prestandan inte motsvarar förväntad fel sökning av en enskild kopierings aktivitet som körs på Azure Integration Runtime, ska du använda förslaget och försöka igen om du ser [tips för prestanda justering](#performance-tuning-tips) i vyn kopiera övervakning. Annars kan du [förstå körnings informationen för kopierings aktiviteten](#understand-copy-activity-execution-details), kontrol lera vilken fas som har den **längsta** varaktigheten och använda rikt linjerna nedan för att öka kopierings prestandan:

- **"Kö" förväntar lång varaktighet:** kopierings aktiviteten väntar länge i kön tills din egen värd-IR-resurs har körts. Kontrol lera IR-kapaciteten och användningen och [skala upp eller ut](create-self-hosted-integration-runtime.md#high-availability-and-scalability) enligt din arbets belastning.

- **"Överförings tid till första byte" upplevde lång arbets tid**: din käll fråga tar lång tid att returnera data. Kontrol lera och optimera frågan eller servern. Om du behöver ytterligare hjälp kan du kontakta ditt data lager team.

- **"Överförings registrerings källa" har haft lång arbets tid**: det är långsamt att räkna upp källfiler eller data partitioner för käll databasen.

  - Kontrol lera om den egna IR-datorn har låg latens för att ansluta till käll data lagret. Om din källa är i Azure kan du använda [det här verktyget](http://www.azurespeed.com/Azure/Latency) för att kontrol lera svars tiden från den egna IR-datorn till Azure-regionen, desto mindre desto bättre.

  - När du kopierar data från filbaserad källa, om du använder **Wildcard-filter** på mappsökväg eller fil namn ( `wildcardFolderPath` eller `wildcardFileName` ), eller om du använder **filter för senaste ändrings tid** ( `modifiedDatetimeStart` eller `modifiedDatetimeEnd` ), så resulterar det i att kopierings aktiviteten visar alla filer under den angivna mappen på klient sidan och tillämpar sedan filtret. Sådan fil uppräkning kan bli Flask hals, särskilt när bara små mängder filer uppfyllde filter regeln.

    - Kontrol lera om du kan [Kopiera filer baserat på sökväg eller namn för datetime-partitionerad fil](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). På så sätt går det inte att ta bördan på käll sidan.

    - Kontrol lera om du kan använda data lagrets interna filter i stället, särskilt "**prefix**" för Amazon S3 och Azure blob. Prefixet filter är ett filter för data lager på Server sidan och har mycket bättre prestanda.

    - Överväg att dela upp en enda stor data uppsättning i flera mindre data mängder, och låt dessa kopierings jobb köras samtidigt, så att varje data mängd förvaras. Du kan göra detta med lookup/GetMetadata + framifrån och efter kopiering. Se [Kopiera filer från flera behållare](solution-template-copy-files-multiple-containers.md) eller [migrera data från Amazon S3 till ADLS Gen2](solution-template-migration-s3-azure.md) Solution-mallar som ett allmänt exempel.

  - Kontrol lera om ADF rapporterar eventuella begränsnings fel på källan eller om data lagret har hög användnings status. I så fall kan du antingen minska arbets belastningarna på data lagret eller försöka kontakta data lager administratören för att öka begränsnings gränsen eller tillgänglig resurs.

- **"Överförings läsning från källa" har länge arbets tid**: 

  - Kontrol lera om den egna IR-datorn har låg latens för att ansluta till käll data lagret. Om din källa är i Azure kan du använda [det här verktyget](http://www.azurespeed.com/Azure/Latency) för att kontrol lera svars tiden från den egna IR-datorn i Azure-regionerna, desto mindre desto bättre.

  - Kontrol lera att den egna IR-datorn har tillräckligt med inkommande bandbredd för att kunna läsa och överföra data på ett effektivt sätt. Om käll data lagret finns i Azure kan du använda [det här verktyget](https://www.azurespeed.com/Azure/Download) för att kontrol lera nedladdnings hastigheten.

  - Kontrol lera den självbetjänings-och minnes användnings trenden i Azure Portal > din Data Factory-> översikts sida. Överväg att [skala upp/ut IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability) om processor användningen är hög eller tillgängligt minne är lågt.

  - Använd bästa praxis för anslutnings data inläsning om detta gäller. Exempel:

    - När du kopierar data från [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)och [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)) aktiverar du alternativ för datapartitioner för att kopiera data parallellt.

    - När du kopierar data från [HDFS](connector-hdfs.md)konfigurerar du att använda DistCp.

    - När du kopierar data från [Amazon RedShift](connector-amazon-redshift.md)konfigurerar du för att använda RedShift-inläsning.

  - Kontrol lera om ADF rapporterar eventuella begränsnings fel på källan eller om data lagret har hög belastning. I så fall kan du antingen minska arbets belastningarna på data lagret eller försöka kontakta data lager administratören för att öka begränsnings gränsen eller tillgänglig resurs.

  - Kontrol lera din kopia av käll-och mottagar mönster: 

    - Om du kopierar data från partition-alternativ-aktiverade data lager, bör du överväga att gradvis justera de [parallella kopiorna](copy-activity-performance-features.md), Observera att för många parallella kopior kan till och med försämra prestandan.

    - I annat fall bör du överväga att dela upp stora data mängder i flera mindre data uppsättningar och låta dessa kopierings jobb köras samtidigt, så att alla kan hantera en del av data. Du kan göra detta med lookup/GetMetadata + framifrån och efter kopiering. Läs mer om att [Kopiera filer från flera behållare](solution-template-copy-files-multiple-containers.md), [migrera data från Amazon S3 till ADLS Gen2](solution-template-migration-s3-azure.md)eller [Mass kopiering med en mall för kontroll tabells](solution-template-bulk-copy-with-control-table.md) lösningar som ett allmänt exempel.

- **"Överförings skrivning till mottagare" som har haft lång arbets tid**:

  - Använd bästa praxis för anslutnings data inläsning om detta gäller. Exempel: när du kopierar data till [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (tidigare SQL DW) använder du PolyBase eller Copy-instruktionen. 

  - Kontrol lera om den egna IR-datorn har låg latens för att ansluta till data lagret för mottagare. Om din Sink är i Azure kan du använda [det här verktyget](http://www.azurespeed.com/Azure/Latency) för att kontrol lera svars tiden från den egna IR-datorn till Azure-regionen, desto mindre desto bättre.

  - Kontrol lera om den egna IR-datorn har tillräckligt med utgående bandbredd för överföring och skrivning av data på ett effektivt sätt. Om ditt data lager för mottagare är i Azure kan du använda [det här verktyget](https://www.azurespeed.com/Azure/UploadLargeFile) för att kontrol lera uppladdnings hastigheten.

  - Kontrol lera om den egen värdbaserade IR-CPU-och minnes användnings trenden i Azure Portal-> din Data Factory-> översikts sida. Överväg att [skala upp/ut IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability) om processor användningen är hög eller tillgängligt minne är lågt.

  - Kontrol lera om ADF rapporterar eventuella begränsnings fel vid Sink eller om data lagret har hög belastning. I så fall kan du antingen minska arbets belastningarna på data lagret eller försöka kontakta data lager administratören för att öka begränsnings gränsen eller tillgänglig resurs.

  - Överväg att gradvis justera de [parallella kopiorna](copy-activity-performance-features.md), Observera att för många parallella kopior kan till och med försämra prestandan.

## <a name="other-references"></a>Andra referenser

Här är prestanda övervakning och justering av referenser för några av de data lager som stöds:

* Azure Blob Storage: [skalbarhets-och prestanda mål för Blob Storage](../storage/blobs/scalability-targets.md) och [Check lista för prestanda och skalbarhet för Blob Storage](../storage/blobs/storage-performance-checklist.md).
* Azure Table Storage: [skalbarhets-och prestanda mål för](../storage/tables/scalability-targets.md) [Check lista för tabell lagring och prestanda och skalbarhet för Table Storage](../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: du kan [övervaka prestanda](../sql-database/sql-database-single-database-monitor.md) och kontrol lera DTU-procenten (Database Transaction Unit).
* Azure Synapse Analytics (tidigare SQL Data Warehouse): dess funktion mäts i informations lager enheter (DWU: er). Se [hantera beräknings kraft i Azure Synapse Analytics (översikt)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [prestanda nivåer i Azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server: [övervaka och justera för prestanda](https://msdn.microsoft.com/library/ms189081.aspx).
* Lokal fil Server: [prestanda justering för fil servrar](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Nästa steg
Se andra artiklar om kopierings aktiviteter:

- [Översikt över kopieringsaktivitet](copy-activity-overview.md)
- [Guiden Kopiera aktivitets prestanda och skalbarhet](copy-activity-performance.md)
- [Kopiera aktivitets prestanda optimerings funktioner](copy-activity-performance-features.md)
- [Använd Azure Data Factory för att migrera data från data Lake eller data lager till Azure](data-migration-guidance-overview.md)
- [Migrera data från Amazon S3 till Azure Storage](data-migration-guidance-s3-azure-storage.md)
