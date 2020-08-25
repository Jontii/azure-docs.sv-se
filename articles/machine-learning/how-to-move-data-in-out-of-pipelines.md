---
title: Flytta data i ML-pipelines
titleSuffix: Azure Machine Learning
description: Läs om indata & datautdata i Azure Machine Learning pipeliner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 08/20/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 1b6b5af2e6533c13165ae8253813a52b2c7ad261
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2020
ms.locfileid: "88756970"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Flytta data till och mellan olika steg i ML-pipelinen (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Den här artikeln innehåller kod för att importera, transformera och flytta data mellan stegen i en Azure Machine Learning pipeline. En översikt över hur data fungerar i Azure Machine Learning finns i [få åtkomst till data i Azure Storage-tjänster](how-to-access-data.md). För fördelarna och strukturen i Azure Machine Learning pipelines, se [Vad är Azure Machine Learning pipelines?](concept-ml-pipelines.md).

Den här artikeln visar hur du kan:

- Använd `Dataset` objekt för tidigare befintliga data
- Få åtkomst till data i dina steg
- Dela upp `Dataset` data i del mängder, till exempel inlärnings-och validerings under uppsättningar
- Skapa `OutputFileDatasetConfig` objekt för att överföra data till nästa pipeline-steg
- Använd `OutputFileDatasetConfig` objekt som inmatade steg i pipeline-steg
- Skapa nya `Dataset` objekt `OutputFileDatasetConfig` som du vill behålla

> [!NOTE]
>`OutputFileDatasetConfig` `OutputTabularDatasetConfig` Klasserna och är experimentella för hands versions funktioner och kan ändras när som helst.
>
>Mer information finns i https://aka.ms/azuremlexperimental.

## <a name="prerequisites"></a>Krav

Du behöver:

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

- [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller åtkomst till [Azure Machine Learning Studio](https://ml.azure.com/).

- En Azure Machine Learning-arbetsyta.
  
  [Skapa antingen en Azure Machine Learning arbets yta](how-to-manage-workspace.md) eller Använd en befintlig via python SDK. Importera `Workspace` och `Datastore` -klassen och Läs in din prenumerations information från filen `config.json` med hjälp av funktionen `from_config()` . Den här funktionen söker efter JSON-filen i den aktuella katalogen som standard, men du kan också ange en Sök vägs parameter som pekar på filen med hjälp av `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Vissa redan befintliga data. Den här artikeln visar kortfattat användningen av en [Azure Blob-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

- Valfritt: en befintlig maskin inlärnings pipeline, till exempel den som beskrivs i [skapa och köra Machine Learning-pipeliner med Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Använd `Dataset` objekt för tidigare befintliga data 

Det bästa sättet att mata in data i en pipeline är att använda ett [data mängds](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) objekt. `Dataset` objekt representerar beständiga data som är tillgängliga i en arbets yta.

Det finns många sätt att skapa och registrera `Dataset` objekt. Tabell data uppsättningar är för avgränsade data tillgängliga i en eller flera filer. Fil data uppsättningar är för binära data (till exempel bilder) eller för data som du ska parsa. De enklaste sätten att skapa `Dataset` objekt är att använda befintliga blobbar i lagrings utrymmen för arbets ytor eller offentliga URL: er:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

För fler alternativ för att skapa data uppsättningar med olika alternativ och från olika källor, registrera dem och granska dem i Azure Machine Learning användar gränssnitt, förstå hur data storleken interagerar med beräknings kapacitet och hur de versioner kan hanteras, se [skapa Azure Machine Learning data uppsättningar](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Skicka data uppsättningar till ditt skript

Om du vill överföra data uppsättningens sökväg till skriptet använder du `Dataset` objektets `as_named_input()` metod. Du kan antingen skicka det resulterande `DatasetConsumptionConfig` objektet till ditt skript som ett argument, eller genom `inputs` att använda argumentet till ditt pipeline-skript, kan du hämta data uppsättningen med hjälp av `Run.get_context().input_datasets[]` .

När du har skapat en namngiven indatamängd kan du välja dess åtkomst läge: `as_mount()` eller `as_download()` . Om skriptet bearbetar alla filer i data uppsättningen och disken på beräknings resursen är tillräckligt stor för data uppsättningen är nedladdnings åtkomst läget det bästa valet. Nedladdnings åtkomst läget gör att du slipper överföra data direkt vid körning. Om ditt skript har åtkomst till en delmängd av data uppsättningen eller om den är för stor för din beräkning, använder du monterings åtkomst läge. Mer information finns i [montering jämfört med nedladdning](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Skicka en data uppsättning till pipeline-steget:

1. Använd `TabularDataset.as_named_inputs()` eller `FileDataset.as_named_input()` (ingen) i slutet för att skapa ett `DatasetConsumptionConfig` objekt
1. Använda `as_mount()` eller `as_download()` för att ange åtkomst läge
1. Skicka data uppsättningarna till dina pipeline-steg med antingen `arguments` `inputs` argumentet eller

I följande kodfragment visas ett gemensamt mönster för att kombinera de här stegen i `PythonScriptStep` konstruktorn: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

Du kan också använda metoder som `random_split()` och `take_sample()` för att skapa flera indata eller minska mängden data som skickas till pipeline-steget:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Få åtkomst till data uppsättningar i skriptet

Namngivna indata till ditt pipeline-steg skript är tillgängliga som en ord lista i `Run` objektet. Hämta det aktiva `Run` objektet med `Run.get_context()` och hämta sedan ord listan med namngivna indata med hjälp av `input_datasets` . Om du har skickat `DatasetConsumptionConfig` objektet med hjälp av `arguments` argumentet i stället för `inputs` argumentet, kan du komma åt data med hjälp av `ArgParser` kod. Båda metoderna visas i följande kodfragment.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

Det skickade värdet är sökvägen till data uppsättnings fil (er).

Det går också att komma åt en registrerad `Dataset` direkt. Eftersom registrerade data uppsättningar är beständiga och delade i en arbets yta kan du hämta dem direkt:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>Använd `OutputFileDatasetConfig` för mellanliggande data

`Dataset`Objekt som endast representerar beständiga data [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py) kan användas för tillfälliga data utdata från pipeline-steg **och** beständiga utdata. 

 `OutputFileDatasetConfig` objektets standard beteende är att skriva till arbets ytans standard data lager. Skicka dina `OutputFileDatasetConfig` objekt till `PythonScriptStep` med- `arguments` parametern.

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

Du kan välja att ladda upp innehållet i `OutputFileDatasetConfig` objektet i slutet av en körning. I så fall använder du `as_upload()` funktionen tillsammans med `OutputFileDatasetConfig` objektet och anger om du vill skriva över befintliga filer i målet. 

```python
#get blob datastore already registered with the workspace
blob_store= ws.datastores['my_blob_store']
OutputFileDatasetConfig(name="clean_data", destination=blob_store).as_upload(overwrite=False)
```

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>Använd `OutputFileDatasetConfig` som utdata i ett utbildnings steg

I din pipeline `PythonScriptStep` kan du hämta tillgängliga sökvägar med hjälp av programmets argument. Om det här steget är det första och kommer att initiera utdata måste du skapa katalogen på den angivna sökvägen. Du kan sedan skriva de filer som du vill ska ingå i `OutputFileDatasetConfig` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>Läs `OutputFileDatasetConfig` som indata till icke-inledande steg

När det första pipeline-steget skriver vissa data till `OutputFileDatasetConfig` sökvägen och det blir utdata från det inledande steget, kan det användas som indata till ett senare steg. 

I följande kod, 

* `step1_output_data` anger att utdatan från PythonScriptStep `step1` skrivs till ADLS gen 2-datalagret `my_adlsgen2` i överförings åtkomst läge. Läs mer om hur du [ställer in roll behörigheter](how-to-access-data.md#azure-data-lake-storage-generation-2) för att kunna skriva tillbaka data till ADLS gen 2-datalager. 

* När `step1` det är klart och utdata skrivs till målet som anges av `step1_output_data` , är step2 redo att användas `step1_output_data` som indata. 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=datastore).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>Registrera `OutputFileDatasetConfig` objekt för åter användning

Om du vill göra din `OutputFileDatasetConfig` tillgängliga under längre tid än ditt experiment kan du registrera den på arbets ytan för att dela och återanvända alla experiment.

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure Machine Learning-datauppsättning](how-to-create-register-datasets.md)
* [Skapa och kör maskin inlärnings pipeliner med Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
