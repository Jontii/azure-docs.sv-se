---
title: 'Felsöka: Apache Hive loggar fyller i disk utrymme – Azure HDInsight'
description: Den här artikeln innehåller fel söknings steg att följa när Apache Hive loggar fyller upp disk utrymmet på huvudnoderna i Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 10/05/2020
ms.openlocfilehash: 64bf5714f5eb99df9929a47fef414a827ec680af
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145641"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scenario: Apache Hive loggar fyller upp disk utrymmet på Head-noderna i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar på problem som rör otillräckligt med disk utrymme på huvudnoderna i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

I ett Apache Hive/LLAP-kluster tar oönskade loggar upp hela disk utrymmet på huvudnoderna. Det här tillståndet kan orsaka följande problem:

- SSH-åtkomsten Miss lyckas eftersom inget utrymme är kvar på Head-noden.
- Ambari genererar *http-fel: tjänsten 503 är inte tillgänglig* .
- HiveServer2 interaktiva kan inte startas om.

`ambari-agent`Loggarna innehåller följande poster när problemet inträffar:
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Orsak

I avancerade Hive-log4j konfigurationer är det aktuella standard borttagnings schemat att ta bort filer som är äldre än 30 dagar, baserat på datum för senaste ändring.

## <a name="resolution"></a>Lösning

1. Gå till sammanfattningen av Hive-komponenten på Ambari-portalen och välj fliken **konfigurationer** .

2. Gå till `Advanced hive-log4j` avsnittet i **Avancerade inställningar** .

3. Ange `appender.RFA.strategy.action.condition.age` en valfri ålder för parametern. I det här exemplet anges ålder till 14 dagar: `appender.RFA.strategy.action.condition.age = 14D`

4. Om du inte ser några relaterade inställningar lägger du till följande inställningar:
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. Ange `hive.root.logger` som `INFO,RFA` , som visas i följande exempel. Standardvärdet är `DEBUG` , vilket gör loggarna stora.

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. Spara konfigurationerna och starta om de nödvändiga komponenterna.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
