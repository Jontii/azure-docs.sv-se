---
title: 'Självstudie: batch-testning för att hitta problem – LUIS'
description: Den här självstudien visar hur du använder batch-testning för att verifiera kvaliteten på din Language Understanding-app (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2020
ms.openlocfilehash: 7dd181f8cd398dd683296b446028b398a9800b53
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91298330"
---
# <a name="tutorial-batch-test-data-sets"></a>Självstudie: data uppsättningar för batch-test

Den här självstudien visar hur du använder batch-testning för att verifiera kvaliteten på din Language Understanding-app (LUIS).

Med batch-testning kan du verifiera den aktiva, tränade modellens tillstånd med en känd uppsättning märkta yttranden och entiteter. I den JSON-formaterade kommando filen lägger du till yttranden och anger de enhets etiketter som du behöver förutsäga inuti uttryck.

Krav för batch-testning:

* Högst 1000 yttranden per test.
* Inga dubbletter.
* Tillåtna entitetstyper: endast enheter som har sparats i enheten.

När du använder en annan app än den här själv studie kursen ska du *inte* använda exemplet yttranden som redan har lagts till i din app.

**I den här guiden får du lära dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importera exempelappen
> * Skapa en batch-testfil
> * Köra ett batch-test
> * Granska test resultat

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importera exempelappen

Importera en app som tar en pizza ordning, till exempel `1 pepperoni pizza on thin crust` .

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Importera JSON till en ny app, ge appen ett namn `Pizza app` .


1. Välj **träna** i det övre högra hörnet i navigeringen för att träna appen.

## <a name="what-should-the-batch-file-utterances-include"></a>Vad ska kommando filen yttranden innehålla

Kommando filen bör innehålla yttranden med enheter på den högsta nivån som är märkta, inklusive start-och slut position. Yttranden ska inte ingå i exemplen som redan finns i appen. De bör vara yttrandena att du vill förutsäga för avsikt och entiteter positivt.

Du kan separera tester efter avsikt och/eller entitet eller ha alla test (upp till 1000 yttranden) i samma fil.

## <a name="batch-file"></a>Kommando fil

JSON-exemplet innehåller en uttryck med en etikettad entitet som illustrerar vad en testfil ser ut. I dina egna tester bör du ha många yttranden med rätt avsikts-och maskin inlärnings enhet med etiketten.

1. Skapa `pizza-with-machine-learned-entity-test.json` i en text redigerare eller [Ladda ned](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) den.

2. I den JSON-formaterade kommando filen lägger du till en uttryck med den **avsikt** som du vill förutsäga i testet.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Kör batchen

1. Välj **test** i det övre navigerings fältet.

2. Välj **panelen för batch-testning** på den högra panelen.

3. Välj **Importera data uppsättning**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av LUIS-appen med importerad data uppsättning markerat](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Välj filens fil placering `pizza-with-machine-learned-entity-test.json` .

5. Ge data uppsättningen ett namn `pizza test` och välj **färdig**.

    > [!div class="mx-imgBorder"]
    > ![Välj fil](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Klicka på knappen **Kör**.

7. Välj **se resultat**.

8. Granska resultatet i grafen och förklaringen.

## <a name="review-batch-results-for-intents"></a>Granska resultat från batch

Test resultaten visar grafiskt hur test yttranden förutsägdes mot den aktiva versionen.

I batch-diagrammet visas fyra kvadranter av resultat. Till höger om diagrammet är ett filter. Filtret innehåller avsikter och entiteter. När du väljer en [del av diagrammet](luis-concept-batch-test.md#batch-test-results) eller en punkt i diagrammet visas tillhör ande uttryck under diagrammet.

Vid hovring över diagrammet kan ett mushjul förstora eller minska visningen i diagrammet. Detta är användbart när det finns många punkter i diagrammet grupperade nära varandra.

Diagrammet är i fyra kvadranter, med två av avsnitten som visas i rött.

1. Välj **ModifyOrder** avsikt i filter listan.

    > [!div class="mx-imgBorder"]
    > ![Välj ModifyOrder avsikt från filter listan](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    Uttryck förutsägs som en **sann positiv** betydelse för uttryck som har matchat sin positiva förutsägelse i kommando filen.

    > [!div class="mx-imgBorder"]
    > ![Uttryck har matchat sin positiva förutsägelse](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    De gröna bockarna i filter listan visar också testets framgång för varje avsikt. Alla andra avsikter visas med en 1/1 positiv Poäng eftersom uttryck har testats för varje avsikt, som ett negativt test för alla syften som inte listas i batch-testet.

1. Välj **bekräftelse** avsikt. Avsikten visas inte i batch-testet, så det är ett negativt test av uttryck som anges i batch-testet.

    > [!div class="mx-imgBorder"]
    > ![Uttryck har förutsägt negativ för en listaad avsikt i kommando filen](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Det negativa testet lyckades, som det skrevs med den gröna texten i filtret och rutnätet.

## <a name="review-batch-test-results-for-entities"></a>Granska resultat för batch-test för entiteter

Entiteten ModifyOrder, som en dator entitet med underentiteter, visar om entiteten på den översta nivån är matchad och visar hur underentiteterna förutsägs.

1. Välj entiteten **ModifyOrder** i filter listan och välj sedan cirkeln i rutnätet.

1. Entitetens förutsägelse visas under diagrammet. Skärmen innehåller heldragna linjer för förutsägelser som matchar förväntad och prickade rader för förutsägelser som inte matchar förväntat.

    > [!div class="mx-imgBorder"]
    > ![Den överordnade enheten har förutsägts i kommando filen](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Hitta fel med ett batch-test

I den här självstudien visar vi hur du kör ett test och tolkar resultat. Det fick inte test filosofin eller svara på misslyckade tester.

* Se till att ta med både positiva och negativa yttranden i testet, inklusive yttranden som kan förutsägas för en annan men relaterad avsikt.
* För att yttranden ska du utföra följande aktiviteter och sedan köra testerna igen:
    * Granska aktuella exempel för avsikter och entiteter. kontrol lera att yttranden för den aktiva versionen är rätt både för avsikts-och entitets etiketter.
    * Lägg till funktioner som hjälper ditt program att förutsäga avsikter och entiteter
    * Lägg till mer positiv exempel yttranden
    * Granska saldot för exempel yttranden över avsikter

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Nästa steg

Självstudien använde ett batch-test för att validera den aktuella modellen.

> [!div class="nextstepaction"]
> [Lär dig mer om mönster](luis-tutorial-pattern.md)

