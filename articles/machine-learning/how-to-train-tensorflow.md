---
title: Träna och distribuera en TensorFlow-modell
titleSuffix: Azure Machine Learning
description: Lär dig hur du kör TensorFlow-tränings skript i skala med hjälp av Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c25464444abe8b4bc274f71618c62a751143d594
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89648288"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Bygg en TensorFlow djup inlärnings modell i skala med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Den här artikeln visar hur du kör dina [TensorFlow](https://www.tensorflow.org/overview) -utbildnings skript i stor skala med Azure Machine Learning [TensorFlow-uppskattnings](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py&preserve-view=true) klass. Detta exempel tågen och registrerar en TensorFlow modell för att klassificera handskrivna siffror med ett djup neurala-nätverk (DNN).

Oavsett om du utvecklar en TensorFlow-modell från grunden eller om du har en [befintlig modell](how-to-deploy-existing-model.md) i molnet kan du använda Azure Machine Learning för att skala ut utbildnings jobb med öppen källkod för att skapa, distribuera, skapa och övervaka modeller för produktions miljön.

Lär dig mer om [djup inlärning vs Machine Learning](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Krav

Kör den här koden i någon av följande miljöer:

 - Azure Machine Learning beräknings instans – inga hämtningar eller installationer behövs

     - Slutför [självstudien: installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad Notebook-server som är förinstallerad med SDK och exempel lagrings plats.
    - I mappen exempel djup inlärning på Notebook-servern hittar du en slutförd och utökad antecknings bok genom att gå till den här katalogen: **How-to-use-azureml > ml-framework > tensorflow > deployment > träna-parameter-finjustera-Deploy-with-tensorflow-** mappen. 
 
 - Din egen Jupyter Notebook Server

    - [Installera Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).
    - [Skapa en konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).
    - [Hämta exempel skript filen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` särskilt `utils.py`
     
    Du kan också hitta en slutförd [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) av den här guiden på sidan med GitHub-exempel. Antecknings boken innehåller utökade avsnitt som täcker intelligenta parametrar, modell distribution och Notebook-widgetar.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

I det här avsnittet anges övnings experimentet genom att läsa in de nödvändiga python-paketen, initiera en arbets yta, skapa ett experiment och ladda upp utbildnings data och utbildnings skript.

### <a name="import-packages"></a>Importera paket

Importera först de nödvändiga python-biblioteken.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import TensorFlow
```

### <a name="initialize-a-workspace"></a>Initiera en arbets yta

[Azure Machine Learning-arbetsytan](concept-workspace.md) är resursen på den översta nivån för tjänsten. Det ger dig en central plats för att arbeta med alla artefakter som du skapar. I python SDK har du åtkomst till arbets ytans artefakter genom att skapa ett [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) objekt.

Skapa ett objekt för arbets ytan från `config.json` filen som skapats i [avsnittet krav](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Skapa ett djup inlärnings experiment

Skapa ett experiment och en mapp för att lagra dina utbildnings skript. I det här exemplet skapar du ett experiment som kallas "TF-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Skapa en fil data uppsättning

Ett `FileDataset` objekt refererar till en eller flera filer i data lagret för din arbets yta eller offentliga URL: er. Filerna kan vara i valfritt format, och klassen ger dig möjlighet att ladda ned eller montera filerna i din beräkning. Genom att skapa en `FileDataset` skapar du en referens till data käll platsen. Om du har tillämpat eventuella omvandlingar till data uppsättningen lagras de även i data uppsättningen. Data behålls på den befintliga platsen, så ingen extra lagrings kostnad uppstår. Mer information finns i [instruktions](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) guiden `Dataset` för.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Använd `register()` metoden för att registrera data uppsättningen på din arbets yta så att de kan delas med andra, återanvändas över olika experiment och refereras till av namn i ditt utbildnings skript.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>Skapa ett beräknings mål

Skapa ett beräknings mål för ditt TensorFlow-jobb som ska köras. I det här exemplet skapar du ett GPU-aktiverat Azure Machine Learning beräknings kluster.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Mer information om beräknings mål finns i artikeln [Vad är en Compute Target](concept-compute-target.md) -artikel.

## <a name="create-a-tensorflow-estimator"></a>Skapa en TensorFlow-uppskattning

[TensorFlow-uppskattningen](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py&preserve-view=true) ger ett enkelt sätt att starta ett TensorFlow utbildnings jobb på ett beräknings mål.

TensorFlow-uppskattningen implementeras via den generiska [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py&preserve-view=true) klassen, som kan användas för att stödja eventuella ramverk. Mer information om tränings modeller med hjälp av den generiska uppskattningen finns i [träna modeller med Azure Machine Learning med hjälp av uppskattning](how-to-train-ml-models.md)

Om ditt utbildnings skript behöver ytterligare pip-eller Conda-paket för att kunna köras, kan du ha paketen installerade på den resulterande Docker-avbildningen genom att skicka namnen via `pip_packages` `conda_packages` argumenten och.


> [!WARNING]
> Azure Machine Learning kör utbildnings skript genom att kopiera hela käll katalogen. Om du har känsliga data som du inte vill överföra använder du en [. IGNORE-fil](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) eller inkluderar den inte i käll katalogen. I stället kan du komma åt dina data med hjälp av ett data [lager](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true).


```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True,
                 pip_packages=['azureml-dataprep[pandas,fuse]'])
```

> [!TIP]
> Stöd för **Tensorflow 2,0** har lagts till i uppskattnings klassen Tensorflow. Mer information finns i [blogg inlägget](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/) .

Mer information om hur du anpassar din python-miljö finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md). 

## <a name="submit-a-run"></a>Skicka in en körning

[Kör-objektet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) tillhandahåller gränssnittet till körnings historiken medan jobbet körs och när det har slutförts.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

När körningen körs går den igenom följande steg:

- **Förbereder**: en Docker-avbildning skapas enligt TensorFlow-uppskattningen. Avbildningen överförs till arbets ytans behållar register och cachelagras för senare körningar. Loggarna strömmas också till körnings historiken och kan visas för att övervaka förloppet.

- **Skalning**: klustret försöker skala upp om det batch AI klustret kräver fler noder för att köra körning än vad som är tillgängligt.

- **Körs**: alla skript i mappen skript överförs till Compute-målet, data lager monteras eller kopieras och entry_script körs. Utdata från STDOUT och./logs-mappen strömmas till körnings historiken och kan användas för att övervaka körningen.

- **Efter bearbetning**: mappen./outputs i körningen kopieras till körnings historiken.

## <a name="register-or-download-a-model"></a>Registrera eller ladda ned en modell

När du har tränat modellen kan du registrera den på din arbets yta. Med modell registreringen kan du lagra och version av dina modeller i din arbets yta för att förenkla [modell hantering och distribution](concept-model-management-and-deployment.md). Genom att ange parametrarna `model_framework` , `model_framework_version` , och `resource_configuration` , blir modell distribution utan kod tillgängligt. På så sätt kan du distribuera din modell direkt som en webb tjänst från den registrerade modellen, och `ResourceConfiguration` objektet definierar beräknings resursen för webb tjänsten.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Du kan också hämta en lokal kopia av modellen med hjälp av objektet kör. I övnings skriptet `mnist-tf.py` behåller ett TensorFlow-sparfunktionen modellen till en lokal mapp (lokal till beräknings målet). Du kan använda kör-objektet för att ladda ned en kopia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Distribuerad träning

[`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py&preserve-view=true)Uppskattningen stöder också distribuerad utbildning mellan processor-och GPU-kluster. Du kan enkelt köra distribuerade TensorFlow-jobb och Azure Machine Learning hanterar dirigeringen åt dig.

Azure Machine Learning stöder två metoder för distribuerad utbildning i TensorFlow:

- [MPI-baserad](https://www.open-mpi.org/) distribuerad utbildning med [Horovod](https://github.com/uber/horovod) -ramverket
- Inbyggda [distribuerade TensorFlow](https://www.tensorflow.org/deploy/distributed) med hjälp av parameter Server metoden

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) är ett ramverk med öppen källkod för distribuerad utbildning som utvecklas av Uber. Den erbjuder en enkel väg till distribuerade GPU TensorFlow-jobb.

Om du vill använda Horovod anger du ett [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true) objekt för `distributed_training` parametern i TensorFlow-konstruktorn. Den här parametern säkerställer att Horovod-biblioteket installeras för användning i ditt utbildnings skript.

```Python
from azureml.core.runconfig import MpiConfiguration
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])
```

### <a name="parameter-server"></a>Parameter Server

Du kan också köra [inbyggda distribuerade TensorFlow](https://www.tensorflow.org/deploy/distributed)som använder parameter Server modellen. I den här metoden tränar du över ett kluster av parameter servrar och arbetare. Arbets tagarna beräknar Övertoningarna under träningen, medan parameter servrarna sammanställer övertoningar.

Om du vill använda parameter Server metoden anger du ett [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py&preserve-view=true) objekt för `distributed_training` parametern i TensorFlow-konstruktorn.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
tf_est= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Definiera kluster specifikationer i TF_CONFIG

Du behöver också nätverks adresser och portar för klustret för [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec) , så Azure Machine Learning ställer in `TF_CONFIG` miljövariabeln åt dig.

`TF_CONFIG`Miljö variabeln är en JSON-sträng. Här är ett exempel på variabeln för en parameter Server:

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

För TensorFlow på hög nivå [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) , parsar TensorFlow `TF_CONFIG` variabeln och skapar kluster specifikationen åt dig.

Parsa `TF_CONFIG` variabeln och utveckla `tf.train.ClusterSpec` i din utbildnings kod för TensorFlow på lägre nivå för grundläggande API: er för utbildning.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deploy-a-tensorflow-model"></a>Distribuera en TensorFlow-modell

Den modell som du precis har registrerat kan distribueras exakt på samma sätt som andra registrerade modeller i Azure Machine Learning, oavsett vilken uppskattning som du använde för utbildning. Distributions anvisningar innehåller ett avsnitt om att registrera modeller, men du kan hoppa direkt till att [skapa ett beräknings mål](how-to-deploy-and-where.md#choose-a-compute-target) för distribution, eftersom du redan har en registrerad modell.

## <a name="preview-no-code-model-deployment"></a>Förhandsgranskningsvyn Distribution utan kod modell

I stället för den traditionella distributions vägen kan du också använda funktionen utan kod distribution (för hands version) för Tensorflow. Genom att registrera din modell enligt vad som visas ovan med `model_framework` `model_framework_version` parametrarna,, och `resource_configuration` kan du bara använda den `deploy()` statiska funktionen för att distribuera modellen.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Den fullständiga [instruktionen att](how-to-deploy-and-where.md) distribuera i Azure Machine Learning större djup.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tränat och registrerat en TensorFlow-modell och lärt dig om distributions alternativen. Mer information om Azure Machine Learning finns i de här artiklarna.

* [Spåra körnings mått under träning](how-to-track-experiments.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Referens arkitektur för distribuerad djup inlärnings utbildning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
