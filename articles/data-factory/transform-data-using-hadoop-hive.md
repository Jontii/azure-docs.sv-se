---
title: Transformera data med Hadoop Hive-aktivitet
description: Lär dig hur du kan använda Hive-aktiviteten i en Azure Data Factory för att köra Hive-frågor på ett eget HDInsight-kluster på begäran.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 05/08/2019
ms.openlocfilehash: 877c1719a76f23f8446164b641dc2dac84261e0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "83849288"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Transformera data med Hadoop Hive-aktivitet i Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-hive-activity.md)
> * [Aktuell version](transform-data-using-hadoop-hive.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

HDInsight Hive-aktiviteten i en Data Factory [pipeline](concepts-pipelines-activities.md) kör Hive-frågor på [ditt eget](compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran HDInsight-](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)  kluster. Den här artikeln bygger på artikeln [data omvandlings aktiviteter](transform-data.md) , som visar en allmän översikt över Datatransformeringen och de omvandlings aktiviteter som stöds.

Om du är nybörjare på Azure Data Factory läser du [Introduktion till Azure Data Factory](introduction.md) och gör [självstudien: transformera data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

## <a name="syntax"></a>Syntax

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```
## <a name="syntax-details"></a>Information om syntax
| Egenskap            | Beskrivning                                                  | Krävs |
| ------------------- | ------------------------------------------------------------ | -------- |
| name                | Namn på aktiviteten                                         | Ja      |
| description         | Text som beskriver vad aktiviteten används för                | Inga       |
| typ                | För Hive-aktivitet är aktivitets typen HDinsightHive        | Ja      |
| linkedServiceName   | Referens till HDInsight-klustret som registrerats som en länkad tjänst i Data Factory. Mer information om den här länkade tjänsten finns i artikeln [Compute-länkade tjänster](compute-linked-services.md) . | Ja      |
| scriptLinkedService | Referens till en Azure Storage länkad tjänst som används för att lagra Hive-skriptet som ska köras. Endast **[Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)** -och **[ADLS Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)** länkade tjänster stöds här. Om du inte anger den här länkade tjänsten används den Azure Storage länkade tjänsten som definierats i den länkade HDInsight-tjänsten.  | Inga       |
| scriptPath          | Ange sökvägen till skript filen som lagras i Azure Storage som refereras av scriptLinkedService. Fil namnet är Skift läges känsligt. | Ja      |
| getDebugInfo        | Anger när loggfilerna kopieras till Azure Storage som används av HDInsight-kluster (eller) som anges av scriptLinkedService. Tillåtna värden: ingen, Always eller Failure. Standardvärde: ingen. | Inga       |
| ogiltiga           | Anger en matris med argument för ett Hadoop-jobb. Argumenten skickas som kommando rads argument till varje aktivitet. | Inga       |
| definierar             | Ange parametrar som nyckel/värde-par för referenser i Hive-skriptet. | Inga       |
| queryTimeout        | Tids gräns värde för fråga (i minuter). Gäller när HDInsight-klustret är med Enterprise Security Package aktiverat. | Nej       |

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omformar data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Aktivitet i gris](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning batch-körning, aktivitet](transform-data-using-machine-learning.md)
* [Lagrad procedur aktivitet](transform-data-using-stored-procedure.md)
