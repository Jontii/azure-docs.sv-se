---
title: Träna och distribuera en förstärknings inlärnings modell (för hands version).
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure Machine Learning förstärknings inlärning (för hands version) för att träna en HUVUDWEBBADRESS-agent att spela Pong.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: peterlu
author: peterclu
ms.date: 05/05/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: d3a7b04e6169df21ad30017b72f70ff667429b23
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902377"
---
# <a name="reinforcement-learning-preview-with-azure-machine-learning"></a>Förstärka inlärningen (för hands version) med Azure Machine Learning



> [!NOTE]
> Azure Machine Learning förstärknings inlärning är för närvarande en förhands gransknings funktion. Endast Ray-och RLlib-ramverk stöds för tillfället.

I den här artikeln får du lära dig hur du tränar en HUVUDWEBBADRESS-agent (en förstärknings inlärning) för att spela spelets Pong. Du kommer att använda python Library [Ray-RLlib](https://ray.readthedocs.io/en/master/rllib.html) med öppen källkod med Azure Machine Learning för att hantera komplexiteten för DISTRIBUERAde huvudwebbadress-jobb.

I den här artikeln får du lära dig att:
> [!div class="checklist"]
> * Konfigurera ett experiment
> * Definiera Head-och Worker-noder
> * Skapa en HUVUDWEBBADRESS-uppskattning
> * Skicka ett experiment för att starta en körning
> * Visa resultat

Den här artikeln baseras på [RLlib Pong-exemplet](https://aka.ms/azureml-rl-pong) som finns i Azure Machine Learning Notebooks GitHub- [lagringsplats](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md).

## <a name="prerequisites"></a>Förutsättningar

Kör den här koden i någon av följande miljöer. Vi rekommenderar att du försöker Azure Machine Learning beräknings instans för den snabbaste start upplevelsen. De förstärkta exempel antecknings böckerna är tillgängliga för att snabbt kunna klona och köra Azure Machine Learning beräknings instanser.

 - Azure Machine Learning-beräkningsinstans

     - Lär dig att klona exempel antecknings böcker i [Självstudier: installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md).
         - Klona mappen för **att använda azureml** i stället för **självstudier**
     - Kör den virtuella datorn för installation av virtuella nätverk som finns på `/how-to-use-azureml/reinforcement-learning/setup/devenv_setup.ipynb` för att öppna nätverks portar som används för distribuerad förstärknings inlärning.
     - Kör exempel antecknings boken `/how-to-use-azureml/reinforcement-learning/atari-on-distributed-compute/pong_rllib.ipynb`
 
 - Din egen Jupyter Notebook Server

    - Installera [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).
    - Installera [Azure Machine Learning HUVUDWEBBADRESS SDK](https://docs.microsoft.com/python/api/azureml-contrib-reinforcementlearning/?view=azure-ml-py&preserve-view=true): `pip install --upgrade azureml-contrib-reinforcementlearning`
    - Skapa en [konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).
    - Kör den virtuella [datorn för installation](https://aka.ms/azure-rl-env-setup) av virtuella nätverk för att öppna nätverks portar som används för distribuerad förstärknings inlärning.


## <a name="how-to-train-a-pong-playing-agent"></a>Hur man tränar en pong-agent

Förstärknings inlärning (HUVUDWEBBADRESS) är en metod för maskin inlärning som lär sig genom att göra. Även om andra Machine Learning-tekniker lär sig att passivt mata in data och hitta mönster i IT, använder HUVUDWEBBADRESS **utbildnings agenter** för att aktivt fatta beslut och lära sig från sina resultat.

Dina utbildnings agenter lär sig att spela Pong i en **simulerad miljö**. Utbildnings agenter fattar ett beslut varje bild ruta i spelet för att flytta Paddle uppåt, nedåt eller stanna på plats. Det tittar på spelets tillstånd (en RGB-bild av skärmen) för att fatta ett beslut.

HUVUDWEBBADRESS använder **belöningar** för att berätta om agenten om besluten har lyckats. I den här miljön får agenten en positiv belöning när den får en punkt och en negativ belöning när en punkt poängas mot den. I många iterationer lär sig utbildnings agenten att välja åtgärden, baserat på dess aktuella status, som optimerar för summan av förväntade framtida förmåner.

Det är vanligt att använda en DNN-modell ( **djup neurala Network** ) för att utföra den här optimeringen i huvudwebbadress. Inlednings vis fungerar inlärnings agenten dåligt, men alla spel genererar ytterligare exempel för att ytterligare förbättra modellen.

Utbildningen upphör när agenten uppnår en genomsnittlig belönings poäng på 18 i ett utbildnings värde. Det innebär att agenten har beaten sin motspelare genom att ett genomsnitt på minst 18 punkter i matchar upp till 21.

Processen att gå igenom simuleringen och återträna en DNN är mycket kostsam och kräver stora mängder data. Ett sätt att förbättra prestanda för HUVUDWEBBADRESS-jobb är att minska **arbetet** så att flera utbildnings agenter kan agera och lära sig samtidigt. Att hantera en distribuerad HUVUDWEBBADRESS-miljö kan dock vara ett komplext företag.

Azure Machine Learning tillhandahåller ramverket för att hantera dessa komplexa funktioner för att skala ut dina HUVUDWEBBADRESS-arbetsbelastningar.

## <a name="set-up-the-environment"></a>Konfigurera miljön

Konfigurera den lokala HUVUDWEBBADRESS-miljön genom att läsa in de nödvändiga python-paketen, initiera arbets ytan, skapa ett experiment och ange ett konfigurerat virtuellt nätverk.

### <a name="import-libraries"></a>Importera bibliotek

Importera de python-paket som krävs för att köra resten av det här exemplet.

```python
# Azure ML Core imports
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
from azureml.core.runconfig import EnvironmentDefinition
from azureml.widgets import RunDetails
from azureml.tensorboard import Tensorboard

# Azure ML Reinforcement Learning imports
from azureml.contrib.train.rl import ReinforcementLearningEstimator, Ray
from azureml.contrib.train.rl import WorkerConfiguration
```

### <a name="initialize-a-workspace"></a>Initiera en arbets yta

[Azure Machine Learning-arbetsytan](concept-workspace.md) är den översta resursen för Azure Machine Learning. Det ger dig en central plats för att arbeta med alla artefakter som du skapar.

Initiera ett objekt för arbets ytan från `config.json` filen som skapats i [avsnittet krav](#prerequisites). Om du kör den här koden i en Azure Machine Learning beräknings instans har konfigurations filen redan skapats åt dig.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-reinforcement-learning-experiment"></a>Skapa ett förstärkt inlärnings experiment

Skapa ett [experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true) för att spåra din effektiviserade inlärnings körning. I Azure Machine Learning är experiment logiska samlingar av relaterade försök för att organisera körnings loggar, historik, utdata och mycket annat.

```python
experiment_name='rllib-pong-multi-node'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="specify-a-virtual-network"></a>Ange ett virtuellt nätverk

För HUVUDWEBBADRESS-jobb som använder flera beräknings mål måste du ange ett virtuellt nätverk med öppna portar som tillåter att arbetsnoder och huvudnoder kan kommunicera med varandra. Det virtuella nätverket kan finnas i vilken resurs grupp som helst, men det bör finnas i samma region som din arbets yta. Mer information om hur du konfigurerar ditt virtuella nätverk finns i [installations programmet för arbets ytan installation](https://aka.ms/azure-rl-env-setup) som kan hittas i avsnittet krav. Här anger du namnet på det virtuella nätverket i resurs gruppen.

```python
vnet = 'your_vnet'
```

## <a name="define-head-and-worker-compute-targets"></a>Definiera mål för Head-och Worker-beräkning

I det här exemplet används separata beräknings mål för noderna för Ray-Head och Worker. Med de här inställningarna kan du skala dina beräknings resurser upp och ned beroende på den förväntade arbets belastningen. Ange antalet noder och storleken på varje nod utifrån experimentets behov.

### <a name="head-computing-target"></a>Huvud dator mål

I det här exemplet används ett GPU-utrustat huvud kluster för att optimera prestanda för djup inlärning. Head-noden tågen neurala-nätverket som agenten använder för att fatta beslut. Head-noden samlar även in data punkter från arbetsnoderna för att ytterligare träna neurala-nätverket.

Huvud beräkningen använder en enda [ `STANDARD_NC6` virtuell dator](https://docs.microsoft.com/azure/virtual-machines/nc-series) (VM). Den har 6 virtuella processorer, vilket innebär att den kan distribuera arbete över 6 fungerande processorer.


```python
from azureml.core.compute import AmlCompute, ComputeTarget

# choose a name for the Ray head cluster
head_compute_name = 'head-gpu'
head_compute_min_nodes = 0
head_compute_max_nodes = 2

# This example uses GPU VM. For using CPU VM, set SKU to STANDARD_D2_V2
head_vm_size = 'STANDARD_NC6'

if head_compute_name in ws.compute_targets:
    head_compute_target = ws.compute_targets[head_compute_name]
    if head_compute_target and type(head_compute_target) is AmlCompute:
        print(f'found head compute target. just use it {head_compute_name}')
else:
    print('creating a new head compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = head_vm_size,
                                                                min_nodes = head_compute_min_nodes, 
                                                                max_nodes = head_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    head_compute_target = ComputeTarget.create(ws, head_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    head_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(head_compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="worker-computing-cluster"></a>Arbets kluster för arbete

I det här exemplet används fyra [ `STANDARD_D2_V2` virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/nc-series) för arbets beräknings målet. Varje arbetsnod har 2 tillgängliga processorer för totalt 8 tillgängliga CPU: er som parallellisera fungerar.

GPU: er behövs inte för arbetsnoder eftersom de inte utför djup inlärning. Arbets tagarna kör spel simuleringarna och samlar in data.

```python
# choose a name for your Ray worker cluster
worker_compute_name = 'worker-cpu'
worker_compute_min_nodes = 0 
worker_compute_max_nodes = 4

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
worker_vm_size = 'STANDARD_D2_V2'

# Create the compute target if it hasn't been created already
if worker_compute_name in ws.compute_targets:
    worker_compute_target = ws.compute_targets[worker_compute_name]
    if worker_compute_target and type(worker_compute_target) is AmlCompute:
        print(f'found worker compute target. just use it {worker_compute_name}')
else:
    print('creating a new worker compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = worker_vm_size,
                                                                min_nodes = worker_compute_min_nodes, 
                                                                max_nodes = worker_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    worker_compute_target = ComputeTarget.create(ws, worker_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    worker_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(worker_compute_target.get_status().serialize())
```

## <a name="create-a-reinforcement-learning-estimator"></a>Skapa en uppskattad uppskattnings utbildning

I det här avsnittet får du lära dig hur du använder [ReinforcementLearningEstimator](https://docs.microsoft.com/python/api/azureml-contrib-reinforcementlearning/azureml.contrib.train.rl.reinforcementlearningestimator?view=azure-ml-py&preserve-view=true) för att skicka ett utbildnings jobb till Azure Machine Learning.

Azure Machine Learning använder uppskattnings klasser för att kapsla in kör konfigurations information. På så sätt kan du enkelt ange hur skript körningen ska konfigureras. Mer information om mönster för Azure Machine Learning uppskattningar finns i [så här tränar du modeller med hjälp av uppskattningar](how-to-train-ml-models.md).

### <a name="define-a-worker-configuration"></a>Definiera en Worker-konfiguration

WorkerConfiguration-objektet visar Azure Machine Learning hur du initierar det arbets kluster som ska köra Entry-skriptet.

```python
# Pip packages we will use for both head and worker
pip_packages=["ray[rllib]==0.8.3"] # Latest version of Ray has fixes for isses related to object transfers

# Specify the Ray worker configuration
worker_conf = WorkerConfiguration(
    
    # Azure ML compute cluster to run Ray workers
    compute_target=worker_compute_target, 
    
    # Number of worker nodes
    node_count=4,
    
    # GPU
    use_gpu=False, 
    
    # PIP packages to use
    pip_packages=pip_packages
)
```

### <a name="define-script-parameters"></a>Definiera skript parametrar

I Entry-skriptet `pong_rllib.py` accepteras en lista över parametrar som definierar hur du kör utbildnings jobbet. Genom att skicka parametrarna via uppskattaren som ett lager med inkapsling är det enkelt att ändra skript parametrar och köra konfigurationer oberoende av varandra.

Att ange rätt `num_workers` kommer att få ut mesta möjliga av dina parallellisering-ansträngningar. Ange antalet arbetare till samma som antalet tillgängliga processorer. I det här exemplet kan du beräkna detta på följande sätt:

Head-noden är en [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) med 6 virtuella processorer. Arbets klustret är 4 [Standard_D2_V2 virtuella datorer](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs#dv2-series) med 2 processorer per, för totalt 8 processorer. Du måste dock subtrahera 1 processor från antalet arbetare sedan 1 måste vara dedikerat till Head-nodens roll. 6 processorer + 8 processorer – 1 Head CPU = 13 samtidiga arbetare. Azure Machine Learning använder huvud-och arbets kluster för att särskilja beräknings resurser. Dock skiljer sig inte mellan huvud och arbetare, och alla CPU: er är tillgängliga processorer för körning av arbets trådar.


```python
training_algorithm = "IMPALA"
rl_environment = "PongNoFrameskip-v4"

# Training script parameters
script_params = {
    
    # Training algorithm, IMPALA in this case
    "--run": training_algorithm,
    
    # Environment, Pong in this case
    "--env": rl_environment,
    
    # Add additional single quotes at the both ends of string values as we have spaces in the 
    # string parameters, outermost quotes are not passed to scripts as they are not actually part of string
    # Number of GPUs
    # Number of ray workers
    "--config": '\'{"num_gpus": 1, "num_workers": 13}\'',
    
    # Target episode reward mean to stop the training
    # Total training time in seconds
    "--stop": '\'{"episode_reward_mean": 18, "time_total_s": 3600}\'',
}
```

### <a name="define-the-reinforcement-learning-estimator"></a>Definiera uppskattningen för förstärknings inlärning

Använd parameter listan och Work Configuration-objektet för att skapa en uppskattning.

```python
# RL estimator
rl_estimator = ReinforcementLearningEstimator(
    
    # Location of source files
    source_directory='files',
    
    # Python script file
    entry_script="pong_rllib.py",
    
    # Parameters to pass to the script file
    # Defined above.
    script_params=script_params,
    
    # The Azure ML compute target set up for Ray head nodes
    compute_target=head_compute_target,
    
    # Pip packages
    pip_packages=pip_packages,
    
    # GPU usage
    use_gpu=True,
    
    # RL framework. Currently must be Ray.
    rl_framework=Ray(),
    
    # Ray worker configuration defined above.
    worker_configuration=worker_conf,
    
    # How long to wait for whole cluster to start
    cluster_coordination_timeout_seconds=3600,
    
    # Maximum time for the whole Ray job to run
    # This will cut off the run after an hour
    max_run_duration_seconds=3600,
    
    # Allow the docker container Ray runs in to make full use
    # of the shared memory available from the host OS.
    shm_size=24*1024*1024*1024
)
```

### <a name="entry-script"></a>Post skript

[Entry-skriptet](https://aka.ms/azure-rl-pong-script) `pong_rllib.py` tågen ett neurala-nätverk med hjälp av [OpenAI gymmet-miljön](https://github.com/openai/gym/) `PongNoFrameSkip-v4` . OpenAI gyms är standardiserade gränssnitt för att testa förstärka inlärnings algoritmer på klassiska Atari-spel.

I det här exemplet används en Impala som kallas för [IMPALA](https://arxiv.org/abs/1802.01561) (vikten viktad skådespelare – elevens arkitektur). IMPALA parallelizes varje enskild utbildnings aktör som ska skalas över flera datornoder utan att offra hastighet eller stabilitet.

[Ray finjustera](https://ray.readthedocs.io/en/latest/tune.html) dirigerar Impala Worker-uppgifter.

```python
import ray
import ray.tune as tune
from ray.rllib import train

import os
import sys

from azureml.core import Run
from utils import callbacks

DEFAULT_RAY_ADDRESS = 'localhost:6379'

if __name__ == "__main__":

    # Parse arguments
    train_parser = train.create_parser()

    args = train_parser.parse_args()
    print("Algorithm config:", args.config)

    if args.ray_address is None:
        args.ray_address = DEFAULT_RAY_ADDRESS

    ray.init(address=args.ray_address)

    tune.run(run_or_experiment=args.run,
             config={
                 "env": args.env,
                 "num_gpus": args.config["num_gpus"],
                 "num_workers": args.config["num_workers"],
                 "callbacks": {"on_train_result": callbacks.on_train_result},
                 "sample_batch_size": 50,
                 "train_batch_size": 1000,
                 "num_sgd_iter": 2,
                 "num_data_loader_buffers": 2,
                 "model": {
                    "dim": 42
                 },
             },
             stop=args.stop,
             local_dir='./logs')
```

### <a name="logging-callback-function"></a>Logga callback-funktionen


I Entry-skriptet används en verktygs funktion för att definiera en [anpassad RLlib-callback-funktion](https://docs.ray.io/en/latest/rllib-training.html#callbacks-and-custom-metrics) som loggar mått till din Azure Machine Learning-arbetsyta. Lär dig hur du visar dessa mått i avsnittet [övervaka och Visa resultat](#monitor-and-view-results) .

```python
'''RLlib callbacks module:
    Common callback methods to be passed to RLlib trainer.
'''
from azureml.core import Run

def on_train_result(info):
    '''Callback on train result to record metrics returned by trainer.
    '''
    run = Run.get_context()
    run.log(
        name='episode_reward_mean',
        value=info["result"]["episode_reward_mean"])
    run.log(
        name='episodes_total',
        value=info["result"]["episodes_total"])
```

## <a name="submit-a-run"></a>Skicka in en körning

[Kör](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true) hanterar körnings historiken för pågående eller fullständiga jobb. 

```python
run = exp.submit(config=rl_estimator)
```
> [!NOTE]
> Det kan ta upp till 30 till 45 minuter att slutföra körningen.

## <a name="monitor-and-view-results"></a>Övervaka och Visa resultat

Använd widgeten Azure Machine Learning Jupyter för att se status för dina körningar i real tid. I det här exemplet visar widgeten två underordnade körningar: en för Head och en för arbetare. 

```python
from azureml.widgets import RunDetails

RunDetails(run).show()
run.wait_for_completion()
```

1. Vänta tills widgeten har lästs in.
1. Välj huvud körningen i listan över körningar.

Välj **Klicka här för att se körningen i Azure Machine Learning Studio** för ytterligare körnings information i Studio. Du kan komma åt den här informationen medan körningen pågår eller när den har slutförts.

![Linje diagram som visar hur widgeten kör information](./media/how-to-use-reinforcement-learning/pong-run-details-widget.png)

I **episode_reward_means** området visas medelvärdet för antalet poäng per utbildning. Du kan se att utbildnings agenten ursprungligen utfördes dåligt och förlorar sina matchningar utan att behöva göra en enstaka punkt (visas med en reward_mean på-21). Inom 100 iterationer har utbildnings agenten lärt sig att takta dator motspelaren med ett genomsnitt på 18 punkter.

Om du bläddrar i loggar för den underordnade körningen kan du se utvärderings resultaten som registrerats i driver_log.txt-filen. Du kan behöva vänta flera minuter innan dessa mått blir tillgängliga på körnings sidan.

I korthet har du lärt dig att konfigurera flera beräknings resurser för att träna en förstärkt inlärnings agent för att spela Pong mycket bra.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du tränar en förstärknings utbildnings agent med hjälp av en IMPALA Learning-agent. Om du vill se fler exempel går du till [GitHub-lagringsplatsen Azure Machine Learning förstärka Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md).
