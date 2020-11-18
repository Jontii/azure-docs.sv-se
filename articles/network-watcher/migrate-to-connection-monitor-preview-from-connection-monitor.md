---
title: Migrera till anslutnings övervakaren från anslutnings övervakaren
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
ms.openlocfilehash: 4d77ec77bae0394b11cb47ae838740dc39855f7c
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94699075"
---
# <a name="migrate-to-connection-monitor-from-connection-monitor"></a>Migrera till anslutnings övervakaren från anslutnings övervakaren

Du kan migrera befintliga anslutnings övervakare till ny, förbättrad anslutnings övervakare med bara några klick och utan stillestånds tid. Läs mer om fördelarna i [anslutnings övervakaren](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview).

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
* Mått aviseringar migreras till anslutnings övervakarens mått aviseringar. Måtten är olika, därför ändringen. Mer information finns i [övervakning av nätverks anslutning med anslutnings övervakaren](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#metrics-in-azure-monitor).
* De migrerade anslutnings övervakarna visas inte längre som den äldre lösningen för anslutnings övervakning. De är nu tillgängliga för användning i anslutnings övervakaren.
* Alla externa integrationer, till exempel instrument paneler i Power BI-och Grafana, och integreringar med säkerhets informations-och händelse hanterings system (SIEM) måste migreras manuellt. Det här är det enda manuella steget du behöver utföra för att migrera installationen.

## <a name="next-steps"></a>Nästa steg

Mer information om anslutnings övervakaren finns i:
* [Migrera från Övervakare av nätverksprestanda till anslutnings övervakaren](migrate-to-connection-monitor-preview-from-network-performance-monitor.md)
* [Skapa anslutnings övervakare med hjälp av Azure Portal](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
