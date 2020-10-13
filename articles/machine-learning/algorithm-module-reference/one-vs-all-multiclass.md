---
title: En-mot-alla – multiklass
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen En-mot-alla – multiklass i Azure Machine Learning för att skapa en klassificerings modell med multiklasser från en ensemble av binära klassificerings modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: dfe01e16b55325db03e4150a33ae5c4aa5822ae2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898505"
---
# <a name="one-vs-all-multiclass"></a>En-mot-alla – multiklass

I den här artikeln beskrivs hur du använder modulen En-mot-alla – multiklass i Azure Machine Learning designer. Målet är att skapa en klassificerings modell som kan förutsäga flera klasser med hjälp av metoden *One-och-all* .

Den här modulen är användbar för att skapa modeller som förutsäger tre eller fler möjliga resultat, när resultatet beror på kontinuerliga eller kategoriska för förutsägande variabler. Med den här metoden kan du också använda binära klassificerings metoder för problem som kräver flera utgående klasser.

### <a name="more-about-one-versus-all-models"></a>Mer om en-och-alla-modeller

Vissa klassificerings algoritmer tillåter användning av fler än två klasser efter design. Andra begränsar möjliga resultat till ett av två värden (en binär eller en modell med två klasser). Men även binära klassificerings algoritmer kan anpassas för klassificerings uppgifter med flera klasser via en mängd olika strategier. 

Den här modulen implementerar metoden One-och-all, där en binär modell skapas för var och en av de olika utgående klasserna. Modulen utvärderar var och en av dessa binära modeller för de enskilda klasserna mot komplementet (alla andra klasser i modellen) som om det är ett binärt klassificerings problem. Modulen utför sedan förutsägelse genom att köra dessa binära klassificerare och välja förutsägelse med högsta möjliga resultat.  

I grunden skapar modulen en ensemble med enskilda modeller och sammanfogar sedan resultaten för att skapa en enskild modell som förutsäger alla klasser. En binär klassificerare kan användas som grund för en modell med en-och-alla.  

Anta till exempel att du konfigurerar en [dubbelriktad Vector Machine-](two-class-support-vector-machine.md) modell och anger den som inmatad i en-mot-alla – multiklass-modulen. Modulen skapar dubbelriktade stöd för vektor dator modeller för alla medlemmar i klassen output. Den använder sedan metoden One-respektive-all för att kombinera resultaten för alla klasser.  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Så här konfigurerar du En-mot-alla – multiklass klassificeraren  

Den här modulen skapar en ensemble med binära klassificerings modeller för att analysera flera klasser. Om du vill använda den här modulen måste du först konfigurera och träna en *binär klassificerings* modell. 

Du ansluter den binära modellen till En-mot-alla – multiklass-modulen. Sedan tränas ensemblen för modeller med hjälp av [träna modell](train-model.md) med en etikettad inlärnings data uppsättning.

När du kombinerar modeller skapar En-mot-alla – multiklass flera binära klassificerings modeller, optimerar algoritmen för varje klass och sammanfogar sedan modellerna. Modulen utför dessa uppgifter även om träning-datauppsättningen kan ha flera klass värden.

1. Lägg till En-mot-alla – multiklass-modulen i din pipeline i designern. Du hittar den här modulen under **Machine Learning-Initialize**i **klassificerings** kategorin.

   En-mot-alla – multiklass klassificeraren har inga parametrar som kan konfigureras. Alla anpassningar måste göras i den binära klassificerings modellen som anges som indata.

2. Lägg till en binär klassificerings modell i pipelinen och konfigurera modellen. Du kan till exempel använda [två klass stöd för en Vector-dator](two-class-support-vector-machine.md) eller ett [utökat besluts träd med två klasser](two-class-boosted-decision-tree.md).

3. Lägg till modulen [träna modell](train-model.md) i din pipeline. Anslut den intränade klassificeraren som är utdata från En-mot-alla – multiklass.

4. På den andra ingången av [träna modell](train-model.md)ansluter du en etikettad tränings uppsättning som har flera klass värden.

5. Skicka pipelinen.

## <a name="results"></a>Resultat

När utbildningen är klar kan du använda modellen för att göra förutsägelser i multiklass.

Alternativt kan du skicka den ej tränade klassificeraren till [kors](cross-validate-model.md) validerings modellen för kors validering mot en etikettad verifierings data uppsättning.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
