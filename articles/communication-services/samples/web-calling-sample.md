---
title: Azure Communication Services – webb anrops exempel
titleSuffix: An Azure Communication Services sample
description: Lär dig mer om kommunikations tjänsternas webb anrops exempel
author: chriswhilar
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e6fc3441fac5fe037e9a268d26012761d1fece70
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92463281"
---
# <a name="get-started-with-the-web-calling-sample"></a>Kom igång med webb anrops exemplet

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

> [!IMPORTANT]
> [Det här exemplet finns på GitHub.](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/).

Azure Communication Services **webb anrops exempel** visar hur kommunikations tjänsterna som anropar klient biblioteket kan användas för att bygga en anrops upplevelse med Java Script.

I den här snabb starten ska vi lära dig hur exemplet fungerar innan vi kör exemplet på den lokala datorn. Vi distribuerar sedan exemplet till Azure med dina egna Azure Communication Services-resurser.

## <a name="overview"></a>Översikt

Webb anrops exemplet är ett webb program som fungerar som en stegvis genom gång av de olika funktionerna som tillhandahålls av kommunikations tjänstens webb anrops klient bibliotek. 

Det här exemplet har skapats för utvecklare och gör det enkelt för dig att komma igång med kommunikations tjänster. Dess användar gränssnitt är indelat i flera avsnitt, med en knapp för att Visa kod som gör att du kan kopiera kod direkt från webbläsaren till ditt eget kommunikations tjänst program.

När [webb anrops exemplet](https://github.com/Azure-Samples/communication-services-web-calling-tutorial) körs på datorn visas följande landnings sida:

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="Vägledning för webb samtal 1" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="Vägledning för webb samtal 1" lightbox="./media/web-calling-tutorial-page-2.png":::


## <a name="user-provisioning-and-sdk-initialization"></a>Användar etablering och SDK-initiering 

Börja använda demonstrationen genom att ange anslutnings strängen från [resursen kommunikations tjänster](../quickstarts/create-communication-resource.md) till `config.json` . Detta används för att etablera en [token för användar åtkomst](../concepts/authentication.md) så att din anropande SDK kan initieras.

Ange ditt eget personliga ID i användar identitetens indata. Om inget anges här skapas en slumpmässig användar identitet. 

Klicka på "etableringen user and Initialize SDK" för att initiera din SDK med hjälp av en token som tillhandahålls av backend-token för etablerings tjänsten. Den här server dels tjänsten är i `/project/webpack.config.js` .

Klicka på knappen "Visa kod" om du vill se exempel koden som du kan använda i din egen lösning.

Du bör se följande när SDK: n har initierats:

:::image type="content" source="./media/user-provisioning.png" alt-text="Vägledning för webb samtal 1" lightbox="./media/user-provisioning.png":::

Du är nu redo att börja placera samtal med kommunikations tjänst resursen!

## <a name="placing-and-receiving-calls"></a>Placera och ta emot samtal

I kommunikations tjänsterna Web Call SDK kan du använda **1:1**, **1: N**och **grupp** anrop.

För 1:1 eller 1: N utgående anrop kan du ange flera användar identiteter för kommunikations tjänster som anropar med kommaavgränsade värden. Du kan också ange traditionella (PSTN) telefonnummer som ska anropas med kommaavgränsade värden. 

När du anropar PSTN-telefonnummer anger du ditt alternativa ID för anroparen. Klicka på knappen "placera samtal" för att placera ett utgående samtal:

:::image type="content" source="./media/place-a-call.png" alt-text="Vägledning för webb samtal 1" lightbox="./media/place-a-call.png":::

Om du vill ansluta till ett grupp anrop anger du det GUID som identifierar anropet och klickar på knappen Anslut till grupp:

:::image type="content" source="./media/join-a-group-call.png" alt-text="Vägledning för webb samtal 1" lightbox="./media/join-a-group-call.png":::

Klicka på knappen "Visa kod" om du vill se exempel koden för att placera samtal, ta emot samtal och ansluta till grupp anrop.

Ett aktivt anrop ser ut så här:

:::image type="content" source="./media/group-call.png" alt-text="Vägledning för webb samtal 1" lightbox="./media/group-call.png":::

Det här exemplet innehåller också kodfragment för följande funktioner:

  - Klicka på video ikonen för att aktivera/inaktivera video kameran
  - Klicka på mikrofon ikonen för att aktivera/inaktivera mikrofonen
  - Klicka på uppspelnings ikonen för att hålla/stoppa samtalet
  - Klicka på skärm ikonen för att starta/stoppa dela skärmen
  - Klicka på person ikonen för att lägga till en deltagare i samtalet
  - Klicka på "ta bort deltagare" i deltagarens lista för att ta bort en viss deltagare från anropet


## <a name="next-steps"></a>Nästa steg

>[!div class="nextstepaction"] 
>[Hämta exemplet från GitHub](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

Mer information finns i följande artiklar:

- Bekanta dig med [att använda det anropande klient biblioteket](../quickstarts/voice-video-calling/calling-client-samples.md)
- Läs mer om [hur du anropar Works](../concepts/voice-video-calling/about-call-types.md)
- Granska [API Reference-dokument](https://docs.microsoft.com/javascript/api/azure-communication-services/@azure/communication-calling/?view=azure-communication-services-js)

## <a name="additional-reading"></a>Mer att läsa

- [Azure Communication-GitHub](https://github.com/Azure/communication) – Hitta fler exempel och information på den officiella GitHub-Sidan
- [Redux](https://redux.js.org/) – tillstånds hantering på klient Sidan
- [FluentUI](https://aka.ms/fluent-ui) – Microsoft Powered UI-bibliotek
- [Reagera](https://reactjs.org/) – bibliotek för att skapa användar gränssnitt
- [ASP.net cores](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) ramverk för att skapa webb program
