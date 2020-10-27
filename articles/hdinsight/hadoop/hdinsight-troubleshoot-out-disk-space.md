---
title: Klusternoden tar slut på disk utrymme i Azure HDInsight
description: Fel sökning av problem med disk utrymmet Apache Hadoop klusternoden i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: 042a894d9f6f2a8fb7737d0a20abeb2293f756aa
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540609"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scenario: klusternoden tar slut på disk utrymme i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Ett jobb kan Miss lyckas med ett fel meddelande som liknar: `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Eller så kan du få Apache Ambari-aviseringar som liknar: `local-dirs usable space is below configured utilization percentage` .

## <a name="cause"></a>Orsak

Apache garn Application cache kan ha använt allt tillgängligt disk utrymme. Spark-programmet körs förmodligen ineffektivt.

## <a name="resolution"></a>Lösning

1. Använd Ambari UI för att avgöra vilken nod som håller på att ta slut på disk utrymme.

1. Bestäm vilken mapp i oroande-noden som bidrar till det mesta av disk utrymmet. Använd först SSH till noden och sedan `df` Visa disk användning för alla monteringar. Det är vanligt vis `/mnt` en temporär disk som används av oss. Du kan ange i en mapp och sedan skriva `sudo du -hs` för att Visa sammanfattade fil storlekar under en mapp. Om du ser en mapp liknande `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007` innebär det att programmet fortfarande körs. Detta kan bero på RDD beständighet eller mellanliggande blandade filer.

1. Du kan åtgärda problemet genom att avsluta programmet, vilket frigör disk utrymme som används av programmet.

1. Om problemet inträffar ofta på arbetsnoderna kan du justera inställningarna för lokalt cacheminne för garn i klustret.

    Öppna Ambari-ANVÄNDARGRÄNSSNITTET navigera till garn--> configs--> Avancerat.  
    Lägg till följande två egenskaper i avsnittet anpassad yarn-site.xml och spara:

    ```
    yarn.nodemanager.localizer.cache.target-size-mb=2048
    yarn.nodemanager.localizer.cache.cleanup.interval-ms=300000
    ```

1. Om det här inte löser problemet permanent kan du optimera ditt program.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).