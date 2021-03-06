---
title: Skapa och kör ML-pipeliner
titleSuffix: Azure Machine Learning
description: Skapa och kör maskin inlärnings pipeliner för att skapa och hantera arbets flöden som häftar ihop Machine Learning-faser (ML).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: nilsp
author: NilsPohlmann
ms.date: 12/10/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 168e5340842dca3c26e4fa48d2f14b8ade529cd9
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505755"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Skapa och kör maskin inlärnings pipeliner med Azure Machine Learning SDK

I den här artikeln får du lära dig hur du skapar och kör [maskin inlärnings pipeliner](concept-ml-pipelines.md) med hjälp av [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py). Använd **ml-pipelines** för att skapa ett arbets flöde som häftar samman olika ml-faser. Publicera sedan den pipelinen för senare åtkomst eller dela med andra. Spåra ML-pipelines för att se hur din modell presterar i verkligheten och för att identifiera data drift. ML-pipelines är idealiska för scenarier med batch-poäng, med hjälp av olika beräkningar, återanvända steg i stället för att köra om dem, samt dela ML-arbetsflöden med andra.

Den här artikeln är inte en själv studie kurs. Vägledning om hur du skapar din första pipeline finns i [Självstudier: skapa en Azure Machine Learning pipeline för batch-Poäng](tutorial-pipeline-batch-scoring-classification.md) eller [Använd automatisk ML i en Azure Machine Learning pipeline i python](how-to-use-automlstep-in-pipelines.md). 

Även om du kan använda en annan typ av pipeline som kallas för en [Azure-pipeline](/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2fmachine-learning%2fservice%2fcontext%2fml-context&preserve-view=true&tabs=yaml&view=azure-devops) för CI/CD-automatisering av ml-aktiviteter, lagras inte den typen av pipelines i din arbets yta. [Jämför dessa olika pipeliner](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

De ML-pipeliner som du skapar visas för medlemmarna i din Azure Machine Learning- [arbetsyta](how-to-manage-workspace.md). 

ML pipelines körs på beräknings mål (se [Vad är beräknings mål i Azure Machine Learning](./concept-compute-target.md)). Pipelines kan läsa och skriva data till och från [Azure Storage](../storage/index.yml) platser som stöds.

Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Krav

* Skapa en [Azure Machine Learning arbets yta](how-to-manage-workspace.md) för att lagra alla dina pipeline-resurser.

* [Konfigurera utvecklings miljön](how-to-configure-environment.md) för att installera Azure Machine Learning SDK eller använd en [Azure Machine Learning beräknings instans](concept-compute-instance.md) med SDK redan installerad.

Börja med att koppla din arbets yta:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>Konfigurera Machine Learning-resurser

Skapa de resurser som krävs för att köra en ML-pipeline:

* Konfigurera ett data lager som används för att komma åt de data som behövs i pipeline-stegen.

* Konfigurera ett `Dataset` objekt så att det pekar på beständiga data som finns i, eller är tillgängliga i, ett data lager. Konfigurera ett `PipelineData` objekt för temporära data som skickas mellan pipeline-steg. 

    > [!TIP]
    > En förbättrad upplevelse för att skicka temporära data mellan pipeline-steg finns i den offentliga för hands versions klassen  [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) .  Den här klassen är en [experimentell](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#&preserve-view=truestable-vs-experimental) förhands gransknings funktion och kan ändras när som helst.

* Konfigurera [beräknings målen](concept-azure-machine-learning-architecture.md#compute-targets) som dina pipeline-steg ska köras på.

### <a name="set-up-a-datastore"></a>Konfigurera ett data lager

Ett data lager lagrar data för pipelinen för åtkomst. Varje arbets yta har ett standard data lager. Du kan registrera ytterligare data lager. 

När du skapar din arbets yta är [Azure Files](../storage/files/storage-files-introduction.md) och [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) kopplade till arbets ytan. Ett standard data lager har registrerats för att ansluta till Azure Blob Storage. Mer information finns i [bestämma när du ska använda Azure Files, Azure-blobbar eller Azure-diskar](../storage/common/storage-introduction.md). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Stegen förbrukar vanligt vis data och producerar utdata. Ett steg kan skapa data, till exempel en modell, en katalog med modell och beroende filer eller temporära data. Dessa data är sedan tillgängliga för andra steg senare i pipelinen. Mer information om hur du ansluter din pipeline till dina data finns i artikeln [så här får du åtkomst till data](how-to-access-data.md) och [hur du registrerar data uppsättningar](how-to-create-register-datasets.md). 

### <a name="configure-data-with-dataset-and-pipelinedata-objects"></a>Konfigurera data med `Dataset` och- `PipelineData` objekt

Det bästa sättet att tillhandahålla data till en pipeline är ett [data uppsättnings](/python/api/azureml-core/azureml.core.dataset.Dataset) objekt. `Dataset`Objektet pekar på data som finns i eller kan nås från ett data lager eller en webb-URL. `Dataset`Klassen är abstrakt, så du kommer att skapa en instans av antingen en `FileDataset` (som refererar till en eller flera filer) eller en `TabularDataset` som skapats av från en eller flera filer med avgränsade kolumner med data.


Du skapar en `Dataset` med metoder som [from_files](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-files-path--validate-true-) eller [from_delimited_files](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-).

```python
from azureml.core import Dataset

my_dataset = Dataset.File.from_files([(def_blob_store, 'train-images/')])
```
Mellanliggande data (eller utdata från ett steg) representeras av ett [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) -objekt. `output_data1` skapas som utdata från ett steg och används som indata för ett eller flera framtida steg. `PipelineData` introducerar ett data beroende mellan stegen och skapar en implicit körnings ordning i pipelinen. Det här objektet kommer att användas senare när du skapar pipeline-steg.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")

```

> [!TIP]
> Det går också att bevara mellanliggande data mellan pipeline-steg med den offentliga för hands versions klassen [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) . Ett kod exempel som använder `OutputFileDatasetConfig` -klassen finns i så här [skapar du en pipeline för två steg ml](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).

> [!TIP]
> Ladda bara upp filer som är relevanta för jobbet i handen. Eventuella ändringar i filerna i data katalogen visas som en anledning till att köra om steget nästa gång pipelinen körs, även om åter användning har angetts. 

## <a name="set-up-a-compute-target"></a>Konfigurera ett beräknings mål


I Azure Machine Learning syftar termen __Compute__ (eller __Compute Target__) på de datorer eller kluster som utför beräknings stegen i din Machine Learning-pipeline.   Se [Compute-mål för modell utbildning](concept-compute-target.md#train) för en fullständig lista över beräknings mål och [skapa beräknings mål](how-to-create-attach-compute-studio.md) för att skapa och koppla dem till din arbets yta.   Processen för att skapa och eller koppla ett beräknings mål är detsamma oavsett om du tränar en modell eller kör ett pipeline-steg. När du har skapat och kopplat ditt beräknings mål använder du `ComputeTarget` objektet i ditt [pipeline-steg](#steps).

> [!IMPORTANT]
> Det finns inte stöd för att utföra hanterings åtgärder på beräknings mål inifrån Fjärrjobb. Eftersom Machine Learning-pipelines skickas som ett Fjärrjobb använder du inte hanterings åtgärder på beräknings mål inifrån pipelinen.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute

Du kan skapa en Azure Machine Learning beräkning för att köra dina steg. Koden för andra beräknings mål är mycket lik, med något annorlunda parametrar, beroende på typ. 

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

## <a name="configure-the-training-runs-environment"></a>Konfigurera utbildnings körningens miljö

Nästa steg är att se till att fjärran sluten utbildning har alla beroenden som krävs av inlärnings stegen. Beroenden och körnings kontexten anges genom att skapa och konfigurera ett `RunConfiguration` objekt. 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import Environment 

aml_run_config = RunConfiguration()
# `compute_target` as defined in "Azure Machine Learning compute" section above
aml_run_config.target = compute_target

USE_CURATED_ENV = True
if USE_CURATED_ENV :
    curated_environment = Environment.get(workspace=ws, name="AzureML-Tutorial")
    aml_run_config.environment = curated_environment
else:
    aml_run_config.environment.python.user_managed_dependencies = False
    
    # Add some packages relied on by data prep step
    aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
        conda_packages=['pandas','scikit-learn'], 
        pip_packages=['azureml-sdk', 'azureml-dataprep[fuse,pandas]'], 
        pin_sdk_version=False)
```

Koden ovan visar två alternativ för att hantera beroenden. Som presenteras `USE_CURATED_ENV = True` baseras konfigurationen på en granskad miljö. Granskade miljöer är "förkortade" med vanliga bibliotek som är beroende av varandra och kan vara mycket snabbare att ta online. De granskade miljöerna har färdiga Docker-avbildningar i [Microsoft container Registry](https://hub.docker.com/publishers/microsoftowner). Mer information finns i [Azure Machine Learning granskade miljöer](resource-curated-environments.md).

Den angivna sökvägen om du ändrar `USE_CURATED_ENV` till `False` visar mönstret för explicit inställning av beroenden. I det scenariot skapas och registreras en ny anpassad Docker-avbildning i en Azure Container Registry i din resurs grupp (se [Introduktion till privata Docker-behållar register i Azure](../container-registry/container-registry-intro.md)). Det kan ta några minuter att skapa och registrera den här avbildningen.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Skapa dina pipeline-steg

När du har skapat beräknings resursen och miljön är du redo att definiera din pipelines steg. Det finns många inbyggda steg som är tillgängliga via Azure Machine Learning SDK, som du kan se i [referens dokumentationen för `azureml.pipeline.steps` paketet](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py). Den mest flexibla klassen är [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?preserve-view=true&view=azure-ml-py), som kör ett Python-skript.

```python
from azureml.pipeline.steps import PythonScriptStep
dataprep_source_dir = "./dataprep_src"
entry_point = "prepare.py"
# `my_dataset` as defined above
ds_input = my_dataset.as_named_input('input1')

# `output_data1`, `compute_target`, `aml_run_config` as defined above
data_prep_step = PythonScriptStep(
    script_name=entry_point,
    source_directory=dataprep_source_dir,
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    inputs=[ds_input],
    outputs=[output_data1],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

Koden ovan visar ett typiskt första pipeline-steg. Din data förberedelse kod finns i en under katalog (i det här exemplet `"prepare.py"` i katalogen `"./dataprep.src"` ). Som en del av processen för att skapa pipelinen är den här katalogen zippad och överförd till `compute_target` och steget Kör skriptet som anges som värde för `script_name` .

`arguments`Värdena, `inputs` och `outputs` anger indata och utdata för steget. I exemplet ovan är data uppsättningen för bas linjen `my_dataset` . Motsvarande data hämtas till beräknings resursen eftersom koden anger den som `as_download()` . Skriptet `prepare.py` utför de data omvandlings aktiviteter som är lämpliga för den aktuella aktiviteten och matar ut data till `output_data1` , av typen `PipelineData` . Mer information finns i [Flytta data till och mellan steg om ml-pipeline (python)](how-to-move-data-in-out-of-pipelines.md). 

Steget kommer att köras på den dator som definieras av `compute_target` , med hjälp av konfigurationen `aml_run_config` . 

Åter användning av tidigare resultat ( `allow_reuse` ) är nyckel när du använder pipelines i en samarbets miljö eftersom det tar onödigt smidigt att ta bort onödig omgång. Åter användning är standard beteendet när script_name, indata och parametrarna för ett steg är desamma. När åter användning tillåts skickas resultatet från föregående körning omedelbart till nästa steg. Om `allow_reuse` är inställt på `False` skapas alltid en ny körning för det här steget under pipeline-körningen.

Det är möjligt att skapa en pipeline med ett enda steg, men nästan alltid väljer du att dela upp den övergripande processen i flera steg. Du kan till exempel ha steg för förberedelse av data, träning, modell jämförelse och distribution. En kan till exempel föreställa sig att efter det som `data_prep_step` anges ovan, kan nästa steg vara utbildning:

```python
train_source_dir = "./train_src"
train_entry_point = "train.py"

training_results = PipelineData(name = "training_results", 
                                datastore=def_blob_store,
                                output_name="training_results")

    
train_step = PythonScriptStep(
    script_name=train_entry_point,
    source_directory=train_source_dir,
    arguments=["--prepped_data", output_data1.as_input(), "--training_results", training_results],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

Ovanstående kod liknar den för steget förberedelse av data. Inlärnings koden finns i en annan katalog än data förberedelse koden. `PipelineData`Utdata från steget förberedelse av data `output_data1` används som _indata_ till övnings steget. Ett nytt `PipelineData` objekt `training_results` skapas för att innehålla resultatet för en efterföljande jämförelse eller ett distributions steg. 


> [!TIP]
> För en förbättrad upplevelse, och möjligheten att skriva mellanliggande data tillbaka till dina data lager i slutet av din pipeline-körning, använder du den offentliga för hands versions klassen [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) . Kod exempel finns i så här [skapar du en pipeline för två steg ml](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb) och [hur du skriver data tillbaka till data lager när körningen är klar](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb).

När du har definierat stegen skapar du pipelinen med hjälp av några eller samtliga av dessa steg.

> [!NOTE]
> Ingen fil eller några data överförs till Azure Machine Learning när du definierar stegen eller skapar pipelinen. Filerna laddas upp när du anropar [experiment. Submit ()](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-).

```python
# list of steps to run (`compare_step` definition not shown)
compare_models = [data_prep_step, train_step, compare_step]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compare_models])
```

### <a name="how-python-environments-work-with-pipeline-parameters"></a>Hur python-miljöer fungerar med pipeline-parametrar

Som tidigare nämnts i [Konfigurera utbildnings körningens miljö](#configure-the-training-runs-environment), anges miljö-och python-bibliotekens beroenden med ett- `Environment` objekt. I allmänhet kan du ange en befintlig `Environment` genom att referera till dess namn och, om du vill, en version:

```python
aml_run_config = RunConfiguration()
aml_run_config.environment.name = 'MyEnvironment'
aml_run_config.environment.version = '1.0'
```

Men om du väljer att använda `PipelineParameter` objekt för att dynamiskt ange variabler vid körning för dina pipeline-steg kan du inte använda den här metoden för att referera till en befintlig `Environment` . Om du i stället vill använda `PipelineParameter` objekt måste du ange `environment` fältet för `RunConfiguration` till ett- `Environment` objekt. Det är ditt ansvar att se till att en sådan `Environment` har sina beroenden för externa python-paket korrekt inställda.

### <a name="use-a-dataset"></a>Använd en data uppsättning 

Data uppsättningar som skapats från Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database och Azure Database for PostgreSQL kan användas som indata till alla pipeline-steg. Du kan skriva utdata till en [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?preserve-view=true&view=azure-ml-py), [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?preserve-view=true&view=azure-ml-py)eller om du vill skriva data till ett bestämt data lager använder [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py). 

> [!IMPORTANT]
> Det går bara att skriva utdata tillbaka till ett data lager med hjälp av PipelineData för Azure blob-och Azure-filresurs-datalager. 
>
> Om du vill skriva tillbaka utdata till Azure Blob, Azure-filresurs, ADLS gen 1-och ADLS gen 2-datalager använder du den offentliga för hands versions klassen [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) .

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Sedan hämtar du data uppsättningen i din pipeline med hjälp av [Run.input_datasets](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueinput-datasets) ord listan.

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Linjen `Run.get_context()` är värda markeringar. Den här funktionen hämtar en `Run` som representerar den aktuella experimentella körningen. I exemplet ovan använder vi den för att hämta en registrerad data uppsättning. En annan vanlig användning av `Run` objektet är att hämta både själva experimentet och arbets ytan där experimentet finns: 

```python
# Within a PythonScriptStep

ws = Run.get_context().experiment.workspace
```

Mer information, inklusive alternativa sätt att skicka och komma åt data, finns i [Flytta data till och mellan steg om ml-pipeline (python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="caching--reuse"></a>Cachelagring & återanvända  

För att optimera och anpassa beteendet för dina pipeliner kan du göra några saker runt cachelagring och åter användning. Du kan till exempel välja att:
+ **Inaktivera standard åter användning av steget Kör utdata** genom att ställa in `allow_reuse=False` under [steg definition](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py). Åter användning är nyckel när du använder pipelines i en samarbets miljö eftersom du tar bort onödiga körningar ger flexibilitet. Du kan dock välja att inte använda.
+ **Framtvinga generering av utdata för alla steg i en körning** med `pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Som standard har `allow_reuse` steg Aktiver ATS och den som `source_directory` anges i steg definitionen hashas. Om skriptet för ett specifikt steg däremot är detsamma ( `script_name` , indata och parametrar) och inget annat i har ändrats, så kommer ` source_directory` utdata från föregående steg att återanvändas, jobbet skickas inte till beräkningen och resultaten från föregående körning är omedelbart tillgängliga för nästa steg i stället.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="submit-the-pipeline"></a>Skicka pipelinen

När du skickar pipelinen kontrollerar Azure Machine Learning beroendena för varje steg och överför en ögonblicks bild av käll katalogen som du har angett. Om ingen käll katalog anges överförs den aktuella lokala katalogen. Ögonblicks bilden lagras också som en del av experimentet i din arbets yta.

> [!IMPORTANT]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
>
> Mer information finns i [ögonblicks bilder](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

När du kör en pipeline första gången Azure Machine Learning:

* Hämtar ögonblicks bilden av projektet till beräknings målet från blob-lagringen som är kopplad till arbets ytan.
* Skapar en Docker-avbildning som motsvarar varje steg i pipelinen.
* Laddar ned Docker-avbildningen för varje steg till beräknings målet från behållar registret.
* Konfigurerar åtkomst till `Dataset` och `PipelineData` objekt. Som `as_mount()` åtkomst läge används säkring för att tillhandahålla virtuell åtkomst. Om Mount inte stöds eller om användaren har angett åtkomst som `as_download()` , kopieras data istället till beräknings målet.

* Kör steget i beräknings målet som anges i steg definitionen. 
* Skapar artefakter, till exempel loggar, STDOUT och stderr, mått och utdata som anges i steget. Dessa artefakter överförs och behålls i användarens standard data lager.

![Diagram över att köra ett experiment som en pipeline](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Mer information finns i referensen till [experiment klassen](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py) .

## <a name="use-pipeline-parameters-for-arguments-that-change-at-inference-time"></a>Använd pipeline-parametrar för argument som ändras vid en fördröjning

## <a name="view-results-of-a-pipeline"></a>Visa resultat för en pipeline

Se listan över alla dina pipeliner och deras körnings information i Studio:

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com).

1. [Visa din arbets yta](how-to-manage-workspace.md#view).

1. Till vänster väljer du **pipelines** för att se alla dina pipeline-körningar.
 ![lista över maskin inlärnings pipeliner](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Välj en enskild pipeline för att se körnings resultaten.

### <a name="git-tracking-and-integration"></a>Git-spårning och integrering

När du startar en utbildning som kör där käll katalogen är en lokal git-lagringsplats, lagras information om lagrings platsen i körnings historiken. Mer information finns i [git-integrering för Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="next-steps"></a>Nästa steg

- Information om hur du delar din pipeline med kollegor eller kunder finns i [publicera maskin inlärnings pipeliner](how-to-deploy-pipelines.md)
- Använd [dessa Jupyter-anteckningsböcker på GitHub](https://aka.ms/aml-pipeline-readme) för att utforska maskin inlärnings pipeliner ytterligare
- Se SDK Reference-hjälpen för [azureml-pipeline-Core-](/python/api/azureml-pipeline-core/?preserve-view=true&view=azure-ml-py) paket och AzureML- [pipeline – steg-](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py) paketet
- Se tips om [hur du](how-to-debug-pipelines.md) felsöker och felsöker pipelines =
- Lär dig att köra notebook-filer genom att följa artikeln [Använda Jupyter-notebooks till att utforska tjänsten](samples-notebooks.md).