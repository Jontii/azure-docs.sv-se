---
title: IllegalArgumentException-fel för Apache Spark – Azure HDInsight
description: IllegalArgumentException för Apache Spark-aktivitet i Azure HDInsight för Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 334e8d129a7b5bb79c9e01d5ba7f347682e5bd79
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545624"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Scenario: IllegalArgumentException för Apache Spark-aktivitet i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Du får följande undantag när du försöker köra en spark-aktivitet i en Azure Data Factory pipeline:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Orsak

Ett Spark-jobb kommer att Miss förväntas om programmets jar-fil inte finns i Spark-klustrets standard-/primär lagring.

Detta är ett känt problem med Spark-ramverket med öppen källkod som spåras i det här felet: [Spark-jobb Miss lyckas om FS. defaultFS och Application jar är olika URL: er](https://issues.apache.org/jira/browse/SPARK-22587).

Det här problemet har lösts i Spark 2.3.0.

## <a name="resolution"></a>Lösning

Se till att programmet jar är lagrat på standard-/primär lagrings platsen för HDInsight-klustret. Om Azure Data Factory ska du kontrol lera att den länkade ADF-tjänsten pekar mot standard behållaren för HDInsight i stället för en sekundär behållare.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).