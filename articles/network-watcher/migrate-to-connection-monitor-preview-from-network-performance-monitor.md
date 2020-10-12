---
title: Migrera till anslutnings övervakaren (förhands granskning) från Övervakare av nätverksprestanda
titleSuffix: Azure Network Watcher
description: Lär dig hur du migrerar till anslutnings övervakaren (för hands version) från Övervakare av nätverksprestanda.
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
ms.openlocfilehash: dcbb82c1315e6150ddcfadbb52b2976447329b87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441841"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>Migrera till anslutnings övervakaren (förhands granskning) från Övervakare av nätverksprestanda

Du kan migrera tester från Övervakare av nätverksprestanda (NPM) till ny, förbättrad anslutnings övervakare (för hands version) med ett enda klick och med noll stillestånds tid. Läs mer om fördelarna i [anslutnings övervakaren (för hands version)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview).

>[!NOTE]
> Endast tester från övervakaren för tjänst anslutning kan migreras till anslutnings övervakaren (för hands version).
>

## <a name="key-points-to-note"></a>Viktiga punkter att Observera

Migreringen hjälper till att producera följande resultat:

* Lokala agenter och brand Väggs inställningar fungerar som de är. Inga ändringar krävs. Log Analytics agenter som är installerade på virtuella Azure-datorer måste ersättas med Network Watcher-tillägget.
* Befintliga tester mappas till anslutnings övervakaren (förhands granskning) > test grupp > test format. Genom att välja **Redigera**kan du Visa och ändra egenskaperna för den nya anslutnings övervakaren (för hands version), hämta en mall för att göra ändringar i den och skicka mallen via Azure Resource Manager.
* Agenter skickar data till både arbets ytan Log Analytics och måtten.
* Data övervakning:
   * **Data i Log Analytics**: innan migreringen finns data kvar i arbets ytan där NPM har kon figurer ATS i NetworkMonitoring-tabellen. Efter migreringen går data till NetworkMonitoring-tabellen och ConnectionMonitor_CL tabellen i samma arbets yta. När testerna har inaktiverats i NPM lagras data endast i ConnectionMonitor_CLs tabellen.
   * **Loggbaserade aviseringar, instrument paneler och integreringar**: du måste redigera frågorna manuellt baserat på den nya ConnectionMonitor_CLs tabellen. Information om hur du återskapar aviseringarna i mått finns i [övervakning av nätverks anslutning med anslutnings övervakare (för hands version)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#metrics-in-azure-monitor).
    
## <a name="prerequisites"></a>Förutsättningar

* Se till att Network Watcher är aktive rad i din prenumeration och regionen för arbets ytan Log Analytics.
* Virtuella Azure-datorer med Log Analytics installerade agenter måste vara aktiverade med Network Watcher-tillägget.

## <a name="migrate-the-tests"></a>Migrera testerna

Om du vill migrera testerna från Övervakare av nätverksprestanda till anslutnings övervakaren (för hands version) gör du följande:

1. I Network Watcher väljer du **anslutnings övervakare**och väljer sedan fliken **MIGRERA tester från NPM** . 

    ![Skärm bild som visar fönstret "migrera test från NPM" i Network Watcher | Anslutnings övervakare (för hands version).](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1. I list rutorna väljer du din prenumeration och arbets yta och väljer sedan den NPM-funktion som du vill migrera. För närvarande kan du endast migrera tester från övervakaren för tjänst anslutning.  
1. Klicka på **Importera** för att migrera testerna.

När migreringen har påbörjats sker följande ändringar: 
* En ny anslutnings övervaknings resurs skapas.
   * En anslutnings övervakare per region och prenumeration skapas. För tester med lokala agenter formateras namnet för den nya anslutnings övervakaren som `<workspaceName>_"on-premises"` . För tester med Azure-agenter formateras namnet för den nya anslutnings övervakaren som `<workspaceName>_<Azure_region_name>` .
   * Övervaknings data lagras nu i samma Log Analytics arbets yta där NPM har Aktiver ATS, i en ny tabell med namnet Connectionmonitor_CL. 
   * Test namnet överförs som test gruppens namn. Test beskrivningen har inte migrerats.
   * Käll-och mål slut punkter skapas och används i den nya test gruppen. För lokala agenter formateras slut punkterna som `<workspaceName>_"endpoint"_<FQDN of on-premises machine>` . För Azure, om migreringen innehåller agenter som inte körs, måste du aktivera agenterna och migrera igen.
   * Mål porten och söknings intervallet flyttas till en test konfiguration med namnet *TC_ \<testname> * och *TC_ \<testname> _AppThresholds*. Protokollet anges baserat på port värden. Tröskelvärden för lyckade och andra valfria egenskaper lämnas tomma.
* NPM är inte inaktiverat, så de migrerade testerna kan fortsätta att skicka data till NetworkMonitoring och ConnectionMonitor_CL tabeller. Den här metoden säkerställer att befintliga loggbaserade aviseringar och integreringar inte påverkas.
* Den nyligen skapade anslutnings övervakaren visas i anslutnings övervakaren (för hands version).

Efter migreringen, se till att:
* Inaktivera testerna manuellt i NPM. Innan du gör det kan du fortsätta att betala för dem. 
* När du har inaktiverat NPM återskapar du aviseringarna på ConnectionMonitor_CLs tabellen eller använder mått. 
* Migrera alla externa integreringar till ConnectionMonitor_CLs tabellen. Exempel på externa integreringar är instrument paneler i Power BI-och Grafana, och integreringar med SIEM-system (Security information and Event Management).


## <a name="next-steps"></a>Nästa steg

Mer information om anslutnings övervakaren (för hands version) finns i:
* [Migrera från anslutnings övervakaren till anslutnings övervakaren (för hands version)](migrate-to-connection-monitor-preview-from-connection-monitor.md)
* [Skapa anslutnings övervakare (för hands version) med hjälp av Azure Portal](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
