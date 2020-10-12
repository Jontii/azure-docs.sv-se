---
title: 'Poäng modell: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Poäng modell i Azure Machine Learning för att generera förutsägelser med hjälp av en tränad klassificerings-eller Regressions modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 0cc367b86507d6d819608795851052e8a0c1c73b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90890311"
---
# <a name="score-model"></a>Poängmodell

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att generera förutsägelser med hjälp av en utbildad klassificerings-eller Regressions modell.

## <a name="how-to-use"></a>Använd så här

1. Lägg till modulen **Poäng modell** i din pipeline.

2. Koppla en utbildad modell och en data uppsättning som innehåller nya indata. 

    Data ska vara i ett format som är kompatibelt med den typ av tränad modell som du använder. Schemat för data uppsättningen för indata ska också ofta matcha schemat för de data som används för att träna modellen.

3. Skicka pipelinen.

## <a name="results"></a>Resultat

När du har genererat en uppsättning poäng med [Poäng modell](./score-model.md):

+ Om du vill generera en uppsättning mått som används för att utvärdera modellens precision (prestanda) kan du ansluta den returnerade data uppsättningen för att [utvärdera modellen](./evaluate-model.md), 
+ Högerklicka på modulen och välj **visualisera** för att se ett exempel på resultatet.
<!-- + To Save the results to a dataset. -->

Poängen eller det förväntade värdet kan ha många olika format, beroende på modell och indata:

- För klassificerings modeller utvärderar [Poäng modellen](./score-model.md) ett förutsägande värde för klassen, samt sannolikheten för det förväntade värdet.
- För Regressions modeller genererar [Poäng modellen](./score-model.md) bara det förväntade numeriska värdet.


## <a name="publish-scores-as-a-web-service"></a>Publicera resultat som en webb tjänst

En vanlig användning av poängsättning är att returnera utdata som en del av en förutsägbar webb tjänst. Mer information finns i [den här självstudien](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) om hur du distribuerar en slut punkt i real tid baserat på en pipeline i Azure Machine Learning designer.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 