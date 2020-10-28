---
title: Omvandling av mottagare i data flöde för mappning
description: Lär dig hur du konfigurerar en Sink-omvandling i mappnings data flödet.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2020
ms.openlocfilehash: 5845ab6419d6914b9221df1ae1280d31aba0ae7a
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92737525"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Omvandling av mottagare i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

När du har slutfört transformeringen av dina data skriver du den i ett mål lager med hjälp av Sink-omvandlingen. Varje data flöde kräver minst en Sink-omvandling, men du kan skriva till så många handfat som behövs för att slutföra ditt omvandlings flöde. Om du vill skriva till ytterligare mottagare skapar du nya strömmar via nya grenar och villkorliga delningar.

Varje Sink-omvandling är associerad med exakt ett Azure Data Factory DataSet-objekt eller en länkad tjänst. Omvandlingen av mottagare bestämmer formen och platsen för de data som du vill skriva till.

## <a name="inline-datasets"></a>Infogade data uppsättningar

När du skapar en Sink-omvandling väljer du om mottagar informationen definieras i ett DataSet-objekt eller i omvandling av mottagare. De flesta format är bara tillgängliga i en eller ett annat. Information om hur du använder en speciell koppling finns i lämpligt kopplings dokument.

När ett format stöds för både infogade och i ett data uppsättnings objekt, finns det fördelar med båda. Data uppsättnings objekt är återanvändbara entiteter som kan användas i andra data flöden och aktiviteter som kopiering. Dessa återanvändbara entiteter är särskilt användbara när du använder ett strikt schema. Data uppsättningar är inte baserade i Spark. Ibland kan du behöva åsidosätta vissa inställningar eller schema projektion i omvandlingen av mottagare.

Infogade data uppsättningar rekommenderas när du använder flexibla scheman, en eller flera Sink-instanser eller parametriserade mottagare. Om din mottagare är mycket parametriserad, kan du inte skapa ett "dummy"-objekt med infogade data uppsättningar. Infogade data uppsättningar baseras i Spark och deras egenskaper är inbyggda i data flödet.

Om du vill använda en infogad data uppsättning väljer du det format som du vill använda i väljar **typ för mottagare** . I stället för att välja en data uppsättning för mottagare väljer du den länkade tjänst som du vill ansluta till.

![Skärm bild som visar infogad markering.](media/data-flow/inline-selector.png "Skärm bild som visar infogad markering.")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> Mottagar typer som stöds

Genom att mappa data flödet följer du en metod för att extrahera, läsa in och transformera (ELT) och fungerar med *mellanlagring* av data uppsättningar som är alla i Azure. För närvarande kan följande data uppsättningar användas i en käll omvandling.

| Anslutningsprogram | Format | Data uppsättning/intern |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Avgränsad text](format-delimited-text.md#mapping-data-flow-properties) <br> [Delta (för hands version)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Avgränsad text](format-delimited-text.md#mapping-data-flow-properties) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Avgränsad text](format-delimited-text.md#mapping-data-flow-properties) <br> [Delta (för hands version)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties)  <br> [Common data Model (för hands version)](format-common-data-model.md#sink-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- <br> -/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL-hanterad instans (för hands version)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Inställningar som är aktuella för dessa anslutningar finns på fliken **Inställningar** . Skript exempel för information och data flöde i dessa inställningar finns i anslutnings dokumentationen.

Azure Data Factory har till gång till fler än [90 inbyggda anslutningar](connector-overview.md). Om du vill skriva data till de andra källorna från ditt data flöde använder du kopierings aktiviteten för att läsa in data från en mottagare som stöds.

## <a name="sink-settings"></a>Mottagar inställningar

När du har lagt till en mottagare konfigurerar du via fliken **mottagare** . Här kan du välja eller skapa den data uppsättning som din Sink skriver till. Utvecklings värden för data uppsättnings parametrar kan konfigureras i [fel söknings inställningar](concepts-data-flow-debug-mode.md). (Fel söknings läge måste vara aktiverat.)

I följande videoklipp förklaras ett antal olika Sink-alternativ för text avgränsade filtyper.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![Skärm bild som visar inställningar för mottagare.](media/data-flow/sink-settings.png "Skärm bild som visar inställningar för mottagare.")

**Schema avvikelse** : [schema avvikelse](concepts-data-flow-schema-drift.md) är möjligheten att Data Factory att internt hantera flexibla scheman i dina data flöden utan att uttryckligen definiera kolumn ändringar. Aktivera **Tillåt schema avvikelse** om du vill skriva ytterligare kolumner ovanpå vad som definieras i Datasink-dataschemat.

**Verifiera schema** : om alternativet Validera schema är markerat Miss varnas data flödet om en kolumn i det inkommande käll schemat inte hittas i käll projektionen, eller om data typerna inte matchar. Använd den här inställningen för att genomdriva att källdata uppfyller kontraktet för din definierade projektion. Det är användbart i scenarier med databas källor för att signalera att kolumn namn eller typer har ändrats.

## <a name="field-mapping"></a>Fältmappning

På samma sätt som i en SELECT-omvandling, på fliken **mappning** för mottagaren, kan du bestämma vilka inkommande kolumner som ska skrivas. Som standard mappas alla inmatade kolumner, inklusive ingångna kolumner. Det här beteendet kallas *Automappning* .

När du inaktiverar automappningen kan du lägga till antingen fasta kolumnbaserade mappningar eller regelbaserade mappningar. Med hjälp av regelbaserade mappningar kan du skriva uttryck med mönster matchning. Fast mappning mappar logiska och fysiska kolumn namn. Mer information om regelbaserade mappning finns [i kolumn mönster i mappa data flöde](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Beställning av anpassad mottagare

Som standard skrivs data till flera handfat i en icke deterministisk ordning. Körnings motorn skriver data parallellt eftersom omvandlings logiken är slutförd och mottagar ordningen kan variera varje gång. Om du vill ange en exakt mottagar ordning aktiverar du **anpassade Sink-sortering** på fliken **Allmänt** i data flödet. När den är aktive rad skrivs Sinks sekventiellt i stigande ordning.

![Skärm bild som visar beställning av anpassade mottagare.](media/data-flow/custom-sink-ordering.png "Skärm bild som visar beställning av anpassade mottagare.")

## <a name="data-preview-in-sink"></a>Förhands granskning av data i mottagare

När du hämtar en data förhands granskning i ett fel söknings kluster kommer inga data att skrivas till din mottagare. En ögonblicks bild av hur data ser ut kommer att returneras, men inget skrivs till ditt mål. Om du vill testa att skriva data i din mottagare kan du köra en pipeline-felsökning från pipeline-arbetsytan.

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat ditt data flöde lägger du till en [data flödes aktivitet i din pipeline](concepts-data-flow-overview.md).
