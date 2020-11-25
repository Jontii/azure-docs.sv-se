---
title: Felsöka problem med Azure Application Insights profiler
description: Den här artikeln innehåller fel söknings steg och information för att hjälpa utvecklare som har problem med att aktivera eller använda Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: d9acd322c454002613e21e8591c3e83aeec2d51e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995360"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Felsöka problem med att aktivera eller Visa Application Insights Profiler

> [!CAUTION]
> Det finns ett fel som kör profiler för ASP.NET Core appar på Azure App Service. Vi har en åtgärd, men det tar några veckor att distribuera World Wide. Du kan kringgå felet genom att lägga till Application Insights SDK i programmet med instruktioner [här](./asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Allmän fel sökning

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profiler laddas bara upp om det finns begär anden till programmet medan profiler körs

Azure Application Insights profiler samlar in profilerings data i två minuter per timme. Den samlar också in data när du väljer knappen **profil nu** i fönstret **Konfigurera Application Insights profiler** . Men profilerings data laddas bara upp när den kan kopplas till en begäran som skedde medan profiler kördes. 

Profiler skriver spårnings meddelanden och anpassade händelser till din Application Insights-resurs. Du kan använda dessa händelser för att se hur profiler körs. Om du tror att profiler ska köras och samla in spårningar, men de inte visas i fönstret **prestanda** , kan du kontrol lera om du vill se hur profiler körs:

1. Sök efter spårnings meddelanden och anpassade händelser som skickats av profileraren till din Application Insights-resurs. Du kan använda den här Sök strängen för att hitta relevanta data:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Följande bild visar två exempel på sökningar från två AI-resurser: 
    
   * Till vänster tar programmet inte emot förfrågningar när profiler körs. Meddelandet förklarar att uppladdningen avbröts på grund av ingen aktivitet. 

   * Till höger startade profileraren och skickade anpassade händelser när den identifierade begär Anden som uppstod när profiler kördes. Om den anpassade händelsen ServiceProfilerSample visas innebär det att profileraren har bifogat en spårning till en begäran och att du kan visa spårningen i fönstret **Application Insights prestanda** .

     Om ingen telemetri visas körs inte profiler. Fel sökning finns i avsnittet fel söknings avsnitt för din speciella app-typ senare i den här artikeln.  

     ![Sök profiler-telemetri][profiler-search-telemetry]

1. Om det fanns begär anden när profiler kördes, se till att förfrågningarna hanteras av den del av ditt program som har profiler aktiverat. Även om program ibland består av flera komponenter, aktive ras profiler endast för vissa av komponenterna. I fönstret **konfigurera Application Insights profiler** visas de komponenter som har överfört spår.

### <a name="other-things-to-check"></a>Andra saker att kontrol lera
* Kontrol lera att din app körs på .NET Framework 4,6.
* Om din webbapp är ett ASP.NET Core program måste den köra minst ASP.NET Core 2,0.
* Om de data du försöker visa är äldre än några veckor kan du försöka begränsa ditt tids filter och försöka igen. Spårningar tas bort efter sju dagar.
* Kontrol lera att proxyservrar eller en brand vägg inte har blockerat åtkomst till https://gateway.azureserviceprofiler.net .
* Profiler stöds inte på kostnads fria eller delade App Service-planer. Om du använder någon av dessa planer kan du prova att skala upp till en av de grundläggande planerna och profileraren ska börja fungera.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Dubbel inventering i parallella trådar

I vissa fall är total tids måttet i stack läsaren mer än varaktigheten för begäran.

Den här situationen kan uppstå när två eller fler trådar är associerade med en begäran och de körs parallellt. I så fall är den totala tråd tiden mer än den tid som förflutit. En tråd kan vänta på att den andra ska slutföras. Visnings programmet försöker identifiera den här situationen och utelämnar den spännande vänte tiden. När du gör det synkroniseringsfel det på sidan om att visa för mycket information i stället för att utesluta vad som kan vara viktig information.

När du ser parallella trådar i spåren ska du bestämma vilka trådar som väntar, så att du kan fastställa den kritiska sökvägen för begäran. Den tråd som snabbt övergår i vänte läge väntar vanligt vis på de andra trådarna. Koncentrera dig på de andra trådarna och ignorera tiden i väntande trådar.

### <a name="error-report-in-the-profile-viewer"></a>Fel rapport i profil visaren
Skicka in ett support ärende i portalen. Se till att ta med korrelations-ID: t från fel meddelandet.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Felsöka profileraren på Azure App Service
För att profiler ska fungera korrekt:
* Web App Service-planen måste vara Basic-nivå eller högre.
* Din webbapp måste ha Application Insights aktive rad.
* Din webbapp måste ha följande app-inställningar:

    |Programinställning    | Värde    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey för din Application Insights-resurs    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |


* Webbjobbet **ApplicationInsightsProfiler3** måste köras. Så här kontrollerar du webb jobbet:
   1. Gå till [kudu](/archive/blogs/cdndevs/the-kudu-debug-console-azure-websites-best-kept-secret).
   1. På menyn **verktyg** väljer du **instrument panel för WebJobs**.  
      Fönstret **WebJobs** öppnas. 
   
      ![Skärm bild som visar fönstret WebJobs som visar namn, status och senaste körnings tid för jobb.][profiler-webjob]   
   
   1. Om du vill visa information om webb jobbet, inklusive loggen, väljer du länken **ApplicationInsightsProfiler3** .  
     Informations fönstret för **kontinuerliga webbjobb** öppnas.

      ![Skärm bild som visar informations fönstret för kontinuerliga webbjobb.][profiler-webjob-log]

Om du inte kan ta reda på varför profiler inte fungerar för dig kan du hämta loggen och skicka den till vårt team för att få hjälp serviceprofilerhelp@microsoft.com . 
    
### <a name="manual-installation"></a>Manuell installation

När du konfigurerar profiler görs uppdateringar av webbappens inställningar. Om din miljö kräver det kan du tillämpa uppdateringarna manuellt. Ett exempel kan vara att ditt program körs i en Web Appss miljö för PowerApps. Så här installerar du uppdateringar manuellt:

1. I **kontroll fönstret webbapp** öppnar du **Inställningar**.

1. Ange **.NET Framework-version** till **v 4.6**.

1. Ställ in **Always on** **på**.
1. Skapa följande app-inställningar:

    |Programinställning    | Värde    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey för din Application Insights-resurs    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |

### <a name="too-many-active-profiling-sessions"></a>För många aktiva profilerings-sessioner

För närvarande kan du aktivera profiler på högst fyra Azure-webbappar och distributions fack som körs i samma tjänste plan. Om du har fler än fyra webbappar som körs i en app service-plan kan profileraren utlösa en *Microsoft. ServiceProfiler. Exceptions. TooManyETWSessionException*. Profiler körs separat för varje webbapp och försöker starta en ETW (Event Tracing for Windows)-session (ETW) för varje app. Men ett begränsat antal ETW-sessioner kan vara aktiva samtidigt. Om profilers-webbjobbet rapporterar för många aktiva profilerings sessioner flyttar du några webbappar till en annan tjänst plan.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Distributions fel: katalogen är inte tom: \\ Start \\ platsens \\ wwwroot \\ App_Data \\ jobb

Om du omdistribuerar din webbapp till en Web Apps-resurs där profileraren är aktive rad kan du se följande meddelande:

*Katalogen är inte tom: \\ Start \\ platsens \\ wwwroot \\ App_Data \\ jobb*

Det här felet uppstår om du kör webb distribution från skript eller från pipeline för Azure DevOps-distribution. Lösningen är att lägga till följande ytterligare distributions parametrar till webb distributions uppgiften:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Dessa parametrar tar bort mappen som används av Application Insights Profiler och avblockerar omdistributions processen. De påverkar inte den profilerade instans som körs för tillfället.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hur gör jag för att avgöra om Application Insights Profiler körs?

Profiler körs som ett kontinuerligt webb jobb i webbappen. Du kan öppna Web App-resursen i [Azure Portal](https://portal.azure.com). I fönstret **WebJobs** kontrollerar du status för **ApplicationInsightsProfiler**. Om den inte körs öppnar du **loggar** för att få mer information.

## <a name="troubleshoot-vms-and-cloud-services"></a>Felsöka virtuella datorer och Cloud Services

>**Felet i profileraren som levereras i WAD för Cloud Services har åtgärd ATS.** Den senaste versionen av WAD (1.12.2.0) för Cloud Services fungerar med alla nya versioner av App Insights SDK. Moln tjänst värdar kommer att uppgradera WAD automatiskt, men det är inte omedelbart. Om du vill framtvinga en uppgradering kan du distribuera om tjänsten eller starta om noden.

Om du vill se om profiler har kon figurer ATS korrekt av Azure-diagnostik gör du följande tre saker: 
1. Kontrol lera först om innehållet i den Azure-diagnostik konfiguration som distribueras är det du förväntar dig. 

1. För det andra kontrollerar du att Azure-diagnostik skickar rätt iKey på profilerings kommando raden. 

1. För det tredje kontrollerar du logg filen för profilering för att se om profiler kördes men returnerade ett fel. 

Kontrol lera inställningarna som användes för att konfigurera Azure-diagnostik:

1. Logga in på den virtuella datorn (VM) och öppna sedan logg filen på den här platsen. Plugin-versionen kan vara nyare på din dator.
    
    För virtuella datorer:
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```
    
    För Cloud Services:
    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```

1. I filen kan du söka efter strängen **WadCfg** för att hitta de inställningar som skickades till den virtuella datorn för att konfigurera Azure-diagnostik. Du kan kontrol lera om iKey som används av profilens Sink är korrekt.

1. Kontrol lera kommando raden som används för att starta profiler. Argumenten som används för att starta profiler finns i följande fil. (Enheten kan vara c: eller d: och katalogen kan vara dold.)

    För virtuella datorer:
    ```
    C:\ProgramData\ApplicationInsightsProfiler\config.json
    ```
    
    för Cloud Services:
    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Kontrol lera att iKey på profilens kommando rad är korrekt. 

1. Använd sökvägen som påträffades i föregående *config.jspå* filen, kontrol lera logg filen för profilering, som heter **bootstrapd. log**. Den felsöknings information som anger vilka inställningar som profiler använder visas. Den visar också status och fel meddelanden från profiler.  

    För virtuella datorer är filen vanligt vis här:
    ```
    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    För Cloud Services:
    ```
    C:\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Om profiler körs medan programmet tar emot begär Anden visas följande meddelande: *aktivitet som identifieras från iKey*. 

    När spårningen laddas upp visas följande meddelande: *börja ladda upp spårning*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Redigera nätverks proxy-eller brand Väggs regler

Om ditt program ansluter till Internet via en proxy eller en brand vägg, kan du behöva redigera reglerna för att tillåta att ditt program kommunicerar med Application Insights Profiler-tjänsten. De IP-adresser som används av Application Insights Profiler ingår i Azure Monitor Service tag.


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png
