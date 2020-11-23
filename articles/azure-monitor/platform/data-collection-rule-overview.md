---
title: Data insamlings regler i Azure Monitor (för hands version)
description: Översikt över data insamlings regler (DCRs) i Azure Monitor inklusive innehåll och struktur och hur du kan skapa och arbeta med dem.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: 048068a74151bb986392b5cb27787385fc0f5363
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95315540"
---
# <a name="data-collection-rules-in-azure-monitor-preview"></a>Data insamlings regler i Azure Monitor (för hands version)
Data insamlings regler (DCR) definierar data som kommer till Azure Monitor och anger var data ska skickas eller lagras. Den här artikeln innehåller en översikt över data insamlings regler, inklusive deras innehåll och struktur och hur du kan skapa och arbeta med dem.

## <a name="input-sources"></a>Inmatade källor
Data insamlings regler stöder för närvarande följande inmatnings källor:

- Virtuell Azure-dator med Azure Monitor agenten. Se [Konfigurera data insamling för Azure Monitor agenten (för hands version)](data-collection-rule-azure-monitor-agent.md).



## <a name="components-of-a-data-collection-rule"></a>Komponenter i en data insamlings regel
En data insamlings regel innehåller följande komponenter.

| Komponent | Beskrivning |
|:---|:---|
| Datakällor | Unik källa för övervakning av data med eget format och metod för att exponera data. Exempel på en data källa är Windows-händelseloggen, prestanda räknare och syslog. Varje data källa matchar en viss typ av data källa enligt beskrivningen nedan. |
| Strömmar | Unik referens som beskriver en uppsättning data källor som omvandlas och schematiserade som en typ. Varje data källa kräver en eller flera strömmar, och en data ström kan användas av flera data källor. Alla data källor i en data ström delar ett gemensamt schema. Använd flera strömmar till exempel när du vill skicka en viss data källa till flera tabeller i samma Log Analytics-arbetsyta. |
| Mål | En uppsättning destinationer där data ska skickas. Exempel på detta är Log Analytics arbets yta, Azure Monitor mått och Azure-Event Hubs. | 
| Dataflöden | Definition av vilka strömmar som ska skickas till vilka mål. | 

Följande diagram visar komponenterna i en data insamlings regel och deras relation

[![Diagram över DCR](media/data-collection-rule/data-collection-rule-components.png)](media/data-collection-rule/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>Typer av datakälla
Varje data källa har en typ av data källa. Varje typ definierar en unik uppsättning egenskaper som måste anges för varje data källa. De data käll typer som är tillgängliga visas i följande tabell.

| Typ av data Källa | Description | 
|:---|:---|
| utöka | VM-baserad data Källa |
| performanceCounters | Prestanda räknare för både Windows och Linux |
| syslog | Syslog-händelser på Linux |
| windowsEventLogs | Händelse loggen i Windows |


## <a name="limits"></a>Gränser
Begränsningar som gäller för varje data insamlings regel finns i [Azure Monitor tjänst begränsningar](../service-limits.md#data-collection-rules).


## <a name="create-a-dcr"></a>Skapa en DCR
Det finns för närvarande två tillgängliga metoder för att skapa en DCR:

- [Använd Azure Portal](data-collection-rule-azure-monitor-agent.md) för att skapa en data insamlings regel och associera den med en eller flera virtuella datorer.
- Redigera data insamlings regeln direkt i JSON och [skicka med REST API](/rest/api/monitor/datacollectionrules).

## <a name="sample-data-collection-rule"></a>Exempel på data insamlings regel
Exempel data insamlings regeln nedan är för virtuella datorer med Azures hanterings agent och har följande information:

- Prestandadata
  - Samlar in vissa räknare för processor, minne, logisk disk och fysisk disk var 15: e sekund och laddar upp varje minut.
  - Samlar in vissa process räknare var 30: e sekund och laddar upp var femte minut.
- Windows-händelser
  - Samlar in säkerhets händelser i Windows och laddar upp varje minut.
  - Samlar in Windows-program och system händelser och laddar upp var femte minut.
- Syslog
  - Samlar in fel söknings-, kritiska-och nöd händelser från cron-anläggningen.
  - Samlar in aviseringar, kritiska händelser och nöd händelser från syslog-anläggningen.
- Mål
  - Skickar alla data till en Log Analytics arbets yta med namnet centralWorkspace.

```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "syslogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-WindowsEvent"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>Nästa steg

- [Skapa en data insamlings regel](data-collection-rule-azure-monitor-agent.md) och en association till den från en virtuell dator med hjälp av Azure Monitor agenten.