---
title: Migrera till anslutnings övervakaren (förhands granskning) från anslutnings övervakaren
titleSuffix: Azure Network Watcher
description: Lär dig hur du migrerar till anslutnings övervakaren från anslutnings övervakaren.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: d785ecd48816150cd33d8e2f5fcce2ff36f1603f
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94985798"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor-classic"></a>Migrera till anslutnings övervakaren (förhands granskning) från anslutnings övervakaren (klassisk)

Du kan migrera befintliga anslutnings övervakare till ny, förbättrad anslutnings övervakare (för hands version) med bara några klick och utan avbrott. Läs mer om fördelarna i [anslutnings övervakaren](./connection-monitor-overview.md).

> [!IMPORTANT]
> Anslutnings övervakaren är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-points-to-note"></a>Viktiga punkter att Observera

Migreringen hjälper till att producera följande resultat:

* Agenter och brand Väggs inställningar fungerar som de är. Inga ändringar krävs. 
* Befintliga anslutnings övervakare mappas till anslutnings övervakaren > test grupp > test format. Genom att välja **Redigera** kan du Visa och ändra egenskaperna för den nya anslutnings övervakaren, hämta en mall för att göra ändringar i anslutnings övervakaren och skicka den via Azure Resource Manager. 
* Virtuella Azure-datorer med Network Watcher-tillägget skickar data till både arbets ytan och måtten. Anslutnings övervakaren gör data tillgängliga via de nya måtten (ChecksFailedPercent [Preview] och RoundTripTimeMs [Preview]) i stället för de gamla måtten (ProbesFailedPercent och AverageRoundtripMs). 
* Data övervakning:
   * **Aviseringar**: migreras automatiskt till de nya måtten.
   * **Instrument paneler och integreringar**: Kräv manuell redigering av de angivna måtten. 
    
## <a name="prerequisites"></a>Förutsättningar

Om du använder en anpassad arbets yta ser du till att Network Watcher är aktive rad i din prenumeration och i den region i Log Analytics arbets ytan. 

## <a name="migrate-the-connection-monitors"></a>Migrera anslutnings övervakare

1. Om du vill migrera äldre anslutnings övervakare till den nyare väljer du **anslutnings övervakare** och väljer sedan **migrera anslutnings** övervakare.

    ![Skärm bild som visar migreringen av anslutnings övervakare till anslutnings övervakaren.](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. Välj din prenumeration och de anslutnings Övervakare som du vill migrera och välj sedan **Migrera markerade**. 

Med bara några klick har du migrerat de befintliga anslutnings övervakarna till anslutnings övervakaren. 

Nu kan du anpassa egenskaperna för anslutnings övervakaren, ändra standard arbets ytan, hämta mallar och kontrol lera migreringens status. 

När migreringen har påbörjats sker följande ändringar: 
* Azure Resource Manager resursen ändras till den nyare anslutnings övervakaren.
    * Anslutnings övervakarens namn, region och prenumeration förblir oförändrade. Resurs-ID: t påverkas inte.
    * Om inte anslutnings övervakaren är anpassad skapas en standard arbets yta Log Analytics i prenumerationen och i regionen för anslutnings övervakaren. Den här arbets ytan är platsen där övervaknings data lagras. Test resultat data lagras också i måtten.
    * Varje test migreras till en test grupp som kallas *defaultTestGroup*.
    * Käll-och mål slut punkter skapas och används i den nya test gruppen. Standard namnen är *defaultSourceEndpoint* och *defaultDestinationEndpoint*.
    * Mål porten och söknings intervallet flyttas till en test konfiguration som kallas *defaultTestConfiguration*. Protokollet anges baserat på port värden. Tröskelvärden för lyckade och andra valfria egenskaper lämnas tomma.
* Mått aviseringar migreras till anslutnings övervakarens mått aviseringar. Måtten är olika, därför ändringen. Mer information finns i [övervakning av nätverks anslutning med anslutnings övervakaren](./connection-monitor-overview.md#metrics-in-azure-monitor).
* De migrerade anslutnings övervakarna visas inte längre som den äldre lösningen för anslutnings övervakning. De är nu tillgängliga för användning i anslutnings övervakaren.
* Alla externa integrationer, till exempel instrument paneler i Power BI-och Grafana, och integreringar med säkerhets informations-och händelse hanterings system (SIEM) måste migreras manuellt. Det här är det enda manuella steget du behöver utföra för att migrera installationen.

## <a name="next-steps"></a>Nästa steg

Mer information om anslutnings övervakaren finns i:
* [Migrera från Övervakare av nätverksprestanda till anslutnings övervakaren](/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor)
* [Skapa anslutnings övervakare med hjälp av Azure Portal](./connection-monitor-create-using-portal.md)