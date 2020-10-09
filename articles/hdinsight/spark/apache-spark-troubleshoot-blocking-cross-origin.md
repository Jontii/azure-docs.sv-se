---
title: Jupyter 404-fel-"blockering av cross origin API" – Azure HDInsight
description: Det gick inte att hitta Jupyter Server 404 på grund av "blockering av cross origin API" i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: e241657186582955d21981f7dfe18856724aa692
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894412"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>Scenario: det gick inte att hitta Jupyter Server 404 på grund av "blockering av cross origin API" i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="issue"></a>Problem

När du har åtkomst till Jupyter-tjänsten i HDInsight visas en felruta som säger "hittades inte". Om du markerar Jupyter-loggarna ser du något som liknar detta:

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Du kan också se en IP-adress i fältet "ursprung" i Jupyter-loggen.

## <a name="cause"></a>Orsak

Det här felet kan orsakas av ett par saker:

- Om du har konfigurerat regler för nätverks säkerhets grupper (NSG) för att begränsa åtkomsten till klustret. Genom att begränsa åtkomsten med NSG-regler kan du fortfarande komma åt Apache Ambari och andra tjänster direkt med hjälp av IP-adressen i stället för kluster namnet. Men vid åtkomst till Jupyter kan du se ett 404-fel som inte hittades.

- Om du har gett din HDInsight-Gateway ett anpassat DNS-namn som inte är standard `xxx.azurehdinsight.net` .

## <a name="resolution"></a>Lösning

1. Ändra jupyter.py-filerna på följande två platser:

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. Hitta raden som säger: `NotebookApp.allow_origin='\"https://{2}.{3}\"'` och ändra den till: `NotebookApp.allow_origin='\"*\"'` .

1. Starta om Jupyter-tjänsten från Ambari.

1. Om du skriver `ps aux | grep jupyter` i kommando tolken bör du visa att den tillåter alla URL: er att ansluta till den.

Detta är ett mindre säkert än den inställning som vi redan hade på plats. Men det förutsätts att åtkomst till klustret är begränsad och att en från utsidan tillåts ansluta till klustret eftersom vi har NSG på plats.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
