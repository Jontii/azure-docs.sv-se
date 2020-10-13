---
title: Video analys i real tid för IoT Edge viktig information – Azure
description: Det här avsnittet innehåller viktig information om real tids analys av IoT Edge-versioner, förbättringar, fel korrigeringar och kända problem.
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: e33a62891f9503a4f2ff907585316c3737c876e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250478"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Viktig information om live video analys i IoT Edge

>Bli informerad om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in den här URL: en `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` i din RSS-feed läsare.

Den här artikeln innehåller information om:

* De senaste versionerna
* Kända problem
* Felkorrigeringar
* Föråldrade funktioner

<hr width=100%>

## <a name="september-22-2020"></a>Den 22 september 2020

Den här versions tag gen för uppdateringen september 2020 för modulen är:

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> I snabb starterna och självstudierna använder distributions manifestet en tagg på 1 (Real-Video-Analytics: 1). Det innebär att bara att omdistribuera sådana manifest bör uppdatera modulen på din gräns > enheter.

### <a name="module-updates"></a>Uppdatera modul

* En ny [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md) -nod, som är tillgänglig för integrering med modulen för [spatial analys](https://docs.microsoft.com/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview)(för hands version) från Cognitive Services.
* Stöd har lagts till för Linux ARM64-enheter – Använd [manuella steg](deploy-iot-edge-device.md) för att distribuera till sådana enheter.

### <a name="documentation-updates"></a>Dokumentationsuppdateringar

* Det finns [instruktioner](deploy-azure-stack-edge-how-to.md) för att använda real tids analys på IoT Edge på Azure Stack gräns enheter.
* Nya självstudier om hur du utvecklar scenarion med en speciell modell för visuellt innehåll som använder [Custom vision-tjänsten](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) och hur du [analyserar direktsänd video](custom-vision-tutorial.md) i real tid.

<hr width=100%>

## <a name="august-19-2020"></a>19 augusti 2020

Den här versions tag gen för uppdateringen augusti 2020 för modulen är:

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> I snabb starterna och självstudierna använder distributions manifestet en tagg på 1 (Real-Video-Analytics: 1). Det innebär att bara att omdistribuera sådana manifest bör uppdatera modulen på din gräns > enheter.

### <a name="module-updates"></a>Uppdatera modul

* Du kan nu få hög data överförings prestanda mellan direktsända video analyser på IoT Edge och ditt anpassade tillägg med gRPC Framework. Se [det här](analyze-live-video-use-your-grpc-model-quickstart.md) för att komma igång.
* En bredare regional distribution av Real video analys och endast moln tjänsten har uppdaterats.  
* Live Video Analytics är nu tillgängligt i 25 ytterligare regioner över hela världen. Här är [listan](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) över alla tillgängliga regioner.  
* [Inställningarna](https://aka.ms/lva-edge/setup-resources-for-samples) för snabb start har också uppdaterats med stöd för nya regioner.
    * Det finns inget anrop till åtgärden för alla som redan har konfigurerat resurser

### <a name="bug-fixes"></a>Felkorrigeringar 

* Ta bort användningen av ett föråldrat Azure-tillägg i konfigurations skriptet

<hr width=100%>

## <a name="july-13-2020"></a>13 juli 2020

Den här versions tag gen för uppdateringen från juli 2020 för modulen är:

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> I snabb starterna och självstudierna använder distributions manifestet en tagg på 1 (Real-Video-Analytics: 1). Det innebär att bara att omdistribuera sådana manifest bör uppdatera modulen på din gräns > enheter.

### <a name="module-updates"></a>Uppdatera modul

* Nu kan du skapa diagram-topologier som har en nod för till gångs mottagare och en noden filmottagare intill en nod i en signal grind processor. Se [det här](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) för ett exempel.

### <a name="bug-fixes"></a>Felkorrigeringar

* Förbättringar av validering av önskade egenskaper

<hr width=100%>

## <a name="june-1-2020"></a>Den 1 juni 2020

Den här versionen är den första offentliga för hands versionen av video analys på IoT Edge. Versions tag gen är

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Funktioner som stöds

* Analysera direktuppspelade video strömmar med AI-moduler som du väljer och spela in video på gräns enheten eller i molnet
* Använd på Linux AMD64-operativ system som [stöds](../../iot-edge/support.md) av IoT Edge
* Distribuera och konfigurera modulen via IoT Hub med Azure Portal eller Visual Studio Code
* Hantera [diagram-topologier och diagram instanser](media-graph-concept.md#media-graph-topologies-and-instances) via fjärr anslutning eller lokalt genom följande direkta metod anrop

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList

## <a name="next-steps"></a>Nästa steg

[Översikt](overview.md)
