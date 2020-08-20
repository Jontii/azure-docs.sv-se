---
title: Bygg & träna modeller
titleSuffix: Azure Machine Learning
description: Lär dig olika metoder som du kan använda för att träna modeller med Azure Machine Learning. Uppskattningar är ett enkelt sätt att arbeta med populära ramverk som Scikit – lära, TensorFlow, keras, PyTorch och kedjor. Machine Learning pipelines gör det enkelt att schemalägga obevakade körningar, använda heterogena beräknings miljöer och återanvänd delar av arbets flödet. Och kör konfigurationer ger detaljerad kontroll över de beräknings mål som inlärnings processen körs på.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 05/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: 186839425e6ab2fb5430a82650615425bb93d51a
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651768"
---
# <a name="train-models-with-azure-machine-learning"></a>Träna modeller med Azure Machine Learning

Azure Machine Learning tillhandahåller flera olika sätt att träna modeller, från att koda första lösningar med hjälp av SDK till lösningar med låg kod, till exempel automatisk maskin inlärning och den visuella designern. Använd följande lista för att avgöra vilken utbildnings metod som passar dig bäst:

+ [Azure Machine Learning SDK för python](#python-sdk): python SDK tillhandahåller flera olika sätt att träna modeller, var och en med olika funktioner.

    | Utbildnings metod | Beskrivning |
    | ----- | ----- |
    | [Kör konfiguration](#run-configuration) | Ett **allmänt sätt att träna modeller** är att använda ett utbildnings skript och köra konfiguration. Kör konfigurationen tillhandahåller den information som behövs för att konfigurera den utbildnings miljö som används för att träna din modell. Du kan ta en körnings konfiguration, ditt utbildnings skript och ett beräknings mål (inlärnings miljön) och köra ett utbildnings jobb. |
    | [Automatiserad maskininlärning](#automated-machine-learning) | Med automatisk maskin inlärning kan du **träna modeller utan omfattande data vetenskap eller programmerings kunskap**. För personer med data vetenskap och programmerings bakgrund är det ett sätt att spara tid och resurser genom att automatisera val av algoritmer och justering av den valda parametern. Du behöver inte bekymra dig om att definiera en körnings konfiguration när du använder automatisk maskin inlärning. |
    | [Kostnadsberäknare](#estimators) | Uppskattnings klasser **gör det enkelt att träna modeller baserat på populära ramverk för maskin inlärning**. Det finns uppskattnings klasser för **Scikit – lära**, **PyTorch**, **TensorFlow**, **Chainer**och **Ray RLlib**. Det finns också en generisk uppskattning som kan användas med ramverk som inte redan har en dedikerad uppskattnings klass. Du behöver inte bekymra dig om att definiera en körnings konfiguration när du använder uppskattningar. |
    | [Maskin inlärnings pipeline](#machine-learning-pipeline) | Pipelines är inte en annan utbildnings metod, men ett **sätt att definiera ett arbets flöde med modulära, återanvändbara steg**som kan innefatta utbildning som en del av arbets flödet. Maskin inlärnings pipeliner har stöd för automatisk maskin inlärning, uppskattningar och körning av konfiguration för att träna modeller. Eftersom pipelines inte fokuserar på utbildning, är orsakerna till att använda en pipeline mer varierade än andra utbildnings metoder. I allmänhet kan du använda en pipeline när:<br>* Du vill **Schemalägga obevakade processer** , t. ex. tids krävande utbildnings jobb eller förberedelse av data.<br>* Använd **flera steg** som är koordinerade över heterogena beräknings resurser och lagrings platser.<br>* Använd pipelinen som en **återanvändbar mall** för vissa scenarier, till exempel omskolning eller batch-poäng.<br>* **Spåra och version data källor, indata och utdata** för ditt arbets flöde.<br>* Ditt arbets flöde **implementeras av olika team som arbetar på vissa steg oberoende av varandra**. Steg kan sedan kopplas ihop i en pipeline för att implementera arbets flödet. |

+ [Azure Machine Learning SDK för r](#r-sdk): SDK för r använder Reticulate-paketet för att binda till Azure Machine learnings python SDK. På så sätt kan du komma åt kärn objekt och metoder som implementeras i python SDK från valfri R-miljö.

+ **Designer**: Azure Machine Learning designer (för hands version) ger dig en lätt ingångs punkt i maskin inlärning för att skapa bevis på begrepp eller för användare med lite kodnings upplevelse. Det gör att du kan träna modeller med ett webbaserat användar gränssnitt med dra och släpp. Du kan använda python-kod som en del av designen eller träna modeller utan att skriva någon kod.

+ **CLI**: Machine Learning CLI innehåller kommandon för vanliga aktiviteter med Azure Machine Learning och används ofta för **skript och automatiserade uppgifter**. När du till exempel har skapat ett utbildnings skript eller en pipeline kan du använda CLI för att starta en utbildning i ett schema eller när datafilerna som används för träningen uppdateras. För utbildnings modeller tillhandahåller den kommandon som skickar utbildnings jobb. Den kan skicka jobb med kör konfigurationer eller pipeliner.

Var och en av dessa utbildnings metoder kan använda olika typer av beräknings resurser för utbildning. De här resurserna kallas gemensamt för [__beräknings mål__](concept-azure-machine-learning-architecture.md#compute-targets). Ett beräknings mål kan vara en lokal dator eller en moln resurs, t. ex. en Azure Machine Learning Compute, Azure HDInsight eller en virtuell dator.

## <a name="python-sdk"></a>Python SDK

Med Azure Machine Learning SDK för python kan du skapa och köra Machine Learning-arbetsflöden med Azure Machine Learning. Du kan interagera med tjänsten från en interaktiv python-session, Jupyter-anteckningsböcker, Visual Studio Code eller annan IDE.

* [Vad är Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Installera/uppdatera SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Konfigurera en utvecklings miljö för Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Kör konfiguration

Ett allmänt utbildnings jobb med Azure Machine Learning kan definieras med hjälp av [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). Körnings konfigurationen används sedan tillsammans med dina utbildnings skript för att träna en modell på ett beräknings mål.

Du kan börja med en körnings konfiguration för den lokala datorn och sedan växla till en för ett moln baserat beräknings mål vid behov. När du ändrar Compute-målet ändrar du bara den körnings konfiguration som du använder. En körning loggar också information om utbildnings jobbet, till exempel indata, utdata och loggar.

* [Vad är en körnings konfiguration?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Självstudie: träna din första ML-modell](tutorial-1st-experiment-sdk-train.md)
* [Exempel: Jupyter Notebook exempel på utbildnings modeller](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [Gör så här: Konfigurera och Använd Compute-mål för modell utbildning](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Automatiserad maskininlärning

Definiera iterationer, inställningar för funktionalisering och andra inställningar. Under utbildningen försöker Azure Machine Learning olika algoritmer och parametrar parallellt. Träningen stoppas när det träffar de avslutnings villkor som du har definierat. Du behöver inte bekymra dig om att definiera en körnings konfiguration när du använder uppskattningar.

> [!TIP]
> Förutom python SDK kan du också använda automatisk ML via [Azure Machine Learning Studio](https://ml.azure.com).

* [Vad är automatiserad maskininlärning?](concept-automated-ml.md)
* [Självstudie: skapa din första klassificerings modell med automatiserad maskin inlärning](tutorial-first-experiment-automated-ml.md)
* [Självstudie: Använd automatisk maskin inlärning för att förutse taxi priser](tutorial-auto-train-models.md)
* [Exempel: Jupyter Notebook exempel för automatisk maskin inlärning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Gör så här: Konfigurera automatiserade ML-experiment i python](how-to-configure-auto-train.md)
* [Gör så här: autoträna en tids serie prognos modell](how-to-auto-train-forecast.md)
* [Gör så här: skapa, utforska och distribuera automatiserade maskin inlärnings experiment med Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>Kostnadsberäknare

Med uppskattningar är det enkelt att träna modeller med hjälp av populära ML-ramverk. Om du använder **Scikit – lära**, **PyTorch**, **TensorFlow**, **Chainer**eller **Ray RLlib** bör du överväga att använda en uppskattning för utbildning. Det finns också en generisk uppskattning som kan användas med ramverk som inte redan har en dedikerad uppskattnings klass. Du behöver inte bekymra dig om att definiera en körnings konfiguration när du använder uppskattningar.

* [Vad är uppskattningar?](concept-azure-machine-learning-architecture.md#estimators)
* [Självstudie: träna bild klassificerings modeller med MNIST data och scikit – lär dig använda Azure Machine Learning](tutorial-train-models-with-aml.md)
* [Exempel: Jupyter Notebook exempel på att använda uppskattningar](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [Gör så här: skapa uppskattningar i utbildning](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Maskin inlärnings pipeline

Maskin inlärnings pipeliner kan använda de tidigare nämnda utbildnings metoderna (köra konfiguration, uppskattningar och automatisk maskin inlärning). Pipelines är mer information om hur du skapar ett arbets flöde, så att de omfattar mer än bara inlärningen av modeller. I en pipeline kan du träna en modell med hjälp av automatiserad maskin inlärning, uppskattare eller köra konfigurationer.

* [Vad är ML-pipelines i Azure Machine Learning?](concept-ml-pipelines.md)
* [Skapa och kör maskin inlärnings pipeliner med Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Självstudie: Använd Azure Machine Learning pipelines för batch-Poäng](tutorial-pipeline-batch-scoring-classification.md)
* [Exempel: Jupyter Notebook exempel för maskin inlärnings pipelines](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Exempel: pipeline med automatiserad maskin inlärning](https://aka.ms/pl-automl)
* [Exempel: pipeline med uppskattningar](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb)

### <a name="understand-what-happens-when-you-submit-a-training-job"></a>Förstå vad som händer när du skickar ett utbildnings jobb

Azures utbildnings livs cykel består av:

1. Zippa upp filerna i projektmappen och ignorera dem som anges i _. amlignore_ eller _. gitignore_
1. Skala upp beräknings klustret 
1. Skapa eller ladda ned Dockerfile till Compute-noden 
    1. Systemet beräknar en hash av: 
        - Bas avbildningen 
        - Anpassade Docker-steg (se [distribuera en modell med en anpassad Docker-bas avbildning](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image))
        - Conda definition YAML (se [skapa & använda program varu miljöer i Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments))
    1. Systemet använder denna hash som nyckel i en sökning i arbets ytans Azure Container Registry (ACR)
    1. Om den inte hittas söker den efter en matchning i den globala ACR
    1. Om den inte hittas skapar systemet en ny avbildning (som kommer att cachelagras och registreras med arbets ytans ACR)
1. Hämta den zippade projekt filen till tillfällig lagring på Compute-noden
1. Zippa upp projekt filen
1. Compute-noden körs `python <entry script> <arguments>`
1. Spara loggar, modellvariabler och andra filer som skrivs till `./outputs` det lagrings konto som är kopplat till arbets ytan
1. Skala ned beräkning, inklusive borttagning av tillfällig lagring 

Om du väljer att träna på den lokala datorn ("Konfigurera som lokal körning") behöver du inte använda Docker. Du kan använda Docker lokalt om du väljer (se avsnittet [Konfigurera ml pipeline](https://docs.microsoft.com/azure/machine-learning/how-to-debug-pipelines#configure-ml-pipeline ) för ett exempel).

## <a name="r-sdk"></a>R SDK

Med R SDK kan du använda R-språket med Azure Machine Learning. SDK använder Reticulate-paketet för att binda till Azure Machine Learnings python SDK. Det ger dig åtkomst till kärn objekt och metoder som implementeras i python SDK från valfri R-miljö.

Mer information finns i följande artiklar:

* [Självstudie: skapa en logistik Regressions modell](tutorial-1st-r-experiment.md)
* [Azure Machine Learning SDK för R-referens](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning designer

Med designern kan du träna modeller med ett dra och släpp-gränssnitt i webbläsaren.

+ [Vad är designern?](concept-designer.md)
+ [Självstudie: förutsäga Automobile-priset](tutorial-designer-automobile-price-train-score.md)
+ [Regression: förutsägelse pris](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Klassificering: Förutsäg inkomst](how-to-designer-sample-classification-predict-income.md)
+ [Klassificering: förutse omsättning, begär och försäljning](how-to-designer-sample-classification-churn.md)
+ [Klassificering med anpassat R-skript: förutsäga flyg fördröjningar](how-to-designer-sample-classification-flight-delay.md)
+ [Text klassificering: Wikipedia SP 500-datauppsättning](how-to-designer-sample-text-classification.md)

## <a name="many-models-solution-accelerator"></a>Många modeller lösnings Accelerator

[Många modeller Solution Accelerator](https://aka.ms/many-models) (för hands version) bygger på Azure Machine Learning och gör det möjligt att träna, hantera och hantera hundratals eller till och med tusentals maskin inlärnings modeller.

Att till exempel skapa en modell __för varje instans eller individ__ i följande scenarier kan leda till förbättrade resultat:

* Förutsäga försäljning för varje enskilt lager
* Förutsägelse underhåll för hundratals olje brunnar
* Skräddarsy en upplevelse för enskilda användare.

Mer information finns i [många modeller Solution Accelerator](https://aka.ms/many-models) på GitHub.

## <a name="cli"></a>CLI

Machine Learning CLI är ett tillägg för Azure CLI. Det tillhandahåller plattforms oberoende CLI-kommandon för att arbeta med Azure Machine Learning. Normalt använder du CLI för att automatisera uppgifter, till exempel träna en maskin inlärnings modell.

* [Använd CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps på Azure](https://github.com/microsoft/MLOps)

## <a name="vs-code"></a>VS Code

Du kan använda VS Code-tillägget för att köra och hantera dina utbildnings jobb. Mer information finns i [vs Code Resource Management instruktions guide](how-to-manage-resources-vscode.md#experiments) .

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar utbildnings miljöer](how-to-set-up-training-targets.md).
