---
title: Använd befintliga spelare för att spela upp innehållet – Azure | Microsoft Docs
description: Den här artikeln innehåller befintliga spelare som du kan använda för att spela upp ditt innehåll.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: bd09b734f1ac5ac3c98b6c0c717a48de19b0106f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89261095"
---
# <a name="playing-your-content-with-existing-players"></a>Spela upp ditt innehåll med befintliga spelare

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services stöder många populära strömnings format, till exempel Smooth Streaming, HTTP Live Streaming och MPEG-streck. Det här avsnittet pekar på befintliga spelare som du kan använda för att testa dina data strömmar.

### <a name="the-azure-portal-media-services-content-player"></a>Azure Portal Media Services innehålls spelare
**Azure** portal tillhandahåller en innehålls spelare som du kan använda för att testa videon.

Klicka på önskad video (se till att den har [publicerats](media-services-portal-publish.md)) och klicka på **uppspelnings** knappen längst ned i portalen.

Vissa förutsättningar gäller:

* **MEDIA SERVICES-INNEHÅLLSSPELAREN** spelar upp från strömningsslutpunkten som är standard. Använd en annan spelare om du vill spela upp från en strömningsslutpunkt som inte är standard. Till exempel [Azure Media Player](https://aka.ms/azuremediaplayer).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player

Använd [Azure Media Player](https://aka.ms/azuremediaplayer) för att spela upp innehållet (tydligt eller skyddat) i något av följande format:

* Smooth Streaming
* MPEG DASH
* HLS
* Progressiv MP4

### <a name="flash-player"></a>Flash Player

#### <a name="playready-with-token"></a>PlayReady med token

[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>STRECK motspelare

[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Övrigt
För att testa HLS-URL: er kan du också använda:

* **Safari** på en iOS-enhet eller
* **3IVX HLS Player** i Windows.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
