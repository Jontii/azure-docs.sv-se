---
title: Det gick inte att läsa Apache-Garnets logg i Azure HDInsight
description: Fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "76776201"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Scenario: det går inte att läsa Apache-Garnets logg i Azure HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Apache-garnen från lagrings kontot är inte läsligt. Fil parsern fungerar inte och genererar följande fel meddelande:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Orsak

Apache-Garnets logg sammanställs i `IndexFile` format, vilket inte stöds av fil parsern.

## <a name="resolution"></a>Lösning

1. I en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net` , där `CLUSTERNAME` är namnet på klustret.

1. Från Ambari UI navigerar du till **garn**  >  **configs**  >  **Advanced**  >  **Advanced garn-site**.

1. För WASB-lagring: standardvärdet för `yarn.log-aggregation.file-formats` är `IndexedFormat,TFile` . Ändra värdet till `TFile` .

1. För ADLS-lagring: standardvärdet för `yarn.nodemanager.log-aggregation.compression-type` är `gz` . Ändra värdet till `none` .

1. Spara ändringen och starta om alla berörda tjänster.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
