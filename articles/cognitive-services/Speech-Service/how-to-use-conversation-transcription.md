---
title: Snabb start för konversations avskrift i real tid – tal tjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder konversations avskrift i real tid med talet SDK. Med konversations avskrift kan du skriva över möten och andra konversationer med möjlighet att lägga till, ta bort och identifiera flera deltagare genom att strömma ljud till tal tjänsten.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: trbye
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 6cb338e8b7baa45e1c84f59a5730a9a500e71a79
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486809"
---
# <a name="get-started-with-real-time-conversation-transcription"></a>Kom igång med konversations avskrift i real tid

Talet SDK: s **ConversationTranscriber** -API gör att du kan skriva över möten och andra konversationer med möjlighet att lägga till, ta bort och identifiera flera deltagare genom att strömma ljud till tal tjänsten med hjälp av `PullStream` eller `PushStream` . Först skapar du röst under skrifter för varje deltagare med hjälp av REST API och använder sedan röst under skrifter med SDK för att skriva över konversationer. Mer information finns i [Översikt över](conversation-transcription.md) konversations avskrift.

## <a name="limitations"></a>Begränsningar

* Endast tillgängligt i följande prenumerations regioner: `centralus` , `eastasia` , `eastus` , `westeurope`
* Kräver en 7-MIC cirkulär multi-Microphone-matris med en uppspelnings referens ström. Mikrofonens matris ska uppfylla [vår specifikation](https://aka.ms/sdsdk-microphone).
* [Tal enheternas SDK](speech-devices-sdk.md) tillhandahåller lämpliga enheter och en exempel-app som demonstrerar konversations avskrift.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](overview.md#try-the-speech-service-for-free).

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/conversation-transcription/real-time-javascript.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/conversation-transcription/real-time-csharp.md)]
::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> Avskrift av asynkron [konversation](how-to-async-conversation-transcription.md) 
>  [Exempel kod](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java) 
>  för ROOBO-enhet [Exempel kod för Azure Kinect dev kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)
