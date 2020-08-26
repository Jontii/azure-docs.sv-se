---
title: Vad är en arbets yta?
titleSuffix: Azure Machine Learning
description: Arbets ytan är den översta resursen för Azure Machine Learning. Den innehåller en historik över alla utbildningar som körs, inklusive loggar, statistik, utdata och en ögonblicks bild av dina skript. Du kan använda den här informationen för att avgöra vilken utbildning som ska användas för att skapa den bästa modellen
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/08/2020
ms.openlocfilehash: 437c2b8e42ed5128cc716eee23b8702ec012b481
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890922"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Vad är en Azure Machine Learning arbets yta?

Arbets ytan är toppnivå resursen för Azure Machine Learning, vilket ger en central plats för att arbeta med alla artefakter som du skapar när du använder Azure Machine Learning.  Arbets ytan har en historik över alla utbildningar som körs, inklusive loggar, mått, utdata och en ögonblicks bild av dina skript. Du kan använda den här informationen för att avgöra vilken utbildning som ska användas för att skapa den bästa modellen.  

När du har en modell som du gillar kan du registrera den med arbets ytan. Sedan använder du de registrerade modell-och bedömnings skripten för att distribuera till Azure Container Instances, Azure Kubernetes-tjänsten eller till en FPGA (Field-programmerbar grind array) som en REST-baserad HTTP-slutpunkt. Du kan också distribuera modellen till en Azure IoT Edge enhet som en modul.

Vilka priser och funktioner som är tillgängliga beror på om [Basic eller Enterprise Edition](overview-what-is-azure-ml.md#sku) har valts för arbets ytan. Du väljer versionen när du [skapar arbets ytan](#create-workspace).  Du kan också [Uppgradera](#upgrade) från Basic till Enterprise Edition.

## <a name="taxonomy"></a>Taxonomi 

En taxonomi i arbets ytan illustreras i följande diagram:

[![Taxonomi för arbets yta](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Diagrammet visar följande komponenter för en arbets yta:

+ En arbets yta kan innehålla [Azure Machine Learning beräknings instanser](concept-compute-instance.md), moln resurser som kon figurer ATS med python-miljön som krävs för att köra Azure Machine Learning.

+ Med [användar roller](how-to-assign-roles.md) kan du dela din arbets yta med andra användare, team eller projekt.
+ [Beräknings mål](concept-azure-machine-learning-architecture.md#compute-targets) används för att köra experimenten.
+ När du skapar arbets ytan skapas även [tillhör ande resurser](#resources) .
+ [Experiment](concept-azure-machine-learning-architecture.md#experiments) är utbildningar som körs och som du kan använda för att bygga modeller.  
+ [Pipelines](concept-azure-machine-learning-architecture.md#ml-pipelines) är återanvändbara arbets flöden för utbildning och omträning av din modell.
+ Data [uppsättnings](concept-azure-machine-learning-architecture.md#datasets-and-datastores) stöd vid hantering av de data du använder för modell utbildning och skapande av pipelines.
+ När du har en modell som du vill distribuera skapar du en registrerad modell.
+ Använd den registrerade modellen och ett bedömnings skript för att skapa en [distributions slut punkt](concept-azure-machine-learning-architecture.md#endpoints).

## <a name="tools-for-workspace-interaction"></a>Verktyg för arbets ytans interaktion

Du kan interagera med din arbets yta på följande sätt:

> [!IMPORTANT]
> Verktyg som marker ATS (för hands version) nedan finns för närvarande i offentlig för hands version.
> För hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+ På webben:
    + [Azure Machine Learning Studio ](https://ml.azure.com) 
    + [Azure Machine Learning designer (för hands version)](concept-designer.md) – endast tillgängligt i [Enterprise Edition](overview-what-is-azure-ml.md#sku) -arbetsytor.
+ I valfri python-miljö med [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ I valfri R-miljö med [Azure Machine Learning SDK för R (för hands version)](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ På kommando raden med hjälp av Azure Machine Learning [CLI-tillägget](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)
+ [Azure Machine Learning VS Code-tillägg](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>Machine Learning med en arbets yta

Machine Learning-uppgifter läser och/eller skriver artefakter till din arbets yta.

+ Kör ett experiment för att träna en modell – skriver experiment körnings resultat till arbets ytan.
+ Använd automatisk ML för att träna en modell – skriver utbildnings resultat till arbets ytan.
+ Registrera en modell i arbets ytan.
+ Distribuera en modell – använder den registrerade modellen för att skapa en distribution.
+ Skapa och kör återanvändbara arbets flöden.
+ Visa maskin inlärnings artefakter som experiment, pipelines, modeller, distributioner.
+ Spåra och övervaka modeller.

## <a name="workspace-management"></a>Hantering av arbets yta

Du kan också utföra följande hanterings uppgifter för arbets ytan:

| Arbets ytans hanterings aktivitet   | Portalen              | Studio | Python SDK/R SDK       | CLI        | VS Code
|---------------------------|---------|---------|------------|------------|------------|
| Skapa en arbetsyta        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| Hantera åtkomst till arbets ytan    | **&check;**   || |  **&check;**    ||
| Uppgradera till Enterprise Edition    | **&check;** | **&check;**  | |     ||
| Skapa och hantera beräknings resurser    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Skapa en virtuell dator för Notebook |   | **&check;** | |     ||

> [!WARNING]
> Det finns inte stöd för att flytta Azure Machine Learning arbets ytan till en annan prenumeration eller flytta den ägande prenumerationen till en ny klient. Detta kan orsaka fel.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Skapa en arbets yta

När du skapar en arbets yta bestämmer du om du vill skapa den med [Basic-eller Enterprise-versionen](overview-what-is-azure-ml.md#sku). Versionen avgör vilka funktioner som är tillgängliga i arbets ytan. Med Enterprise Edition får du till gång till [Azure Machine Learning designer](concept-designer.md) och Studio versionen av att skapa [automatiserade maskin inlärnings experiment](tutorial-first-experiment-automated-ml.md).  Mer information och pris information finns i [Azure Machine Learning prissättning](https://azure.microsoft.com/pricing/details/machine-learning/).

Det finns flera sätt att skapa en arbets yta:  

* Använd [Azure Portal](how-to-manage-workspace.md) för ett punkt-och-klick-gränssnitt för att vägleda dig genom varje steg.
* Använd [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) för att skapa en arbets yta i farten från Python-skript eller Jupiter-anteckningsböcker
* Använd en [Azure Resource Manager mall](how-to-create-workspace-template.md) eller [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) när du behöver automatisera eller anpassa skapandet med företags säkerhets standarder.
* Använd [vs Code-tillägget](how-to-manage-resources-vscode.md#create-a-workspace)om du arbetar i Visual Studio Code.

> [!NOTE]
> Namnet på arbets ytan är Skift läges okänsligt.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a> Uppgradera till Enterprise Edition

Du kan [uppgradera din arbets yta från Basic till Enterprise Edition](how-to-manage-workspace.md#upgrade) med Azure Portal. Du kan inte nedgradera en Enterprise Edition-arbetsyta till en Basic Edition-arbetsyta. 

## <a name="associated-resources"></a><a name="resources"></a> Associerade resurser

När du skapar en ny arbets yta skapar den automatiskt flera Azure-resurser som används av arbets ytan:

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): registrerar Docker-behållare som du använder under utbildningen och när du distribuerar en modell. För att minimera kostnaderna är ACR en **Lazy-inläst** tills distributions avbildningar skapas.
+ [Azure Storage konto](https://azure.microsoft.com/services/storage/): används som standard data lager för arbets ytan.  Jupyter-anteckningsböcker som används med dina Azure Machine Learning beräknings instanser lagras också här.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): lagrar övervaknings information om dina modeller.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): lagrar hemligheter som används av beräknings mål och annan känslig information som krävs av arbets ytan.

> [!NOTE]
> Förutom att skapa nya versioner kan du också använda befintliga Azure-tjänster.

### <a name="azure-storage-account"></a>Azure Storage-konto

Det Azure Storage konto som skapas som standard med arbets ytan är ett allmänt v1-konto. Du kan uppgradera detta till General-Purpose v2 När arbets ytan har skapats genom att följa stegen i artikeln [Uppgradera till ett allmänt lagrings konto](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) .

> [!IMPORTANT]
> Aktivera inte hierarkiskt namn område på lagrings kontot efter uppgraderingen till General-Purpose v2.

Om du vill använda ett befintligt Azure Storage-konto kan det inte vara ett Premium-konto (Premium_LRS och Premium_GRS). Det får inte heller ha ett hierarkiskt namn område (används med Azure Data Lake Storage Gen2). Varken Premium Storage eller hierarkiska namn områden stöds med arbets ytans _standard_ lagrings konto. Du kan använda Premium Storage eller hierarkiskt namnrymd med lagrings konton som _inte är standard_ .



## <a name="next-steps"></a>Nästa steg

För att komma igång med Azure Machine Learning, se:

+ [Översikt över Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Skapa en arbetsyta](how-to-manage-workspace.md)
+ [Hantera en arbetsyta](how-to-manage-workspace.md)
+ [Självstudie: kom igång med att skapa ditt första ML-experiment med python SDK](tutorial-1st-experiment-sdk-setup.md)
+ [Självstudie: kom igång med Azure Machine Learning med R SDK](tutorial-1st-r-experiment.md)
+ [Självstudie: skapa din första klassificerings modell med automatisk maskin inlärning](tutorial-first-experiment-automated-ml.md) (endast tillgängligt i [Enterprise Edition](overview-what-is-azure-ml.md#sku) -arbetsytor)
+ [Självstudie: förutsäga Automobile-priset med designern](tutorial-designer-automobile-price-train-score.md) (endast tillgängligt i [Enterprise Edition](overview-what-is-azure-ml.md#sku) -arbetsytor)
