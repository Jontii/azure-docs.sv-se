---
title: Vad är Azure Machine Learning
description: Översikt över Azure Machine Learning – en integrerad lösning från slut punkt till slut punkt för data vetenskaps tekniker som utvecklar, experimenterar och distribuerar avancerade analys program i moln skala.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.custom: devx-track-python
ms.openlocfilehash: 53ccd610c6f0f36d4ab04efbada2070a22975023
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650119"
---
# <a name="what-is-azure-machine-learning"></a>Vad är Azure Machine Learning?

I den här artikeln får du lära dig mer om Azure Machine Learning, en molnbaserad miljö som du kan använda för att träna, distribuera, automatisera, hantera och spåra ML-modeller. 

Azure Machine Learning kan användas för alla typer av Machine Learning, från klassisk ml till djup inlärning, övervakad och oövervakad inlärning. Oavsett om du föredrar att skriva python-eller R-kod eller koder med lägre kod, till exempel [designern](tutorial-designer-automobile-price-train-score.md), kan du skapa, träna och spåra mycket exakta maskin inlärnings-och djup inlärnings modeller i en Azure Machine Learning-arbetsyta. 

Starta utbildningen på den lokala datorn och skala sedan ut till molnet. 

Tjänsten samverkar också med populära djup inlärnings-och förstärknings verktyg med öppen källkod, till exempel PyTorch, TensorFlow, scikit – lära och Ray RLlib. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Kostnads fri utvärderings version!**  Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag. Du får krediter som du kan använda för att köpa Azure-tjänster. När de är slut kan du behålla kontot och använda [kostnadsfria Azure-tjänster](https://azure.microsoft.com/free/). Ditt kreditkort debiteras aldrig om du inte specifikt ändrar dina inställningar och ber om debitering.


## <a name="what-is-machine-learning"></a>Vad är maskininlärning?

Maskininlärning är en datavetenskapsteknik som gör att datorer kan använda befintliga data för att göra prognoser om framtida beteenden, resultat och trender. Genom att använda maskininlärning kan datorer lära sig utan att vara explicit programmerade.

Prognoser eller förutsägelser från maskininlärning kan göra appar och enheter smartare. När du handlar på nätet kan maskininlärning till exempel användas för att rekommendera andra produkter som du kanske gillar baserat på vad du har köpt. När ditt kreditkort används så kontrollerar maskininlärningen transaktionen mot en databas med transaktioner vilket hjälper till att upptäcka bedrägerier. När din robotdammsugare dammsuger ett rum tar den hjälp av maskininlärning för att avgöra om jobbet är klart.

## <a name="machine-learning-tools-to-fit-each-task"></a>Maskin inlärnings verktyg för att passa varje uppgift 

Azure Machine Learning innehåller alla verktyg och data forskare som behöver för sina Machine Learning-arbetsflöden, inklusive:
+ [Azure Machine Learning designer](tutorial-designer-automobile-price-train-score.md) (för hands version): dra-n-släpp-moduler för att bygga experimenten och sedan distribuera pipeliner.

+ Jupyter Notebook: Använd våra [exempel Notebook-anteckningsböcker](https://github.com/Azure/MachineLearningNotebooks) eller skapa egna antecknings böcker för att utnyttja våra SDK: er <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">för python</a> -exempel för din maskin inlärning. 

+ R-skript eller antecknings böcker där du använder <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK för R</a> för att skriva din egen kod eller använda R-modulerna i designern.

+ + [Många modeller Solution Accelerator](https://aka.ms/many-models) (för hands version) bygger på Azure Machine Learning och gör det möjligt att träna, hantera och hantera hundratals eller till och med tusentals maskin inlärnings modeller.

+ [Visual Studio Code-tillägg](tutorial-setup-vscode-extension.md)

+ [Maskininlärning CLI](reference-azure-machine-learning-cli.md)

+ Ramverk med öppen källkod, till exempel PyTorch, TensorFlow och scikit – lära och många fler

+ [Förstärka inlärning](how-to-use-reinforcement-learning.md) med Ray RLlib

Du kan även använda [MLflow för att spåra mått och distribuera modeller](how-to-use-mlflow.md) eller Kubeflow för att [bygga arbets flöden från slut punkt till slut punkt](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-in-python-or-r"></a>Bygg ML-modeller i python eller R

Starta utbildningen på den lokala datorn med hjälp av Azure Machine Learning <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">python SDK</a> eller <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK</a>. Sedan kan du skala ut till molnet. 

Tack vare de många tillgängliga [beräkningsmålen](how-to-set-up-training-targets.md), däribland Azure Machine Learning Compute och [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), och med [tjänster för finjustering av avancerade hyperparametrar](how-to-tune-hyperparameters.md) kan du skapa bättre modeller snabbare genom att använda hela styrkan från molnet.

Du kan också [Automatisera modell träning och-justering](tutorial-auto-train-models.md) med hjälp av SDK.

## <a name="build-ml-models-with-no-code-tools"></a>Bygg ML-modeller med verktyg utan kod

För kod kostnads fri eller låg kod utbildning och distribution, försök:

+ **Azure Machine Learning designer (för hands version)**

  Använd designern för att prepare data, träna, testa, distribuera, hantera och spåra maskin inlärnings modeller utan att skriva någon kod. Det krävs ingen programmering, du ansluter visuellt data uppsättningar och moduler visuellt för att skapa din modell. Prova [själv studie kursen om designer](tutorial-designer-automobile-price-train-score.md).

  Läs mer i [artikeln Översikt över Azure Machine Learning designer](concept-designer.md). 

  ![Exempel på Azure Machine Learning designer](./media/overview-what-is-azure-ml/designer-drag-and-drop.gif)

+ **Automatiserat gränssnitt för Machine Learning**

  Lär dig hur du skapar [automatiserade ml-experiment](tutorial-first-experiment-automated-ml.md) i det lättanvända gränssnittet. 

  [![Navigerings fönstret i Azure Machine Learning Studio](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

## <a name="mlops-deploy--lifecycle-management"></a>MLOps: Distribuera & livs cykel hantering
När du har rätt modell kan du enkelt använda den i en webb tjänst, på en IoT-enhet eller från Power BI. Mer information finns i artikeln om [hur och var man distribuerar](how-to-deploy-and-where.md).

Sedan kan du hantera dina distribuerade modeller med hjälp av [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py), [Azure Machine Learning Studio](https://ml.azure.com)eller [Machine Learning CLI](reference-azure-machine-learning-cli.md).

Dessa modeller kan förbrukas och returnera förutsägelser i [real tid](how-to-consume-web-service.md) eller [asynkront](how-to-use-parallel-run-step.md) på stora mängder data.

Och med avancerade [maskin inlärnings pipeliner](concept-ml-pipelines.md)kan du samar beta med varje steg från förberedelse av data, modell utbildning och utvärdering, via distribution. Med pipelines kan du:

* Automatisera dator inlärnings processen från slut punkt till slut punkt i molnet
* Återanvänd komponenter och kör bara stegen igen när det behövs
* Använd olika beräknings resurser i varje steg
* Köra batch-bedömnings uppgifter

Om du vill använda skript för att automatisera arbets flödet för Machine Learning innehåller [Machine Learning CLI](reference-azure-machine-learning-cli.md) kommando rads verktyg som utför vanliga åtgärder, till exempel att skicka in en utbildning, köra eller distribuera en modell.

Information om hur du kommer igång med Azure Machine Learning finns i [Nästa steg](#next-steps).

## <a name="integration-with-other-services"></a>Integrering med andra tjänster

Azure Machine Learning fungerar med andra tjänster på Azure-plattformen och integreras med verktyg för öppen källkod som git och MLFlow.

+ Compute-mål som __Azure Kubernetes service__, __Azure Container instances__, __Azure Databricks__, __Azure Data Lake Analytics__och __Azure HDInsight__. Mer information om Compute-mål finns i [Vad är Compute-mål?](concept-compute-target.md).
+ __Azure Event Grid__. Mer information finns i [använda Azure Machine Learning händelser](concept-event-grid-integration.md).
+ __Azure Monitor__. Mer information finns i [övervaknings Azure Machine Learning](monitor-azure-machine-learning.md).
+ Data lager som __Azure Storage konton__, __Azure Data Lake Storage__, __Azure SQL Database__, __Azure Database for PostgreSQL__och __Azure Open-datauppsättningar__. Mer information finns i [få åtkomst till data i Azure Storage Services](how-to-access-data.md) och [skapa data uppsättningar med Azure Open data uppsättningar](how-to-create-register-datasets.md).
+ __Virtuella Azure-nätverk__. Mer information finns i [säker experimentering och härledning i ett virtuellt nätverk](how-to-enable-virtual-network.md).
+ __Azure-pipeliner__. Mer information finns i [träna och distribuera Machine Learning-modeller](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Git-lagringsplatsens loggar__. Mer information finns i [git-integrering](concept-train-model-git-integration.md).
+ __MLFlow__. Mer information finns i [MLflow för att spåra mått och distribuera modeller](how-to-use-mlflow.md) 
+ __Kubeflow__. Mer information finns i [bygga arbets flöden från slut punkt till slut punkt](https://www.kubeflow.org/docs/azure/).

### <a name="secure-communications"></a>Säker kommunikation

Ditt Azure Storage-konto, beräknings mål och andra resurser kan användas på ett säkert sätt i ett virtuellt nätverk för att träna modeller och utföra en härledning. Mer information finns i [säker experimentering och härledning i ett virtuellt nätverk](how-to-enable-virtual-network.md).

## <a name="basic--enterprise-editions"></a><a name="sku"></a>Grundläggande & Enterprise-versioner

Azure Machine Learning erbjuder två versioner som är skräddarsydda för dina maskin inlärnings behov:
+ **Basic**: bra för utveckling med öppen källkod i moln skala med en kod för första upplevelsen.

+ **Enterprise**: hela Basic Plus ett webb gränssnitt (Studio) + säker, omfattande ml livs cykel hantering för alla färdighets nivåer.

Läs mer om dessa versioner och vilka funktioner de låser upp i [artikeln Azure Machine Learning utgåvor](concept-editions.md).

## <a name="next-steps"></a>Nästa steg

- Skapa ditt första experiment med önskad metod:
  + [Använd python-anteckningsböcker för att träna & distribuera ML-modeller](tutorial-1st-experiment-sdk-setup.md)
  + [Använd R-markdown för att träna & distribuera ML-modeller](tutorial-1st-r-experiment.md) 
  + [Använd automatisk maskin inlärning för att träna & distribuera ML-modeller](tutorial-first-experiment-automated-ml.md) 
  + [Använd designerns dra & släpp-funktioner för att träna & distribuera](tutorial-designer-automobile-price-train-score.md) 
  + [Använda Machine Learning CLI för att träna och distribuera en modell](tutorial-train-deploy-model-cli.md)

- Lär dig mer om [pipelines för maskininlärning](concept-ml-pipelines.md) för att skapa, optimera och hantera dina maskininlärningsscenarier.

- Läs artikeln djupgående [Azure Machine Learning arkitektur och begrepp](concept-azure-machine-learning-architecture.md) .
