---
title: MLflow spårning för ML experiment
titleSuffix: Azure Machine Learning
description: Konfigurera MLflow med Azure Machine Learning för att logga mått och artefakter från ML-modeller och distribuera dina ML-modeller som en webb tjänst.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 116faae1bc0a93ce2007fcf809a8c96475289036
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897194"
---
# <a name="track-model-metrics-and-deploy-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Spåra modell mått och distribuera ML-modeller med MLflow och Azure Machine Learning (för hands version)

Den här artikeln visar hur du aktiverar MLflow för spårnings-och loggnings-API: t, gemensamt känt som [MLflow spårning](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), för att ansluta dina MLflow experiment och Azure Machine Learning. 

Med Azure Machine Learning inbyggt stöd för MLflow kan du

+ Spåra och logga experiment mått och artefakter i din [Azure Machine Learning-arbetsyta](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Om du redan använder MLflow spårning för dina experiment, tillhandahåller arbets ytan en centraliserad, säker och skalbar plats för att lagra utbildnings mått och-modeller.

+ Skicka utbildnings jobb med MLflow-projekt med Azure Machine Learning backend-support (för hands version). Du kan skicka jobb lokalt med Azure Machine Learning spårning eller migrera dina körningar till molnet som via en [Azure Machine Learning-beräkning](https://docs.microsoft.com/azure/machine-learning/how-to-create-attach-compute-sdk#amlcompute).

+ Spåra och hantera modeller i MLflow och Azure Machine Learning modell register.

+ Distribuera dina MLflow-experiment som en Azure Machine Learning-webbtjänst. Genom att distribuera som en webb tjänst kan du använda funktionerna för att identifiera Azure Machine Learning övervakning och data avkänning i dina produktions modeller. 

[MLflow](https://www.mlflow.org) är ett bibliotek med öppen källkod för hantering av livs cykeln för maskin inlärnings experiment. MLFlow-spårning är en komponent i MLflow som loggar och spårar din utbildning kör mått och modell artefakter, oavsett experimentets miljö – lokalt på datorn, på ett fjärrberäknings mål, en virtuell dator eller ett Azure Databricks-kluster. 

>[!NOTE]
> Som ett bibliotek med öppen källkod ändras MLflow ofta. De funktioner som gjorts tillgängliga via Azure Machine Learning-och MLflow-integreringen bör därför betraktas som en för hands version och stöds inte fullt ut av Microsoft.

Följande diagram illustrerar att med MLflow spårning spårar du ett Experiments körnings mått och lagrings modell artefakter i din Azure Machine Learning-arbetsyta.

![mlflow med Azure Machine Learning-diagram](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Informationen i det här dokumentet är främst avsedd för data experter och utvecklare som vill övervaka processen för modell inlärning. Om du är en administratör som vill övervaka resursanvändningen och händelser från Azure Machine Learning, till exempel kvoter, slutförda utbildningar eller slutförda modell distributioner, se [övervakning Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Jämför MLflow och Azure Machine Learning klienter

 I tabellen nedan sammanfattas olika klienter som kan använda Azure Machine Learning och deras respektive funktions funktioner.

 MLflow tracking erbjuder funktioner för mått loggning och artefakt lagring som endast är tillgängliga via [Azure Machine Learning python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true).

| Kapacitet | MLflow spårning & distribution | Azure Machine Learning python SDK |  Azure Machine Learning CLI | Azure Machine Learning-studio|
|---|---|---|---|---|
| Hantera arbets yta |   | ✓ | ✓ | ✓ |
| Använda data lager  |   | ✓ | ✓ | |
| Loggmått      | ✓ | ✓ |   | |
| Ladda upp artefakter | ✓ | ✓ |   | |
| Visa mått     | ✓ | ✓ | ✓ | ✓ |
| Hantera beräkning   |   | ✓ | ✓ | ✓ |
| Distribuera modeller    | ✓ | ✓ | ✓ | ✓ |
|Övervaka modell prestanda||✓|  |   |
| Identifiera dataavvikelser |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Förutsättningar

* [Installera MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installera Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) på den lokala datorn SDK: n ger anslutningen till MLflow för att få åtkomst till din arbets yta.
* [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Spåra lokala körningar

Genom MLflow spårning med Azure Machine Learning kan du lagra de inloggade måtten och artefakterna från dina lokala körningar i Azure Machine Learning arbets ytan.

Installera `azureml-mlflow` paketet om du vill använda MLflow spårning med Azure Machine Learning på dina experiment lokalt i en Jupyter Notebook eller kod redigerare.

```shell
pip install azureml-mlflow
```

Importera `mlflow` och- [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py&preserve-view=true) klasserna för att få åtkomst till MLflows spårnings-URI och konfigurera din arbets yta.

I följande kod `get_mlflow_tracking_uri()` tilldelar metoden en unik spårnings-URI-adress till arbets ytan, `ws` och `set_tracking_uri()` pekar på MLflow spårnings-URI: n till den adressen.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Spårnings-URI: n är giltig till en timme eller mindre. Om du startar om skriptet efter inaktivitet använder du get_mlflow_tracking_uri API för att hämta en ny URI.

Ange MLflow experiment namn med `set_experiment()` och starta din utbildning med `start_run()` . Använd sedan `log_metric()` för att aktivera loggnings-API: et för MLflow och börja logga dina utbildnings körnings mått.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Spåra fjärrkörningar

Med MLflow spårning med Azure Machine Learning kan du lagra de inloggade måtten och artefakterna från fjärrdatorn som körs i Azure Machine Learning arbets ytan.

Med fjärrkörningar kan du träna dina modeller på mer kraftfulla beräkningar, till exempel GPU-aktiverade virtuella datorer eller Machine Learning-beräkning kluster. Se [använda beräknings mål för modell utbildning](how-to-set-up-training-targets.md) för att lära dig mer om olika beräknings alternativ.

Konfigurera din beräknings-och övnings körnings miljö med- [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) klassen. Ta med `mlflow` och `azureml-mlflow` pip-paket i miljö [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py&preserve-view=true) avsnittet. Skapa sedan  [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) med fjärrberäkningen som beräknings mål.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

Importera `mlflow` med hjälp av MLflow Logging-API: er i ditt utbildnings skript och börja logga dina körnings mått.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Med den här beräknings-och övnings körnings konfigurationen använder du `Experiment.submit()` metoden för att skicka in en körning. Den här metoden anger automatiskt MLflow spårnings-URI och dirigerar loggningen från MLflow till din arbets yta.

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>Träna med MLflow-projekt

Med [MLflow-projekt](https://mlflow.org/docs/latest/projects.html) kan du ordna och beskriva din kod för att låta andra data experter (eller automatiserade verktyg) köra den. Med MLflow-projekt med Azure Machine Learning kan du spåra och hantera dina utbildnings körningar i din arbets yta. 

Det här exemplet visar hur du skickar MLflow-projekt lokalt med Azure Machine Learning spårning.

Installera `azureml-mlflow` paketet om du vill använda MLflow spårning med Azure Machine Learning på dina experiment lokalt. Experimenten kan köras via en Jupyter-anteckningsbok eller kod redigerare.

```shell
pip install azureml-mlflow
```

Importera `mlflow` och- [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) klasserna för att få åtkomst till MLflows spårnings-URI och konfigurera din arbets yta.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Ange MLflow experiment namn med `set_experiment()` och starta din utbildning med `start_run()` . Använd sedan `log_metric()` för att aktivera loggnings-API: et för MLflow och börja logga dina utbildnings körnings mått.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

Skapa konfigurations objekt för Server delen för att lagra nödvändig information för integreringen, till exempel beräknings målet och vilken typ av hanterad miljö som ska användas.

```python
backend_config = {"USE_CONDA": False}
```
Lägg till `azureml-mlflow` paketet som ett pip-beroende i miljö konfigurations filen för att kunna spåra mått och nyckel artefakter i din arbets yta. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
Skicka den lokala körningen och se till att du anger parametern `backend = "azureml" ` . Med den här inställningen kan du skicka körningar lokalt och få till gång till stöd för automatisk utdata, loggfiler, ögonblicks bilder och utskrifts fel i arbets ytan. 

Visa dina körningar och mått i [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md). 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="track-azure-databricks-runs"></a>Spåra Azure Databricks körningar

Med MLflow spårning med Azure Machine Learning kan du lagra de inloggade måtten och artefakterna från dina Azure Databricks körs i alla tre av följande områden på en gång: 

* Azure Machine Learning-arbetsyta
* Azure Databricks-arbetsyta.
* MLflow

Om du vill köra dina Mlflow-experiment med Azure Databricks måste du först skapa en [Azure Databricks arbets yta och ett kluster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). I klustret installerar du *azureml-mlflow-* biblioteket från PyPi för att se till att klustret har åtkomst till nödvändiga funktioner och klasser.

> [!NOTE]
> `azureml.core`Paketet innehåller `azureml-mlflow` . Om du redan har installerat `azureml.core` kan du hoppa över `azureml-mlflow` installations steget. 

Härifrån kan du importera din experiment-anteckningsbok, koppla den till ditt Azure Databricks-kluster och köra experimentet. 

### <a name="install-libraries"></a>Installera bibliotek

Om du vill installera bibliotek i klustret går du till fliken **bibliotek** och klickar på **installera ny**

 ![mlflow med Azure databricks](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

I fältet **paket** skriver du azureml-mlflow och klickar sedan på installera. Upprepa det här steget vid behov för att installera andra ytterligare paket till klustret för experimentet.

 ![Mlflow-bibliotek för Azure DB](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Konfigurera din bärbara dator och arbets yta

När klustret har kon figurer ATS importerar du din experiment-anteckningsbok, öppnar den och kopplar klustret till den.

Följande kod ska finnas i din experiment antecknings bok. Den här koden hämtar information om din Azure-prenumeration för att skapa en instans av arbets ytan. Den här koden förutsätter att du har en befintlig resurs grupp och Azure Machine Learning arbets ytan, annars kan du [skapa dem](how-to-manage-workspace.md). 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)
```

### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Anslut dina Azure Databricks och Azure Machine Learning arbets ytor

På [Azure Portal](https://ms.portal.azure.com)kan du länka din Azure DATABRICKS (ADB)-arbets yta till en ny eller befintlig Azure Machine Learning-arbetsyta. Det gör du genom att gå till din ADB-arbetsyta och välja knappen **länk Azure Machine Learning arbets yta** längst ned till höger. Genom att länka dina arbets ytor kan du spåra dina experiment data i Azure Machine Learning-arbetsytan. 

### <a name="mlflow-tracking-in-your-workspaces"></a>MLflow spårning i dina arbets ytor

När du har instansierat din arbets yta anges MLflow-spårning automatiskt som spåras på följande platser:

* Arbets ytan länkad Azure Machine Learning.
* Din ursprungliga ADB-arbetsyta. 
* MLflow. 

Alla experiment kommer att hamna i den hanterade Azure Machine Learning spårnings tjänsten.

#### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>Ställ in MLflow-spårning för att bara spåra i din Azure Machine Learning-arbetsyta

Om du föredrar att hantera dina spårade experiment på en central plats kan du ange MLflow spårning så att den **bara** spårar i din Azure Machine Learning-arbetsyta. 


```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Importera `mlflow` med hjälp av MLflow Logging-API: er i ditt utbildnings skript och börja logga dina körnings mått. I följande exempel loggas måttet för epoken. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Visa mått och artefakter i din arbets yta

Måtten och artefakterna från MLflow-loggning behålls i din arbets yta. Om du vill visa dem när som helst, navigerar du till din arbets yta och hittar experimentet efter namn i din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com).  Eller kör koden nedan. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="manage-models"></a>Hantera modeller 

Registrera och spåra dina modeller med [Azure Machine Learning Model-registret](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere) som stöder MLflow Model-registret. Azure Machine Learning modeller justeras med MLflow modell schema och gör det enkelt att exportera och importera dessa modeller över olika arbets flöden. MLflow relaterade metadata som, till exempel körnings-ID taggas också med den registrerade modellen för spårning. Användarna kan skicka in utbildnings körningar, registrera och distribuera modeller som skapats från MLflow-körningar. 

Om du vill distribuera och registrera din produktions färdiga modell i ett enda steg, se [distribuera och registrera MLflow-modeller](#deploy-and-register-mlflow-models).

Använd följande steg för att registrera och visa en modell från en körning:

1. Anropa metoden när körningen är klar `register_model()` .

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. Visa den registrerade modellen i din arbets yta med [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md).

    I följande exempel har den registrerade modellen `my-model` MLflow spårning av metadata som taggats. 

    ![registrerad-mlflow-Model](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. Välj fliken **artefakter** om du vill se alla konsolfiler som överensstämmer med MLflow-modell schema (Conda. yaml, MLmodel, Model. PKL).

    ![modell schema](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. Välj MLmodel för att se MLmodel-filen som genereras av körningen.

    ![MLmodel – schema](./media/how-to-use-mlflow/mlmodel-view.png)



## <a name="deploy-and-register-mlflow-models"></a>Distribuera och registrera MLflow-modeller 

Genom att distribuera dina MLflow-experiment som en Azure Machine Learning-webbtjänst kan du utnyttja och använda funktionerna för hantering av Azure Machine Learning-modell och data avkänning i dina produktions modeller.

För att göra det måste du

1. Registrera din modell.
1. Ta reda på vilken distributions konfiguration som du vill använda för ditt scenario.

    1. [Azure Container Instance (ACI)](#deploy-to-aci) är ett lämpligt alternativ för en snabb distribution av dev-test.
    1. [Azure Kubernetes service (AKS)](#deploy-to-aks) är lämpligt för skalbara produktions distributioner.

Följande diagram visar att med MLflow distributions-API: t kan du distribuera dina befintliga MLflow-modeller som en Azure Machine Learning webb tjänst, trots deras ramverk – PyTorch, Tensorflow, scikit – lära, ONNX och så vidare, och hantera dina produktions modeller i din arbets yta.

![ distribuera mlflow-modeller med Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)


### <a name="deploy-to-aci"></a>Distribuera till ACI

Konfigurera distributions konfigurationen med metoden [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Du kan också lägga till taggar och beskrivningar för att hålla reda på din webb tjänst.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Registrera och distribuera sedan modellen i ett steg med [distributions](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) metoden för Azure Machine Learning SDK. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

### <a name="deploy-to-aks"></a>Distribuera till AKS

Om du vill distribuera till AKS måste du först skapa ett AKS-kluster. Skapa ett AKS-kluster med metoden [ComputeTarget. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) . Det kan ta 20-25 minuter att skapa ett nytt kluster.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Konfigurera distributions konfigurationen med metoden [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Du kan också lägga till taggar och beskrivningar för att hålla reda på din webb tjänst.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Registrera och distribuera sedan modellen i ett steg med Azure Machine Learning SDK [Deploy ()] (sedan registrerar och distribuerar du modellen med hjälp av metoden för [distribution](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) av Azure Machine Learning SDK. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

Tjänste distributionen kan ta flera minuter.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda de loggade måtten och artefakterna i din arbets yta är möjligheten att ta bort dem individuellt otillgänglig. Ta i stället bort resurs gruppen som innehåller lagrings kontot och arbets ytan, så du debiteras inte några avgifter:

1. I Azure-portalen väljer du **Resursgrupper** längst till vänster.

   ![Ta bort i Azure Portal](./media/how-to-use-mlflow/delete-resources.png)

1. Välj den resursgrupp i listan som du har skapat.

1. Välj **Ta bort resursgrupp**.

1. Ange resursgruppsnamnet. Välj sedan **Ta bort**.

## <a name="example-notebooks"></a>Exempelnotebook-filer

[MLflow med Azure ml-anteckningsböcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) demonstrerar och utökar begrepp som presenteras i den här artikeln.

## <a name="next-steps"></a>Nästa steg

* [Hantera dina modeller](concept-model-management-and-deployment.md).
* Övervaka dina produktions modeller för [data avvikelser](how-to-monitor-data-drift.md).
