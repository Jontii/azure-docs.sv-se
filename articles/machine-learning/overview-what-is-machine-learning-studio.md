---
title: Vad är Azure Machine Learning Studio?
description: Studio är en webb Portal för Azure Machine Learning arbets ytor. Studio kombinerar inga kod-och kod-första upplevelser för en inklusiv data vetenskaps plattform.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/24/2020
ms.openlocfilehash: 229c1d2aaab6029ccec3f8831f42a7ce8de2e31e
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555489"
---
# <a name="what-is-azure-machine-learning-studio"></a>Vad är Azure Machine Learning Studio?

I den här artikeln får du lära dig mer om Azure Machine Learning Studio, webb portalen för data expert-utvecklare i [Azure Machine Learning](overview-what-is-azure-ml.md). Studio kombinerar inga kod-och kod-första upplevelser för en inklusiv data vetenskaps plattform.

I den här artikeln får du lära dig:
>[!div class="checklist"]
> - Så här [skapar du Machine Learning-projekt](#author-machine-learning-projects) i Studio.
> - [Hantera till gångar och resurser](#manage-assets-and-resources) i Studio.
> - Skillnaderna mellan [Azure Machine Learning Studio och ml Studio (klassisk)](#ml-studio-classic-vs-azure-machine-learning-studio).


## <a name="author-machine-learning-projects"></a>Redigera Machine Learning-projekt

Studio erbjuder flera redigerings upplevelser beroende på typ av projekt och användar upplevelse.

+ **Notebook-filer**

  Skriv och kör din egen kod i hanterade [Jupyter Notebook servrar](how-to-run-jupyter-notebooks.md) som är direkt integrerade i Studio. 

+ **Azure Machine Learning Designer**

  Använd designern för att träna och distribuera Machine Learning-modeller utan att skriva någon kod. Dra och släpp data uppsättningar och moduler för att skapa ML-pipeliner. Prova [själv studie kursen om designer](tutorial-designer-automobile-price-train-score.md).

    ![Exempel på Azure Machine Learning designer](media/concept-designer/designer-drag-and-drop.gif)

+ **Automatiserat gränssnitt för Machine Learning**

  Lär dig hur du skapar [automatiserade ml-experiment](tutorial-first-experiment-automated-ml.md) med ett lättanvänt gränssnitt. 

  [![Navigerings fönstret i Azure Machine Learning Studio](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

+ **Data etiketter**

    Använd [Azure Machine Learning data etiketter](how-to-create-labeling-projects.md) för att effektivt koordinera data märknings projekt.

## <a name="manage-assets-and-resources"></a>Hantera till gångar och resurser

Hantera dina Machine Learning-tillgångar direkt i webbläsaren. Till gångar delas i samma arbets yta mellan SDK och Studio för en sömlös upplevelse. Använd Studio för att hantera:

- Modeller
- Datauppsättningar
- Datalager
- Beräknings resurser
- Notebooks
- Experiment
- Kör loggar
- Pipelines 
- Pipeline-slutpunkter

Även om du är en erfaren utvecklare kan Studio förenkla hanteringen av arbets ytans resurser.

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>ML Studio (klassisk) vs Azure Machine Learning Studio

**Ml Studio (klassisk)** släpptes i 2015 var vårt första drag-och-släpp Machine Learning Builder. Det är en fristående tjänst som bara erbjuder en visuell upplevelse. Studio (klassisk) fungerar inte med Azure Machine Learning.

**Azure Machine Learning** är en separat och modern tjänst som levererar en komplett data vetenskaps plattform. Det stöder både kod-till-och låg kod upplevelser.

**Azure Machine Learning Studio** är en webb portal *i* Azure Machine Learning som innehåller alternativ med låg kod och ingen kod för projekt redigering och till gångs hantering. 

Vi rekommenderar att nya användare väljer **Azure Machine Learning** , i stället för ml Studio (klassisk), för den senaste mängden data vetenskaps verktyg.

### <a name="feature-comparison"></a>Jämför funktioner

I följande tabell sammanfattas viktiga skillnader mellan ML Studio (klassisk) och Azure Machine Learning.

| Funktion | ML Studio (klassisk) | Azure Machine Learning |
|---| --- | --- |
| Dra och släpp gränssnitt | Klassisk miljö | Uppdaterad erfarenhet – [Azure Machine Learning designer](concept-designer.md)| 
| Kod-SDK: er | Stöd saknas | Fullständigt integrerat med [Azure Machine Learning python](/python/api/overview/azure/ml/) -och [R](tutorial-1st-r-experiment.md) SDK: er |
| Experiment | Skalbar (10 GB inlärnings data gräns) | Skala med beräknings mål |
| Inlärnings mål | Patentskyddat beräknings mål, endast CPU-stöd | Brett utbud av anpassningsbara [inlärnings mål](concept-compute-target.md#train). Innehåller stöd för GPU och CPU | 
| Beräknings mål för distribution | Eget webb tjänst format, inte anpassningsbart | Brett utbud av anpassningsbara [beräknings mål för distribution](concept-compute-target.md#deploy). Innehåller stöd för GPU och CPU |
| ML pipeline | Stöds inte | Bygg flexibla, modulära [pipelines](concept-ml-pipelines.md) för att automatisera arbets flöden |
| MLOps | Grundläggande modell hantering och distribution; Distributioner endast CPU | Enhets version (modell, data, arbets flöden), automatisering av arbets flöde, integrering med CICD-verktyg, processor-och GPU-distributioner med [mera](concept-model-management-and-deployment.md) |
| Modell format | Eget format, endast Studio (klassiskt) | Flera format som stöds beroende på utbildnings jobb typ |
| Automatisk modell inlärning och inställning för att justera dem |  Stöds inte | [Stöds](concept-automated-ml.md). Kod-första och inga kod alternativ. | 
| Data avkänning | Stöds inte | [Stöds](how-to-monitor-datasets.md) |
| Data märknings projekt | Stöds inte | [Stöds](how-to-create-labeling-projects.md) |


## <a name="next-steps"></a>Nästa steg

Besök [Studio](https://ml.azure.com)eller utforska de olika redigerings alternativen med de här självstudierna:  

- + [Kom igång i din egen utvecklings miljö](tutorial-1st-experiment-sdk-setup-local.md)
  + [Använd Jupyter notebooks på en beräknings instans för att träna & distribuera modeller](tutorial-1st-experiment-sdk-setup.md)
  + [Använd automatisk maskin inlärning för att träna & distribuera modeller](tutorial-first-experiment-automated-ml.md)  
  + [Använd designern för att träna & distribuera modeller](tutorial-designer-automobile-price-train-score.md)