---
title: 'Snabb start: skapa en röst assistent med anpassade kommandon'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten skapar du och testar ett grundläggande anpassat kommando program med tal Studio.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 05b47a786fe845460177b66b5bd54cdb140c246e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289412"
---
# <a name="create-a-voice-assistant-using-custom-commands"></a>Skapa en röstassistent med Anpassade kommandon

I den här snabb starten skapar du och testar ett grundläggande anpassat kommando program med tal Studio. Du kommer även att ha åtkomst till det här programmet från en Windows-klient. Med **anpassade kommandon** kan du enkelt bygga avancerade röst kommando program som är optimerade för röst-och första interaktions upplevelser. Det ger en enhetlig redigerings upplevelse, en automatisk värd modell och relativt lägre komplexitet, vilket hjälper dig att fokusera på att skapa den bästa lösningen för röst kommando scenarier.

## <a name="region-availability"></a>Tillgänglighet för regioner
För tillfället stöder anpassade kommandon tal prenumerationer som skapats i följande regioner:
* USA, västra
* USA, västra 2
* East US
* USA, östra 2
* Norra Europa
* Europa, västra
* USA, västra centrala
* Indien, centrala
* Asien, östra
* Sydostasien

## <a name="prerequisites"></a>Förutsättningar

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Skapa en Azure tal-resurs i en region som stöder anpassade kommandon.<span class="docon docon-navigate-external x-hidden-focus"></span></a> Se avsnittet **region tillgänglighet** ovan för att visa en lista över regioner som stöds.
> * Ladda ned exempel filen [Smart Room lite](https://aka.ms/speech/cc-quickstart) JSON.
> * Ladda ned den senaste versionen av [Windows Voice Assistant-klienten](https://aka.ms/speech/va-samples-wvac).

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Gå till tal Studio för anpassade kommandon

1. I en webbläsare går du till [tal Studio](https://speech.microsoft.com/).
1. Ange dina autentiseringsuppgifter och logga in på portalen.

   Standardvyn är listan med tal prenumerationer.
   > [!NOTE]
   > Om du inte ser sidan Välj prenumeration kan du gå dit genom att välja "tal resurser" på menyn Inställningar i det översta fältet.

1. Välj din tal prenumeration och välj sedan **gå till Studio**.
1. Välj **anpassade kommandon**.

   Standardvyn är en lista över de anpassade kommandon som du har i den valda prenumerationen.

## <a name="import-an-existing-application-as-a-new-custom-commands-project"></a>Importera ett befintligt program som ett nytt anpassat kommandon-projekt

1. Välj **nytt projekt** för att skapa ett projekt.

1. I rutan **namn** anger du projekt namnet som `Smart-Room-Lite` (eller något annat alternativ).
1. I listan **språk** väljer du **engelska (USA)**.
1. Välj **Bläddra bland filer** och välj filen **SmartRoomLite.js** i fönstret Bläddra.

    > [!div class="mx-imgBorder"]
    > ![Skapa ett projekt](media/custom-commands/import-project.png)

1.  Välj en redigerings resurs i listan **Luis Authoring Resource** . Om det inte finns några giltiga redigerings resurser skapar du en genom att välja  **Skapa ny Luis Authoring-resurs**.

    > [!div class="mx-imgBorder"]
    > ![Skapa en resurs](media/custom-commands/create-new-luis-resource.png)
    
    
    1. Ange namnet på resursen i rutan **resurs namn** .
    1. Välj en resurs grupp i listan **resurs grupp** .
    1. Välj en plats i listan **plats** .
    1. Välj en nivå i listan **pris nivå** .
    
    
    > [!NOTE]
    > Du kan skapa resurs grupper genom att ange önskat resurs grupp namn i fältet resurs grupp. Resurs gruppen skapas när du väljer **skapa** .


1. Välj sedan **skapa** för att skapa projektet.
1. När projektet har skapats väljer du ditt projekt.
Nu bör du se Översikt över dina nya anpassade kommandon.

## <a name="try-out-some-voice-commands"></a>Prova några röst kommandon
1. Välj **träna** överst i den högra rutan.
1. När inlärningen är klar väljer du **test** och provar följande yttranden:
    - Aktivera TV
    - Ange temperatur till 80 grader
    - Stäng av den
    - TV
    - Ange ett larm för 5 PM

## <a name="integrate-custom-commands-application-in-an-assistant"></a>Integrera program för anpassade kommandon i en assistent
Innan du kan komma åt det här programmet från externa tal Studio måste du publicera programmet. För att publicera ett program måste du konfigurera en förutsägelse LUIS-resurs.  

### <a name="update-prediction-luis-resource"></a>Uppdatera förutsägelse LUIS-resurs


1. Välj **Inställningar** i den vänstra rutan och välj  **Luis-resurser** i den mellersta rutan.
1. Välj en förutsägelse resurs eller skapa en genom att välja **Skapa ny resurs**.
1. Välj **Spara**.
    
    > [!div class="mx-imgBorder"]
    > ![Ange LUIS-resurser](media/custom-commands/set-luis-resources.png)

> [!NOTE]
> Eftersom redigerings resursen endast stöder 1 000 förutsägelse slut punkts begär Anden per månad måste du mandatorily ange en LUIS förutsägelse resurs innan du publicerar dina anpassade kommandon.

### <a name="publish-the-application"></a>Publicera programmet

Välj  **publicera** ovanpå den högra rutan. När publiceringen är klar visas ett nytt fönster. Anteckna värdet för **program-ID** och **tal resurs nyckel** från det. Du behöver dessa två värden för att kunna komma åt programmet från externa tal Studio.

Du kan också hämta dessa värden genom att välja allmänna **Inställningar**  >  **General** .

### <a name="access-application-from-client"></a>Kom åt program från klienten

I den här artikeln kommer vi att använda Windows Voice Assistant-klienten som du laddade ned som en del av kraven. Packa upp mappen.
1. Starta **VoiceAssistantClient.exe**.
1. Skapa en ny publicerings profil och ange ett värde för **anslutnings profilen**. I avsnittet **allmänna inställningar** anger du **prenumerations nyckel** för värden (detta är samma som det **tal resurs nyckel** värde som du sparade vid publicering av programmet), **prenumerations nyckel region** och **anpassade kommandon app-ID**.
    > [!div class="mx-imgBorder"]
    > ![Skapa profil för WVAC](media/custom-commands/create-profile.png)
1. Välj **Spara och Använd profil**.
1. Prova nu följande indata via tal/text
    > [!div class="mx-imgBorder"]
    > ![Skapa profil för WVAC](media/custom-commands/conversation.png)


> [!TIP]
> Du kan klicka på poster i **aktivitets loggen** för att kontrol lera de råa svar som skickas från tjänsten för anpassade kommandon.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du använt ett befintligt program. I avsnittet [instruktions avsnitt](how-to-custom-commands-create-application-with-simple-commands.md)får du lära dig att utforma, utveckla, felsöka, testa och integrera ett anpassat kommando program från grunden.
