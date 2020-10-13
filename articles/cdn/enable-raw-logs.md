---
title: Övervaknings mått och råa loggar för Azure CDN från Microsoft
description: I den här artikeln beskrivs Azure CDN från Microsoft Monitoring Metrics och RAW-loggar.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/25/2020
ms.author: allensu
ms.openlocfilehash: c41bf8bc6e5aa3749786bc1189343dfdebdc1508
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321253"
---
# <a name="monitoring-metrics-and-raw-logs-for-azure-cdn-from-microsoft"></a>Övervaknings mått och råa loggar för Azure CDN från Microsoft
Med Azure CDN från Microsoft kan du övervaka resurser på följande sätt för att hjälpa dig att felsöka, spåra och felsöka problem. 

* Obehandlade loggar innehåller omfattande information om varje begäran som CDN tar emot. Obehandlade loggar skiljer sig från aktivitets loggar. Aktivitets loggarna ger insyn i de åtgärder som utförs på Azure-resurser.
* Mått, som visar fyra nyckel mått för CDN, inklusive antal byte träff, antal förfrågningar, svars storlek och total svars tid. Den innehåller också olika mått för att dela upp mått.
* Avisering, som gör att kunden kan ställa in aviseringar för nyckel mått
* Ytterligare mått, som gör det möjligt för kunder att använda Azure Log Analytics för att aktivera ytterligare Mät värden. Vi tillhandahåller även fråge exempel för några andra mått under Azure Log Analytics.

> [!IMPORTANT]
> Funktionen HTTP RAW-loggar är tillgänglig för Azure CDN från Microsoft.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration---azure-portal"></a>Konfiguration – Azure Portal

Så här konfigurerar du obehandlade loggar för din Azure CDN från Microsoft-profilen: 

1. Välj **alla resurser**på Azure Portal-menyn  >>  **\<your-CDN-profile>** .

2. Under **Övervakning** väljer du **Diagnostikinställningar**.

3. Välj **+ Lägg till diagnostisk inställning**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="Lägg till diagnostisk inställning för CDN-profil." border="true":::
    
    > [!IMPORTANT]
    > Råa loggar är bara tillgängliga på profil nivå medan sammanställda HTTP-statuskod loggar är tillgängliga på slut punkts nivå.

4. Under **diagnostikinställningar**anger du ett namn för den diagnostiska inställningen under **diagnostiska**inställnings namn.

5. Välj **AzureCdnAccessLog** och ange kvarhållning i dagar.

6. Välj **mål information**. Mål alternativen är:
    * **Skicka till Log Analytics**
        * Välj **prenumerationen** och **Log Analytics arbets ytan**.
    * **Arkivera till ett lagrings konto**
        * Välj **prenumerationen** och **lagrings kontot**.
    * **Strömma till en Event Hub**
        * Välj **prenumeration**, **Event Hub-namnrymd**, **Event Hub-namn (valfritt)** och **princip namn för Event Hub**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="Lägg till diagnostisk inställning för CDN-profil." border="true":::

7. Välj **Spara**.

## <a name="configuration---azure-powershell"></a>Konfiguration – Azure PowerShell

Använd [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) för att konfigurera den diagnostiska inställningen för obehandlade loggar.

Kvarhållning av data definieras av alternativet **-RetentionInDays** i kommandot.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Aktivera diagnostikloggar i ett lagrings konto

1. Logga in på Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Om du vill aktivera diagnostikloggar i ett lagrings konto anger du dessa kommandon. Ersätt variablerna med dina värden:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Aktivera diagnostikloggar för Log Analytics arbets yta

1. Logga in på Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Om du vill aktivera diagnostikloggar för en arbets yta Log Analytics anger du dessa kommandon. Ersätt variablerna med dina värden:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Aktivera diagnostikloggar för Event Hub-namnområde

1. Logga in på Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Om du vill aktivera diagnostikloggar för ett Event Hub-namnområde anger du dessa kommandon. Ersätt variablerna med dina värden:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>Egenskaper för RAW-loggar

Azure CDN från Microsoft-tjänsten tillhandahåller för närvarande obehandlade loggar. Obehandlade loggar innehåller enskilda API-begäranden med varje post med följande schema: 

| Egenskap              | Beskrivning                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | Den unika referens strängen som identifierar en begäran som betjänas av en front dörr, som också skickas som X-Azure-ref-huvud till klienten. Krävs för att söka efter information i åtkomst loggarna för en speciell begäran. |
| HttpMethod            | HTTP-metod som används av begäran.                                                                                                                                                                     |
| HttpVersion           | Typ av begäran eller anslutning.                                                                                                                                                                   |
| RequestUri            | URI för den mottagna begäran.                                                                                                                                                                         |
| RequestBytes          | Storleken på HTTP Request-meddelandet i byte, inklusive begärandehuvuden och begär ande texten.                                                                                                   |
| ResponseBytes         | Byte som skickats av backend-servern som svar.                                                                                                                                                    |
| UserAgent             | Webbläsarens typ som används av klienten.                                                                                                                                                               |
| ClientIp              | IP-adressen för klienten som gjorde begäran.                                                                                                                                                  |
| TimeTaken             | Hur lång tid åtgärden tog, i millisekunder.                                                                                                                                            |
| SecurityProtocol      | TLS/SSL-protokollets version som används av begäran eller null om ingen kryptering.                                                                                                                           |
| Slutpunkt              | CDN-slutpunktens värd har kon figurer ATS under den överordnade CDN-profilen.                                                                                                                                   |
| Server dels värd namn     | Namnet på backend-värden eller ursprunget där förfrågningar skickas.                                                                                                                                |
| Skickas till ursprungs sköld </br> (föråldrad) * **Se anteckna om föråldrade nedan.** | Om värdet är true innebär det att begäran besvarades från ursprungs sköldens cacheminne i stället för Edge-pop. Ursprungs sköld är ett överordnat cacheminne som används för att förbättra förhållandet mellan cacheträffar.                                       |
| isReceivedFromClient | Om värdet är true innebär det att begäran kom från klienten. Om det här värdet är falskt, är begäran en brist i kanten (underordnad POP) och är besvarad från ursprungs skölden (överordnad POP). 
| HttpStatusCode        | HTTP-statuskod som returnerades från proxyservern.                                                                                                                                                        |
| HttpStatusDetails     | Resulterande status för begäran. Innebörd av detta sträng värde finns i en status referens tabell.                                                                                              |
| Popup                   | Edge-pop, som svarade på användarens begäran. Pop-förkortningar är flyg plats koder för deras respektive Metros.                                                                                   |
| Cache-status          | Indikerar om objektet returnerades från cachen eller kom från ursprunget.                                                                                                             |
> [!NOTE]
> Loggarna kan visas under din Log Analytics profil genom att köra en fråga. En exempel fråga skulle se ut så här:
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>Skickas till ursprungs sköldens utfasning
Den råa logg egenskapen **isSentToOriginShield** har ersatts och ersatts av ett nytt fält **isReceivedFromClient**. Använd det nya fältet om du redan använder det inaktuella fältet. 

Obehandlade loggar innehåller loggar som skapats från både CDN-Edge (underordnad POP) och ursprungs sköld. Ursprungs sköld syftar på överordnade noder som är strategiskt placerade över hela världen. Dessa noder kommunicerar med ursprungs servrar och minskar belastningen på belastningen på ursprunget. 

För varje begäran som går till ursprungs sköld finns det två logg poster:

* En för Edge-noder
* En för ursprungs sköld. 

Om du vill särskilja utgången eller svaren från Edge-noderna eller ursprungs skölden kan du använda fältet **isReceivedFromClient** för att hämta rätt data. 

Om värdet är false innebär det att begäran besvaras från ursprungs sköld till Edge-noder. Den här metoden är effektiv för att jämföra obehandlade loggar med fakturerings data. Avgifter debiteras inte för utgående från ursprungs sköld till Edge-noderna. Avgifter debiteras för utgående från Edge-noderna till klienter. 

**Kusto Query-exempel för att utesluta loggar som skapats på ursprungs sköld i Log Analytics.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> Funktionen HTTP RAW-loggar är tillgänglig automatiskt för profiler som skapats eller uppdaterats efter **25 februari 2020**. För CDN-profiler som skapats tidigare bör du uppdatera CDN-slutpunkten när du har konfigurerat loggning. Till exempel kan en navigera till geo-filtrering under CDN-slutpunkter och blockera alla länder/regioner som inte är relevanta för deras arbets belastning och tryck på Spara.


## <a name="metrics"></a>Mått
Azure CDN från Microsoft är integrerat med Azure Monitor och publicerar fyra CDN-mått för att kunna spåra, felsöka och felsöka problem. 

Måtten visas i diagram och kan nås via PowerShell, CLI och API. CDN-måtten är kostnads fria.

Azure CDN från Microsoft-mått och skickar sina mått i intervall om 60 sekunder. Måtten kan ta upp till 3 minuter innan de visas i portalen. 

Mer information finns i [Azure Monitor mått](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

**Mått som stöds av Azure CDN från Microsoft**

| Mått         | Beskrivning                                                                                                      | Dimension                                                                                   |
|-----------------|------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| Träff grad för byte * | Procent andelen utgående från CDN-cachen, beräknad mot det totala utgående värdet.                                      | Slutpunkt                                                                                    |
| RequestCount    | Antalet klient begär Anden som hanteras av CDN.                                                                     | Slutpunkt </br> Klient land. </br> Klient region. </br> HTTP-status. </br> HTTP-status grupp. |
| ResponseSize    | Antalet byte som har skickats som svar från CDN-Edge till klienter.                                                  |Slutpunkt </br> Klient land. </br> Klient region. </br> HTTP-status. </br> HTTP-status grupp.                                                                                          |
| TotalLatency    | Den totala tiden från klient förfrågan som mottagits av CDN **tills den sista svars bytet skickades från CDN till klienten**. |Slutpunkt </br> Klient land. </br> Klient region. </br> HTTP-status. </br> HTTP-status grupp.                                                                                             |

***Antal byte träff-portioner = (utgående från Edge-utgång från ursprung)/egress från gräns**

Scenarier som exkluderas i beräkningen av byte-träffar:

* Du konfigurerar uttryckligen inget cacheminne antingen via regel motor eller cachelagring av frågesträngar.
* Du konfigurerar inte Cache-Control-direktivet explicit utan lagring eller privat cache.

### <a name="metrics-configuration"></a>Mått konfiguration

1. Välj **alla resurser**på Azure Portal-menyn  >>  **\<your-CDN-profile>** .

2. Under **övervakning**väljer du **mått**:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="Lägg till diagnostisk inställning för CDN-profil." border="true":::

3. Välj **Lägg till mått**, Välj måttet som ska läggas till:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="Lägg till diagnostisk inställning för CDN-profil." border="true":::

4. Välj **Lägg till filter** för att lägga till ett filter:
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="Lägg till diagnostisk inställning för CDN-profil." border="true":::

5. Välj **Använd** delning för att se trenden efter olika dimensioner:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="Lägg till diagnostisk inställning för CDN-profil." border="true":::

6. Välj **nytt diagram** för att lägga till ett nytt diagram:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="Lägg till diagnostisk inställning för CDN-profil." border="true":::

### <a name="alerts"></a>Aviseringar

Du kan konfigurera aviseringar i Microsoft CDN genom att välja **övervaknings**  >>  **aviseringar**.

Välj **ny varnings regel** för mått som anges i avsnittet mått:

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="Lägg till diagnostisk inställning för CDN-profil." border="true":::

Aviseringen kommer att debiteras baserat på Azure Monitor. Mer information om aviseringar finns i [Azure Monitor aviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

### <a name="additional-metrics"></a>Ytterligare mått
Du kan aktivera ytterligare mått med hjälp av Azure Log Analytics och råa loggar för ytterligare kostnader.

1. Följ stegen ovan för att aktivera diagnostik för att skicka rå logg till Log Analytics.

2. Välj Log Analytics arbets ytan som du skapade:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="Lägg till diagnostisk inställning för CDN-profil." border="true":::   

3. Välj **loggar** under **Allmänt** i Log Analytics-arbetsytan.  Välj sedan **Kom igång**:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="Lägg till diagnostisk inställning för CDN-profil." border="true":::   
 
4. Välj **CDN-profiler**.  Välj en exempel fråga att köra eller Stäng exempel skärmen för att ange en anpassad fråga:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="Lägg till diagnostisk inställning för CDN-profil." border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="Lägg till diagnostisk inställning för CDN-profil." border="true":::   

4. Om du vill visa data efter diagram väljer du **diagram**.  Välj **Fäst på instrument panelen** för att fästa diagrammet på Azure-instrumentpanelen:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="Lägg till diagnostisk inställning för CDN-profil." border="true"::: 

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du aktiverat HTTP RAW-loggar för Microsoft CDN-tjänsten.

Mer information om Azure CDN och de andra Azure-tjänsterna som nämns i den här artikeln finns i:

* [Analysera](cdn-log-analysis.md) Azure CDN användnings mönster.

* Läs mer om [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* Konfigurera [Log Analytics i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
