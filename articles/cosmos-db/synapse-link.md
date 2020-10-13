---
title: Azure Synapse-länk för Azure Cosmos DB, förmåner och när du ska använda den
description: Lär dig mer om Azure Synapse-länken för Azure Cosmos DB. Med Synapse-länken kan du köra nära real tids analys (HTAP) med Azure Synapse Analytics över drift data i Azure Cosmos DB.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2020
ms.reviewer: sngun
ms.openlocfilehash: 0e02f0bf1bb9646a644a40279a8bae930de34c0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91568003"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db-preview"></a>Vad är Azure Synapse-länk för Azure Cosmos DB (för hands version)?

> [!IMPORTANT]
> Azure Synapse-länken för Azure Cosmos DB är för närvarande en för hands version. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

Azure Synapse-länken för Azure Cosmos DB är en molnbaserad hybrid transaktions-och analys bearbetnings funktion (HTAP) som gör att du kan köra nära real tids analys över drifts data i Azure Cosmos DB. Azure Synapse-länken skapar en tätt sömlös integrering mellan Azure Cosmos DB och Azure Synapse Analytics.

Med hjälp av [Azure Cosmos DB Analytical Store](analytical-store-introduction.md), ett fullständigt isolerat kolumn lager, ger Azure Synapse-länken ingen analys av Extract-Transformation-load (ETL) i [Azure Synapse-analys](../synapse-analytics/overview-what-is.md) mot dina operativa data i stor skala. Affärsanalytiker, data tekniker och data experter kan nu använda Synapse Spark eller Synapse SQL för att köra nära real tids Business Intelligence, analyser och maskin inlärnings pipeliner. Du kan åstadkomma detta utan att påverka prestanda för dina transaktionsarbetsbelastningar på Azure Cosmos DB. 

Följande bild visar Azure Synapse Link integration med Azure Cosmos DB och Azure Synapse Analytics: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Arkitektur diagram för integrering med Azure Synapse Analytics med Azure Cosmos DB" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Funktioner

För att analysera stora drift data uppsättningar och minimera påverkan på prestandan hos verksamhets kritiska transaktions arbets belastningar, kommer de tidigare i Azure Cosmos DB att extraheras och bearbetas av ETL-pipelinen (Extract-Transform-Load). ETL-pipeline kräver många lager av data förflyttning, vilket leder till mycket drifts komplexitet och prestanda påverkan på dina transaktions arbets belastningar. Det ökar också svars tiden för att analysera drift data från tidpunkten för ursprunget.

Jämfört med traditionella ETL-baserade lösningar erbjuder Azure Synapse-länken för Azure Cosmos DB flera fördelar som:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Minskad komplexitet utan ETL-jobb att hantera

Med Azure Synapse-länken kan du direkt komma åt Azure Cosmos DB analys lager med hjälp av Azure Synapse Analytics utan komplex data förflyttning. Alla uppdateringar som görs i användnings data visas i analys lagret i nära real tid utan ETL eller ändra feed-jobb. Du kan köra storskalig analys mot analys lager från Synapse Analytics utan ytterligare datatransformering.

### <a name="near-real-time-insights-into-your-operational-data"></a>Insikter i nära real tid i dina användnings data

Nu kan du få omfattande insikter om dina drift data i nära real tid med hjälp av Azure Synapse-länken. ETL-baserade system tenderar att ha högre latens för att analysera dina drift data, på grund av många lager som behövs för att extrahera, transformera och läsa in drift data. Med inbyggd integrering av Azure Cosmos DB analys lager med Azure Synapse Analytics kan du analysera drift data i nära real tid och aktivera nya affärs scenarier. 

### <a name="no-impact-on-operational-workloads"></a>Ingen påverkan på drift arbets belastningar

Med Azure Synapse-länken kan du köra analytiska frågor mot ett Azure Cosmos DB analys lager (ett separat kolumn lager) medan transaktionerna bearbetas med hjälp av ett allokerat data flöde för transaktions arbets belastningen (ett rad-baserat transaktions lager).  Analytiska arbets belastningar behandlas oberoende av transaktions arbets belastningen utan att något av de data flöde som har allokerats för dina drift data förbrukas.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Optimerad för storskaliga analys arbets belastningar

Azure Cosmos DB Analytical Store är optimerat för att ge skalbarhet, elastiskhet och prestanda för analytiska arbets belastningar utan något beroende på beräknings körnings tiden. Lagrings tekniken är själv hanterad för att optimera dina analys arbets belastningar. Med inbyggt stöd i Azure Synapse Analytics ger åtkomst till det här lagrings lagret enkelhet och höga prestanda.

### <a name="cost-effective"></a>Kostnadseffektiv

Med Azure Synapse-länken kan du få en kostnads optimerad, helt hanterad lösning för drift analys. Den eliminerar extra lager av lagring och data bearbetning som krävs i traditionella ETL-pipelines för att analysera drift data. 

Azure Cosmos DB Analytical Store följer en förbruknings pris modell som baseras på data lagring och analys av Läs-och skriv åtgärder och frågor som körs. Du behöver inte etablera några data flöden, som du gör i dag för transaktionernas arbets belastningar. Genom att komma åt dina data med mycket elastiska beräknings motorer från Azure Synapse Analytics blir den totala kostnaden för att köra lagring och beräkning mycket effektiv.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Analys för lokalt tillgängliga, globalt distribuerade, flera olika region skrivningar

Du kan köra analytiska frågor effektivt mot den närmaste regionala kopian av data i Azure Cosmos DB. Azure Cosmos DB tillhandahåller den avancerade kapaciteten för att köra de globalt distribuerade analytiska arbets belastningarna tillsammans med transaktions arbets belastningar på ett aktivt och aktivt sätt.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>Aktivera HTAP-scenarier för dina användnings data

Synapse-länken sammanställer Azure Cosmos DB analys lager med Azure Synapse Analytics runtime support. Med den här integreringen kan du skapa inbyggda Cloud HTAP-lösningar (hybrid transaktion/analys bearbetning) som genererar insikter baserade på real tids uppdateringar av dina drift data över stora data uppsättningar. Det låser upp nya affärs scenarier för att generera aviseringar baserat på Live-trender, bygga nära real tids instrument paneler och affärs upplevelser baserat på användarens beteende.

### <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analys lager

Azure Cosmos DB Analytical Store är en kolumn-orienterad representation av dina drift data i Azure Cosmos DB. Det här analys lagret är lämpligt för snabba, kostnads effektiva frågor om stora användnings data uppsättningar, utan att kopiera data och påverka prestandan för dina transaktions arbets belastningar.

Analys lagringen hämtar automatiskt höga frekvens infogningar, uppdateringar och borttagningar i dina transaktions arbets belastningar i nära real tid, som en fullständigt hanterad funktion ("automatisk synkronisering") av Azure Cosmos DB. Ingen ändrings-feed eller ETL krävs. 

Om du har ett globalt distribuerat Azure Cosmos DB konto kommer det att vara tillgängligt i alla regioner för det kontot när du har aktiverat analytiskt lagrings utrymme för en behållare. Mer information om analys lagret finns i artikeln [Översikt över Azure Cosmos DB analys lager](analytical-store-introduction.md) .

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integrering med Azure Synapse Analytics

Med Synapse-länken kan du nu ansluta direkt till dina Azure Cosmos DB behållare från Azure Synapse Analytics och komma åt analys lagret utan separata anslutningar. Azure Synapse Analytics stöder för närvarande Synapse-länk med [Synapse Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) och [Synapse SQL Server utan SQL Server](../synapse-analytics/sql/on-demand-workspace-overview.md).

Du kan fråga data från Azure Cosmos DB analys lager samtidigt, med interop över olika analys tider som stöds av Azure Synapse Analytics. Inga ytterligare data omvandlingar krävs för att analysera användnings data. Du kan fråga och analysera analys data med hjälp av:

* Synapse Apache Spark med fullständigt stöd för Scala, python, SparkSQL och C#. Synapse Spark är central för data teknik och data vetenskaps scenarier

* SQL Server utan språk i T-SQL och stöd för välbekanta BI-verktyg (till exempel Power BI Premium osv.)

> [!NOTE]
> Från Azure Synapse Analytics kan du komma åt både analys-och transaktions lager i din Azure Cosmos DB-behållare. Men om du vill köra storskalig analys eller skanningar på dina användnings data rekommenderar vi att du använder analytisk lagring för att undvika prestanda påverkan på transaktions arbets belastningar.

> [!NOTE]
> Du kan köra analyser med låg latens i en Azure-region genom att ansluta din Azure Cosmos DB-behållare till Synapse runtime i den regionen.

Den här integrationen möjliggör följande HTAP-scenarier för olika användare:

* En BI-tekniker som vill modellera och publicera en Power BI rapport för att komma åt direkt användnings data i Azure Cosmos DB direkt via Synapse SQL.

* En dataanalytiker som vill härleda insikter från användnings data i en Azure Cosmos DB-behållare genom att fråga den med Synapse SQL, läsa data i skala och kombinera dessa resultat med andra data källor.

* En data expert som vill använda Synapse Spark för att hitta en funktion för att förbättra sin modell och träna den modellen utan att göra komplexa data tekniker. De kan också skriva resultatet av modellen efter att du har gått över till Azure Cosmos DB i real tid med data genom Spark-Synapse.

* En data tekniker som vill göra data tillgängliga för konsumenter genom att skapa SQL-eller Spark-tabeller över Azure Cosmos DB behållare utan manuella ETL-processer.

Mer information om stöd för Azure Synapse Analytics runtime för Azure Cosmos DB finns i [Azure Synapse Analytics for Cosmos DB support](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md).

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>När ska jag använda Azure Synapse-länken för Azure Cosmos DB?

Synapse-länk rekommenderas i följande fall:

* Om du är Azure Cosmos DB kund och vill köra analyser, BI och maskin inlärning över dina användnings data. I sådana fall ger Synapse-länken en mer integrerad analys upplevelse utan att påverka det etablerade data flödet i transaktions arkivet. Exempel:

  * Om du kör Analytics eller BI på Azure Cosmos DB operativa data direkt med hjälp av separata anslutningar idag, eller

  * Om du kör ETL-processer för att extrahera drift data till ett separat analys system.
 
I sådana fall ger Synapse-länken en mer integrerad analys upplevelse utan att påverka det etablerade data flödet i transaktions arkivet.

Synapse-länk rekommenderas inte om du söker efter traditionella informations lager krav som hög samtidighet, arbets belastnings hantering och persistence av agg regeringar över flera data källor. Mer information finns i [vanliga scenarier som kan användas med Azure Synapse-länken för Azure Cosmos DB](synapse-link-use-cases.md).

## <a name="limitations"></a>Begränsningar

* Azure Synapse-länken stöds för API: et för Azure Cosmos DB SQL (Core) och Azure Cosmos DB s API för MongoDB.

* För närvarande kan analys lagret bara aktive ras för nya behållare. Om du vill använda analytisk lagring för befintliga behållare migrerar du data från dina befintliga behållare till nya behållare med hjälp av [Azure Cosmos DB Migreringsverktyg](cosmosdb-migrationchoices.md). Du kan aktivera Synapse-länk på nya och befintliga Azure Cosmos DB-konton.

* Att komma åt Azure Cosmos DB analys lager med SQL Server utan Synapse är för närvarande överbelastad för hands version. Om du vill begära åtkomst, e-posta [Azure Cosmos DBS teamet](mailto:cosmosdbsynapselink@microsoft.com).

* I för hands versionen stöds inte säkerhets kopiering och återställning av behållare för Synapse-länk aktiverade databas konton. Om du har arbets belastningar som kräver säkerhets kopierings-och återställnings funktioner rekommenderar vi inte att du aktiverar Synapse-länken för dessa databas konton. 

* Det går inte att komma åt Azure Cosmos DB Analytics Store med SQL-etableringen för Synapse.

## <a name="pricing"></a>Prissättning

I fakturerings modellen för Azure Synapse-länken ingår kostnader som uppstår med hjälp av Azure Cosmos DB analys lager och Synapse Runtime. Mer information finns i artikeln om priser för [Azure Cosmos DB analys lager](analytical-store-introduction.md#analytical-store-pricing) och [Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/) .

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande dokument:

* [Översikt över Azure Cosmos DB analys lager](analytical-store-introduction.md)

* [Kom igång med Azure Synapse Link för Azure Cosmos DB](configure-synapse-link.md)
 
* [Vad stöds i Azure Synapse Analytics-körning](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Vanliga frågor och svar om Azure Synapse Link för Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Användningsfall för Azure Synapse Link för Azure Cosmos DB](synapse-link-use-cases.md)
