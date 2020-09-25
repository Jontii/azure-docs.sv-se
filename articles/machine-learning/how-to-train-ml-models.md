---
title: Träna ML-modeller med uppskattningar
titleSuffix: Azure Machine Learning
description: Lär dig hur du utför en enda nod och distribuerad utbildning av traditionella maskin inlärnings-och djup inlärnings modeller med hjälp av Azure Machine Learning uppskattnings klass
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 051a910fb8803f7c9ebc6d9cdfb00bc814db4c0e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250869"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Träna modeller med Azure Machine Learning med hjälp av uppskattning


Med Azure Machine Learning kan du enkelt skicka ditt utbildnings skript till [olika beräknings mål](how-to-set-up-training-targets.md), med hjälp av ett [RunConfiguration-objekt](how-to-set-up-training-targets.md#whats-a-run-configuration) och ett ScriptRunConfig- [objekt](how-to-set-up-training-targets.md#submit). Det här mönstret ger dig mycket flexibilitet och maximal kontroll.


Klassen uppskattning gör det enklare att träna modeller med djup inlärning och förstärknings inlärning. Det ger en abstraktion på hög nivå som gör att du enkelt kan skapa körnings konfiguration. Du kan skapa och använda en generisk [uppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py&preserve-view=true) för att skicka utbildnings skript med hjälp av valfritt ramverk som du väljer (till exempel scikit-information) på alla beräknings mål du väljer, oavsett om det är en lokal dator, en enskild virtuell dator i Azure eller ett GPU-kluster i Azure. För PyTorch-, TensorFlow-, kedje-och förstärknings aktiviteter Azure Machine Learning tillhandahåller även de olika [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py&preserve-view=true)-, [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py&preserve-view=true)-, [kedje](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py&preserve-view=true)-och [förstärknings inlärnings](how-to-use-reinforcement-learning.md) uppskattningarna för att förenkla användningen av dessa ramverk.

## <a name="train-with-an-estimator"></a>Träna med en uppskattning

När du har skapat din [arbets yta](concept-workspace.md) och konfigurerat [utvecklings miljön](how-to-configure-environment.md)kan du träna en modell i Azure Machine Learning följande steg:  
1. Skapa ett [fjärrberäknings mål](how-to-create-attach-compute-sdk.md) (eller också kan du använda lokal dator som beräknings mål)
2. Ladda upp dina [utbildnings data](how-to-access-data.md) till data lagret (valfritt)
3. Skapa ett [utbildnings skript](tutorial-train-models-with-aml.md#create-a-training-script)
4. Skapa ett `Estimator`-objekt
5. Skicka uppskattningen till ett experiment objekt under arbets ytan

Den här artikeln fokuserar på steg 4-5. Steg 1-3 finns i [självstudierna träna en modell](tutorial-train-models-with-aml.md) för ett exempel.

### <a name="single-node-training"></a>Utbildning med en nod

Använd en `Estimator` för en utbildning med en enda nod på fjärrberäkning i Azure för en scikit-modell. Du bör redan ha skapat [Compute Target](how-to-create-attach-compute-sdk.md#amlcompute) -objektet `compute_target` och [FileDataset](how-to-create-register-datasets.md) -objektet `ds` .

```Python
from azureml.train.estimator import Estimator

script_params = {
    # to mount files referenced by mnist dataset
    '--data-folder': ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Det här kodfragmentet anger följande parametrar för `Estimator` konstruktorn.

Parameter | Beskrivning
--|--
`source_directory`| Lokal katalog som innehåller all kod som behövs för utbildnings jobbet. Den här mappen kopieras från den lokala datorn till fjärrdatorn.
`script_params`| Ord lista som anger de kommando rads argument som ska skickas till ditt utbildnings skript `entry_script` i form av `<command-line argument, value>` par. Om du vill ange en utförlig flagga i `script_params` använder du `<command-line argument, "">` .
`compute_target`| Fjärrberäknings mål som ditt utbildnings skript ska köras på, i det här fallet ett Azure Machine Learning Compute-kluster ([AmlCompute](how-to-create-attach-compute-sdk.md#amlcompute)). (Observera även om AmlCompute-kluster är det vanligaste målet, är det också möjligt att välja andra beräknings mål typer, till exempel virtuella Azure-datorer eller till och med lokal dator.)
`entry_script`| Sökväg (relativ till `source_directory` ) för det utbildnings skript som ska köras på fjärrdatorn. Den här filen och eventuella ytterligare filer som den är beroende av bör finnas i den här mappen.
`conda_packages`| Lista över python-paket som ska installeras via Conda som krävs av ditt utbildnings skript.  

Konstruktorn har en annan parameter `pip_packages` som heter att du använder för alla pip-paket som behövs.

Nu när du har skapat `Estimator` objektet skickar du det utbildnings jobb som ska köras på fjärrdatorn med ett anrop till `submit` funktionen i [experiment](concept-azure-machine-learning-architecture.md#experiments) -objektet `experiment` . 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Särskilda mappar** Två mappar, *utdata* och *loggar*, tar emot särskild behandling av Azure Machine Learning. När du skriver filer till mappar med namnet *utdata* och *loggar* som är relativa till rot katalogen ( `./outputs` och `./logs` respektive) överförs filerna automatiskt till din körnings historik så att du har åtkomst till dem när körningen är färdig.
>
> Om du vill skapa artefakter under träning (till exempel modell filer, kontroll punkter, datafiler eller ritade bilder) skriver du dessa till `./outputs` mappen.
>
> På samma sätt kan du skriva loggar från din utbildning som körs till `./logs` mappen. Om du vill använda Azure Machine Learning [TensorBoard-integrering](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) kontrollerar du att du skriver dina TensorBoard-loggar till den här mappen. När körningen pågår kommer du att kunna starta TensorBoard och strömma dessa loggar.  Senare kommer du även att kunna återställa loggarna från alla tidigare körningar.
>
> Till exempel för att ladda ned en fil som skrivits till mappen *utdata* till din lokala dator efter att din fjärran sluten utbildning har körts: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Distribuerad utbildning och anpassade Docker-avbildningar

Det finns två ytterligare utbildnings scenarier som du kan utföra med `Estimator` :
* Använda en anpassad Docker-avbildning
* Distribuerad utbildning i ett kluster med flera noder

Följande kod visar hur du utför distribuerad utbildning för en keras-modell. I stället för att använda standard Azure Machine Learning avbildningar anges dessutom en anpassad Docker-avbildning från Docker Hub `continuumio/miniconda` för träning.

Du bör redan ha skapat ditt [Compute Target](how-to-create-attach-compute-sdk.md#amlcompute) -objekt `compute_target` . Du skapar uppskattningen enligt följande:

```Python
from azureml.train.estimator import Estimator
from azureml.core.runconfig import MpiConfiguration

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),        
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

Koden ovan visar följande nya parametrar för `Estimator` konstruktorn:

Parameter | Beskrivning | Standardvärde
--|--|--
`custom_docker_image`| Namnet på den avbildning som du vill använda. Ange bara avbildningar som är tillgängliga i offentliga Docker-databaser (i det här fallet Docker Hub). Använd konstruktorns parameter i stället om du vill använda en avbildning från en privat Docker-lagringsplats `environment_definition` .| `None`
`node_count`| Antal noder som ska användas för ditt utbildnings jobb. | `1`
`process_count_per_node`| Antal processer (eller "arbetare") som ska köras på varje nod. I det här fallet använder du de `2` GPU: er som är tillgängliga på varje nod.| `1`
`distributed_training`| [MPIConfiguration ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true) -objekt för att starta distribuerad utbildning med MPI-backend.  | `None`


Slutligen skickar du utbildnings jobbet:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Registrera en modell

När du har tränat modellen kan du spara och registrera den på din arbets yta. Med modell registreringen kan du lagra och version av dina modeller i din arbets yta för att förenkla [modell hantering och distribution](concept-model-management-and-deployment.md).

Genom att köra följande kod registrerar du modellen på din arbets yta och gör den tillgänglig för referens med namn i fjärrstyrda beräknings kontexter eller distributions skript. [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)Mer information och ytterligare parametrar finns i referens dokumenten.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>GitHub spårning och integrering

När du startar en utbildning som kör där käll katalogen är en lokal git-lagringsplats, lagras information om lagrings platsen i körnings historiken. Mer information finns i [git-integrering för Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="examples"></a>Exempel

För en bärbar dator som tränar en scikit-modell med hjälp av en uppskattning kan du läsa:
* [Självstudier/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

För antecknings böcker i utbildnings modeller med hjälp av detaljerade uppskattningar för djup inlärnings ramverk, se:

* [How-to-use-azureml/ml-Framework](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* [Spåra körnings mått under träning](how-to-track-experiments.md)
* [Utbilda PyTorch-modeller](how-to-train-pytorch.md)
* [Utbilda TensorFlow-modeller](how-to-train-tensorflow.md)
* [Träna ett djup neurala nätverk för förstärknings inlärning](how-to-use-reinforcement-learning.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
* [Skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md)