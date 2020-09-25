---
title: Analysera direktsänd video med din egen HTTP-modell – Azure
description: I den här snabb starten ska du använda dator vision för att analysera direktsänd video från en (simulerad) IP-kamera genom att använda din egen HTTP-modell.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 07661fb1e9496aacff6f108a840e0c357f068d41
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336470"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-http-model"></a>Snabb start: analysera direktsänd video med din egen HTTP-modell

Den här snabb starten visar hur du använder video analys i real tid för IoT Edge för att analysera ett direktuppspelat video flöde från en (simulerad) IP-kamera. Du får se hur du använder en modell för dator vision för att identifiera objekt. En delmängd av bild rutorna i direktsänd video-feed skickas till en tjänst för drift störningar. Resultaten skickas till IoT Edge Hub. 

Den här snabb starten använder en virtuell Azure-dator som en IoT Edge enhet och använder en simulerad direktuppspelad video ström. Den baseras på exempel kod som skrivits i C# och bygger på snabb starten för att [identifiera rörelse och generera händelser](detect-motion-emit-events-quickstart.md) . 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Förutsättningar

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Granska exempel videon

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Översikt

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>Skapa och distribuera medie diagrammet

::: zone pivot="programming-language-csharp"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Tolka resultaten

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

Om du tänker prova andra snabb starter behåller du de resurser som du har skapat. Annars går du till Azure Portal, går till dina resurs grupper, väljer den resurs grupp där du körde den här snabb starten och tar bort alla resurser.

## <a name="next-steps"></a>Nästa steg

* Prova en [säker version av YoloV3-modellen](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/tls-yolov3-onnx/readme.md) och distribuera den till den IoT Edge enheten. 

Granska ytterligare utmaningar för avancerade användare:

* Använd en [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) som har stöd för RTSP i stället för att använda RTSP-simulatorn. Du kan söka efter IP-kameror som stöder RTSP på sidan [ONVIF](https://www.onvif.org/conformant-products/) -produkter. Sök efter enheter som uppfyller profilerna G, S eller T.
* Använd en AMD64-eller x64 Linux-enhet i stället för en virtuell Azure Linux-dator. Enheten måste finnas i samma nätverk som IP-kameran. Du kan följa anvisningarna i [installera Azure IoT Edge runtime på Linux](../../iot-edge/how-to-install-iot-edge-linux.md). Registrera sedan enheten med Azure IoT Hub genom att följa anvisningarna i [distribuera din första IoT Edge-modul till en virtuell Linux-enhet](../../iot-edge/quickstart-linux.md).
