---
title: Azure Relay mått i Azure Monitor | Microsoft Docs
description: Den här artikeln innehåller information om hur du kan använda Azure Monitor för att övervaka till Azure Relays tillstånd.
services: service-bus-relay
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5fcc0f820983da388d808cadf8ce64a555e8ef0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87532891"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Azure Relay mått i Azure Monitor 
Azure Relay mått ger dig tillstånd för resurser i din Azure-prenumeration. Med en omfattande uppsättning Mät data kan du utvärdera övergripande hälso tillstånd för dina relä resurser, inte bara på namn områdes nivå, utan även på enhets nivå. Den här statistiken kan vara viktig när de hjälper dig att övervaka status för Azure Relay. Mått kan också hjälpa till att felsöka rotor Saks problem utan att behöva kontakta Azure-supporten.

Azure Monitor tillhandahåller enhetliga användar gränssnitt för övervakning i olika Azure-tjänster. Mer information finns i [övervakning i Microsoft Azure](../azure-monitor/overview.md) och [Hämta Azure Monitor mått med .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) -exempel på GitHub.

> [!IMPORTANT]
> Den här artikeln gäller bara för den Hybridanslutningar funktionen i Azure Relay, inte på WCF Relay. 

## <a name="access-metrics"></a>Åtkomst mått

Azure Monitor ger till gång till mått på flera sätt. Du kan antingen komma åt mått via [Azure Portal](https://portal.azure.com)eller använda Azure Monitor-API: er (rest och .net) och analys lösningar som operation Management Suite och Event Hubs. Mer information finns i [övervaka data som samlas in av Azure Monitor](../azure-monitor/platform/data-platform.md).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna med data. Om du behöver spara data under en längre tids period kan du arkivera mått data till ett Azure Storage konto. Detta konfigureras i [diagnostikinställningar](../azure-monitor/platform/diagnostic-settings.md) i Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Åtkomst mått i portalen

Du kan övervaka mått över tid i [Azure Portal](https://portal.azure.com). I följande exempel visas hur du visar lyckade förfrågningar och inkommande begär Anden på konto nivå:

![En sida med namnet "Monitor-Metrics (för hands version)" visar ett linje diagram över minnes användningen för de senaste 30 dagarna.][1]

Du kan också komma åt mått direkt via namn området. Om du vill göra det väljer du namn området och klickar sedan på * * mått * *. 

För mått som stöder dimensioner måste du filtrera med det önskade dimension svärdet.

## <a name="billing"></a>Fakturering

Användning av mått i Azure Monitor är för närvarande kostnads fritt i för hands versionen. Men om du använder ytterligare lösningar som inhämtar mått data kan du debiteras av dessa lösningar. Till exempel debiteras du per Azure Storage om du arkiverar mått data till ett Azure Storage konto. Du debiteras också med Azure Monitor loggar om du strömmar mått data till Azure Monitor loggar för avancerad analys.

Följande mått ger en översikt över hälso tillståndet för din tjänst. 

> [!NOTE]
> Vi har föråldrat flera mått när de flyttas under ett annat namn. Detta kan kräva att du uppdaterar dina referenser. Mått som har marker ATS med nyckelordet "inaktuell" kommer inte att stödjas framåt.

Alla mått värden skickas till Azure Monitor varje minut. Tids kornig het definierar tidsintervallet för vilka mått värden presenteras. Det tidsintervall som stöds för alla Azure Relay mått är 1 minut.

## <a name="connection-metrics"></a>Anslutnings mått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| ListenerConnections-Success  | Antalet lyckade lyssnar anslutningar som har gjorts till Azure Relay under en angiven period. <br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|ListenerConnections-ClientError |Antalet klient fel i lyssnar anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|ListenerConnections-ServerError |Antalet Server fel på lyssnar anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|SenderConnections-Success |Antalet lyckade avsändare som gjorts under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|SenderConnections-ClientError |Antalet klient fel på avsändarens anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|SenderConnections-ServerError |Antalet Server fel på avsändarens anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|ListenerConnections-TotalRequests |Det totala antalet lyssnar anslutningar under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|SenderConnections-TotalRequests |Anslutnings förfrågningar som skickats av avsändarna under en angiven period.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|ActiveConnections |Antalet aktiva anslutningar. Det här värdet är ett värde för tidpunkt.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|ActiveListeners |Antalet aktiva lyssnare. Det här värdet är ett värde för tidpunkt.<br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|ListenerDisconnects |Antalet frånkopplade lyssnare under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|
|SenderDisconnects |Antalet frånkopplade avsändare under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|

## <a name="memory-usage-metrics"></a>Minnes användnings mått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|BytesTransferred |Antalet byte som överförts under en angiven period.<br/><br/> Enhet: byte <br/> Sammansättnings typ: totalt <br/> Dimension: EntityName|

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Relay stöder följande dimensioner för mått i Azure Monitor. Det är valfritt att lägga till dimensioner i måtten. Om du inte lägger till dimensioner anges måtten på namn områdes nivå. 

|Dimensions namn|Beskrivning|
| ------------------- | ----------------- |
|Entitetsnamnet| Azure Relay stöder meddelande enheter under namn området.|

## <a name="next-steps"></a>Nästa steg

Se [Översikt över Azure Monitoring](../azure-monitor/overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png
