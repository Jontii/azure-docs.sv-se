---
title: Kors tjänst fråga mellan Azure Monitor och Azure Datautforskaren (för hands version)
description: Fråga Azure Datautforskaren data via Azure Log Analytics-verktyg vice versa för att ansluta och analysera alla dina data på en och samma plats.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 2b68afcb9d200970ca4ea29b13175223f52c77e0
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730997"
---
# <a name="cross-service-query---azure-monitor-and-azure-data-explorer-preview"></a>Kors tjänst fråga – Azure Monitor och Azure Datautforskaren (för hands version)
Skapa kors tjänst frågor mellan [Azure datautforskaren](/azure/data-explorer/), [Application Insights](../app/app-insights-overview.md)och [Log Analytics](./data-platform-logs.md).
## <a name="azure-monitor-and-azure-data-explorer-cross-service-querying"></a>Fråga om Azure Monitor och Azure-Datautforskaren frågor om kors tjänster
Med den här funktionen kan du [skapa kors tjänst frågor mellan Azure datautforskaren och Azure Monitor](/azure/data-explorer/query-monitor-data) och [skapa kors tjänst frågor mellan Azure Monitor och Azure datautforskaren](./azure-monitor-data-explorer-proxy.md).

Till exempel (fråga Azure-Datautforskaren från Log Analytics):
```kusto
CustomEvents | where aField == 1
| join (adx("Help/Samples").TableName | where secondField == 3) on $left.Key == $right.key
```
Där den yttre frågan frågar en tabell i arbets ytan och sedan ansluter till en annan tabell i ett Azure Datautforskaren-kluster (i det här fallet kluster namn = hjälp, databasename = exempel) med hjälp av en ny "ADX ()"-funktion, t. ex. hur du kan göra samma sak för att fråga en annan arbets yta från en frågetext.

> [!NOTE]
> * Möjligheten att fråga Azure Monitor data från Azure Datautforskaren, antingen direkt från Azure Datautforskaren klient verktyg eller indirekt genom att köra en fråga på ett Azure Datautforskaren-kluster, är i förhands gransknings läge.
> * Kontakta vår [fråga om kors tjänster](mailto:adxproxy@microsoft.com) med eventuella frågor.

## <a name="query-exported-log-analytics-data-from-azure-blob-storage-account"></a>Fråga exporterad Log Analytics data från Azure Blob Storage-konto

Export av data från Azure Monitor till ett Azure Storage-konto möjliggör låg kostnads kvarhållning och möjlighet att omfördela loggar till olika regioner.

Använd Azure-Datautforskaren för att fråga data som har exporter ATS från dina Log Analytics arbets ytor. När den har kon figurer ATS kommer de tabeller som stöds som skickas från dina arbets ytor till ett Azure Storage-konto att vara tillgängliga som data källa för Azure-Datautforskaren. [Fråga exporterade data från Azure monitor med Azure datautforskaren (för hands version)](./azure-data-explorer-query-storage.md).

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-query.png" alt-text="Azure Datautforskaren fråga från lagrings flödet.":::

>[!tip] 
> * Om du vill exportera alla data från din Log Analytics-arbetsyta till ett Azure Storage-konto eller en händelsehubben, använder du data export funktionen Log Analytics arbets yta för Azure Monitor loggar. [Se Log Analytics data export för arbets ytan i Azure Monitor (för hands version)](/azure/data-explorer/query-monitor-data).

## <a name="next-steps"></a>Nästa steg
Läs mer om:
* [skapa kors tjänst frågor mellan Azure datautforskaren och Azure Monitor](/azure/data-explorer/query-monitor-data). Fråga Azure Monitor data från Azure Datautforskaren
* [skapa kors tjänst frågor mellan Azure Monitor och Azure datautforskaren](./azure-monitor-data-explorer-proxy.md). Fråga Azure Datautforskaren-data från Azure Monitor
* [Log Analytics arbets ytans data export i Azure Monitor (för hands version)](/azure/data-explorer/query-monitor-data). Länka och fråga Azure Blob Storage-kontot med Log Analytics exporterade data.