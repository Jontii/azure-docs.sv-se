---
title: Använda beräknings mål för modell träning
titleSuffix: Azure Machine Learning
description: Konfigurera utbildnings miljöer (beräknings mål) för maskin inlärnings modell träning. Du kan enkelt växla mellan utbildnings miljöer. Börja träna lokalt. Om du behöver skala ut växlar du till ett moln baserat beräknings mål.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: e83faee7d72026dafc50b21d0a0773e663e5a03a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933127"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Konfigurera och Använd Compute-mål för modell träning 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Med Azure Machine Learning kan du träna din modell på en mängd olika resurser eller miljöer, som sammankallas för [__beräknings mål__](concept-azure-machine-learning-architecture.md#compute-targets). Ett beräknings mål kan vara en lokal dator eller en moln resurs, t. ex. en Azure Machine Learning Compute, Azure HDInsight eller en virtuell dator.  Du kan också skapa beräknings mål för modell distribution enligt beskrivningen i ["var och hur du distribuerar dina modeller"](how-to-deploy-and-where.md).

Du kan skapa och hantera ett beräknings mål med hjälp av Azure Machine Learning SDK, Azure Machine Learning Studio, Azure CLI eller Azure Machine Learning VS Code Extension. Om du har beräknings mål som har skapats via en annan tjänst (till exempel ett HDInsight-kluster) kan du använda dem genom att koppla dem till din Azure Machine Learning-arbetsyta.
 
I den här artikeln får du lära dig hur du använder olika beräknings mål för modell träning.  Stegen för alla beräknings mål följer samma arbets flöde:
1. __Skapa__ ett beräknings mål om du inte redan har ett.
2. __Koppla__ beräknings målet till din arbets yta.
3. __Konfigurera__ beräknings målet så att det innehåller python-miljön och paket beroenden som krävs av ditt skript.


>[!NOTE]
> Koden i den här artikeln har testats med Azure Machine Learning SDK-version 1.0.74.

## <a name="compute-targets-for-training"></a>Compute-mål för utbildning

Azure Machine Learning har varierande stöd för olika beräknings mål. En typisk modell utvecklings livs cykel börjar med utveckling/experiment på en liten mängd data. I det här skedet rekommenderar vi att du använder en lokal miljö. Till exempel din lokala dator eller en molnbaserad virtuell dator. När du skalar din utbildning på större data uppsättningar eller utför distribuerad träning rekommenderar vi att du använder Azure Machine Learning Compute för att skapa ett kluster med en eller flera noder som autoskalar varje gång du skickar en körning. Du kan också koppla din egen beräknings resurs, även om stöd för olika scenarier kan variera enligt beskrivningen nedan:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning beräknings kluster kan skapas som en permanent resurs eller skapas dynamiskt när du begär en körning. Med körnings-baserad skapande tas beräknings målet bort när övnings körningen är klar, så du kan inte återanvända beräknings målen som skapats på det här sättet.

## <a name="whats-a-run-configuration"></a>Vad är en körnings konfiguration?

När det gäller utbildning är det vanligt att starta på den lokala datorn och senare köra det utbildnings skriptet på ett annat Compute-mål. Med Azure Machine Learning kan du köra skriptet på olika beräknings mål utan att behöva ändra ditt skript.

Allt du behöver göra är att definiera miljön för varje beräknings mål i en **körnings konfiguration**.  När du sedan vill köra ditt utbildnings experiment på ett annat beräknings mål anger du körnings konfigurationen för den beräkningen. Information om hur du anger en miljö och binder den till att köra konfigurationen finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md).

Läs mer om hur du [skickar experiment](#submit) i slutet av den här artikeln.

## <a name="whats-an-estimator"></a>Vad är en uppskattning?

För att under lätta modell utbildningen med populära ramverk, tillhandahåller Azure Machine Learning python SDK en alternativ högre abstraktion, klassen uppskattning.  Med den här klassen kan du enkelt konstruera kör konfigurationer. Du kan skapa och använda en generisk [uppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) för att skicka utbildnings skript som använder valfritt ramverk för inlärning (till exempel scikit – lära). Vi rekommenderar att du använder en uppskattning för utbildning eftersom den automatiskt konstruerar inbäddade objekt som en miljö eller RunConfiguration objekt åt dig. Om du vill ha mer kontroll över hur dessa objekt skapas och ange vilka paket som ska installeras för att köra experimentet, följer du [dessa steg](#amlcompute) för att skicka dina utbildnings experiment med ett RunConfiguration-objekt på en Azure Machine Learning-beräkning.

Azure Machine Learning tillhandahåller vissa uppskattningar för [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)-, [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)-, [kedje](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)-och [Ray-RLlib](how-to-use-reinforcement-learning.md).

Mer information finns i [träna ml-modeller med uppskattningar](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>Vad är en ML-pipeline?

Med ML-pipelines kan du optimera arbets flödet med enkelhet, hastighet, portabilitet och åter användning. När du skapar pipeliner med Azure Machine Learning kan du fokusera på din expertis, maskin inlärning i stället för infrastruktur och automatisering.

ML pipelines skapas från flera **steg**, som är distinkta beräknings enheter i pipelinen. Varje steg kan köras oberoende och använda isolerade beräknings resurser. Den här metoden gör det möjligt för flera data experter att arbeta med samma pipeline samtidigt utan beskattnings beräknings resurser och gör det också enkelt att använda olika beräknings typer/storlekar för varje steg.

> [!TIP]
> ML pipelines kan använda kör konfiguration eller uppskattningar när du tränar modeller.

Även om ML pipelines kan träna modeller, kan de också förbereda data innan de tränas och distribuera modeller efter utbildning. En av de främsta användnings fallen för pipelines är batch-poäng. Mer information finns i [pipelines: optimera Machine Learning-arbetsflöden](concept-ml-pipelines.md).

## <a name="set-up-in-python"></a>Konfigurera i python

Använd avsnitten nedan för att konfigurera dessa beräknings mål:

* [Lokal dator](#local)
* [Azure Machine Learning beräknings kluster](#amlcompute)
* [Azure Machine Learning-beräkningsinstans](#instance)
* [Virtuella fjärrdatorer](#vm)
* [Azure HDInsight](#hdinsight)


### <a name="local-computer"></a><a id="local"></a>Lokal dator

1. **Skapa och koppla**: du behöver inte skapa eller ansluta ett beräknings mål för att använda din lokala dator som tränings miljö.  

1. **Konfigurera**: när du använder den lokala datorn som ett beräknings mål körs inlärnings koden i [utvecklings miljön](how-to-configure-environment.md).  Om den miljön redan har de python-paket som du behöver använder du den användar hanterade miljön.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Nu när du har kopplat beräkningen och konfigurerat din körning är nästa steg att [Skicka utbildningen](#submit).

### <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Azure Machine Learning beräknings kluster

Azure Machine Learning Compute Cluster är en hanterad beräknings infrastruktur som gör att du enkelt kan skapa en beräkning med en enda eller flera noder. Beräkningen skapas i arbets ytans region som en resurs som kan delas med andra användare i din arbets yta. Beräkningen skalas upp automatiskt när ett jobb skickas och kan placeras i ett Azure-Virtual Network. Beräkningen körs i en behållare miljö och paketerar dina modell beroenden i en [Docker-behållare](https://www.docker.com/why-docker).

Du kan använda Azure Machine Learning Compute för att distribuera inlärnings processen över ett kluster av processor-eller GPU-datornoder i molnet. Mer information om de VM-storlekar som innehåller GPU: er finns i [GPU-optimerade storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu). 

Azure Machine Learning Compute har standard gränser, till exempel antalet kärnor som kan allokeras. Mer information finns i [Hantera och begära kvoter för Azure-resurser](how-to-manage-quotas.md).


> [!TIP]
> Kluster kan i allmänhet skala upp till 100 noder så länge som du har tillräckligt med kvot för antalet kärnor som krävs. Som standard konfigureras kluster för kommunikation mellan noder mellan noderna i klustret som stöd för MPI-jobb till exempel. Du kan dock skala dina kluster till tusentals noder genom att bara [höja ett support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)och begära att tillåta att en lista över din prenumeration, arbets yta eller ett särskilt kluster inaktive ras i kommunikation mellan noder. 

Azure Machine Learning Compute kan återanvändas över körningar. Beräkningen kan delas med andra användare på arbets ytan och bevaras mellan körningar och automatiskt skalar noderna uppåt eller nedåt baserat på antalet körningar som skickats och max_nodes som angetts i klustret. Inställningen min_nodes styr de minsta tillgängliga noderna.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **Skapa och koppla**: om du vill skapa en beständig Azure Machine Learning beräknings resurs i python anger du **vm_size** och **max_nodes** egenskaper. Azure Machine Learning använder sedan smarta standardinställningar för de andra egenskaperna. Beräkningen skalas ned till noll noder när den inte används.   Dedikerade virtuella datorer skapas för att köra jobben efter behov.
    
    * **vm_size**: VM-serien för noderna som skapats av Azure Machine Learning Compute.
    * **max_nodes**: det högsta antalet noder som autoskalar upp till när du kör ett jobb på Azure Machine Learning beräkning.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Du kan också konfigurera flera avancerade egenskaper när du skapar Azure Machine Learning Compute. Med egenskaperna kan du skapa ett beständigt kluster med fast storlek eller inom en befintlig Azure-Virtual Network i din prenumeration.  Mer information finns i [AmlCompute-klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) .

    Eller så kan du skapa och koppla en beständig Azure Machine Learning beräknings resurs i [Azure Machine Learning Studio](#portal-create).

   
1. **Konfigurera**: skapa en körnings konfiguration för det beständiga beräknings målet.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Nu när du har kopplat beräkningen och konfigurerat din körning är nästa steg att [Skicka utbildningen](#submit).

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Sänk din beräknings kluster kostnad

Du kan också välja att använda [virtuella datorer med låg prioritet](concept-plan-manage-cost.md#low-pri-vm) för att köra vissa eller alla arbets belastningar. De här virtuella datorerna har inte garanterad tillgänglighet och kan komma att blockeras när den används. Ett väntande jobb startas om, inte återupptas. 

Använd något av följande sätt för att ange en virtuell dator med låg prioritet:
    
* I Studio väljer du **låg prioritet** när du skapar en virtuell dator.
    
* Med python SDK anger du `vm_priority` attributet i etablerings konfigurationen.  
    
    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                vm_priority='lowpriority',
                                                                max_nodes=4)
    ```
    
* Använd CLI och ange `vm-priority` :
    
    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 ### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Konfigurera hanterad identitet

 Azure Machine Learning beräknings kluster stöder även [hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) för att autentisera åtkomsten till Azure-resurser utan att inkludera autentiseringsuppgifter i din kod. Det finns två typer av hanterade identiteter:

* En **systemtilldelad hanterad identitet** aktive ras direkt i Azure Machine Learning beräknings kluster. Livs cykeln för en tilldelad identitet är direkt knuten till beräknings klustret. Om beräknings klustret tas bort rensar Azure automatiskt autentiseringsuppgifterna och identiteten i Azure AD.
* En **användare som tilldelats en hanterad identitet** är en fristående Azure-resurs som tillhandahålls via Azure Managed Identity service. Du kan tilldela en användardefinierad hanterad identitet till flera resurser och den finns kvar så länge du vill.

Använd något av följande sätt för att ange en hanterad identitet för ett beräknings kluster:
    
* I Studio, när du skapar beräknings kluster eller när du redigerar beräknings kluster information, växlar du till **tilldela en hanterad identitet** och anger en tilldelad identitet eller användardefinierad identitet.
    
* Med python SDK anger du `identity_type` attributet i etablerings konfigurationen.  
    
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )

    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

* Med python SDK ställer du in `identity_type` och `identity_id` (om du skapar en användardefinierad hanterad identitet) i din etablerings konfiguration.  
    
    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")

    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```
    
* Använd CLI för att ange `assign-identity` attributet när klustret skapas:
    
    ```azurecli
    # create a cluster with a user-assigned managed identity
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'

    # create a cluster with a system-managed identity
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'

* Using the CLI, execute the following commands to assign a managed identity on an existing cluster:
    
    ```azurecli
    # add a user-assigned managed identity
    az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'

    # add a system-assigned managed identity
    az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'

> [!NOTE]
> Azure Machine Learning compute clusters support only **one system-assigned identity** or **multiple user-assigned identities**, not both concurrently.
> 
> Additionally, you can assign only one managed identity from the studio.

#### Managed identity usage

AML defines the **default managed identity** as the system-assigned managed identity or the first user-assigned managed identity.

During a run there are two applications of an identity:
1. The system uses an identity to setup the user's storage mounts, container registry, and datastores.
    * In this case, the system will use the default managed identity.

1. The user applies an identity to access resources from within the code for a submitted run
    
    * In this case, the user must provide the *client_id* corresponding to the managed identity they want to use to retrieve a credential. 
    * Alternatively, AML exposes the user-assigned identity's client ID through the *DEFAULT_IDENTITY_CLIENT_ID* environment variable.
    
    For example, to retrieve a token for a datastore with the default managed identity:
    
    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')



### <a id="instance"></a>Azure Machine Learning compute instance

[Azure Machine Learning compute instance](concept-compute-instance.md) is a managed-compute infrastructure that allows you to easily create a single VM. The compute is created within your workspace region, but unlike a compute cluster, an instance cannot be shared with other users in your workspace. Also the instance does not automatically scale down.  You must stop the resource to prevent ongoing charges.

A compute instance can run multiple jobs in parallel and has a job queue. 

Compute instances can run jobs securely in a [virtual network environment](how-to-enable-virtual-network.md#compute-instance), without requiring enterprises to open up SSH ports. The job executes in a containerized environment and packages your model dependencies in a Docker container. 

1. **Create and attach**: 
    
    ```python
    import datetime
    import time
    
    from azureml.core.compute import ComputeTarget, ComputeInstance
    from azureml.core.compute_target import ComputeTargetException
    
    # Choose a name for your instance
    # Compute instance name should be unique across the azure region
    compute_name = "ci{}".format(ws._workspace_id)[:10]
    
    # Verify that instance does not exist already
    try:
        instance = ComputeInstance(workspace=ws, name=compute_name)
        print('Found existing instance, use it.')
    except ComputeTargetException:
        compute_config = ComputeInstance.provisioning_configuration(
            vm_size='STANDARD_D3_V2',
            ssh_public_access=False,
            # vnet_resourcegroup_name='<my-resource-group>',
            # vnet_name='<my-vnet-name>',
            # subnet_name='default',
            # admin_user_ssh_public_key='<my-sshkey>'
        )
        instance = ComputeInstance.create(ws, compute_name, compute_config)
        instance.wait_for_completion(show_output=True)
    ```

1. **Konfigurera**: skapa en körnings konfiguration.
    
    ```python
    
    from azureml.core import ScriptRunConfig
    from azureml.core.runconfig import DEFAULT_CPU_IMAGE
    
    src = ScriptRunConfig(source_directory='', script='train.py')
    
    # Set compute target to the one created in previous step
    src.run_config.target = instance
    
    # Set environment
    src.run_config.environment = myenv
     
    run = experiment.submit(config=src)
    ```

Nu när du har kopplat beräkningen och konfigurerat din körning är nästa steg att [Skicka utbildningen](#submit)


### <a name="remote-virtual-machines"></a><a id="vm"></a>Virtuella fjärrdatorer

Azure Machine Learning också stöd för att ta med din egen beräknings resurs och koppla den till din arbets yta. En sådan resurs typ är en godtycklig virtuell fjärrdator, så länge den är tillgänglig från Azure Machine Learning. Resursen kan vara en virtuell Azure-dator, en fjärrserver i din organisation eller lokalt. Mer specifikt, baserat på IP-adress och autentiseringsuppgifter (användar namn och lösen ord eller SSH-nyckel), kan du använda valfri tillgänglig virtuell dator för fjärrkörningar.

Du kan använda en systemskapad Conda-miljö, en redan befintlig python-miljö eller en Docker-behållare. Om du vill köra på en Docker-behållare måste du ha en Docker-motor som körs på den virtuella datorn. Den här funktionen är särskilt användbar när du vill ha en mer flexibel, molnbaserad utvecklings-eller experiment miljö än din lokala dator.

Använd Azure-Data Science Virtual Machine (DSVM) som den virtuella Azure-dator som du väljer för det här scenariot. Den här virtuella datorn är en förkonfigurerad miljö för data vetenskap och AI-utveckling i Azure. Den virtuella datorn innehåller ett granskat val av verktyg och ramverk för hela livs cykeln för Machine Learning-utveckling. Mer information om hur du använder DSVM med Azure Machine Learning finns i [Konfigurera en utvecklings miljö](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Skapa**: skapa en DSVM innan du använder den för att träna din modell. Information om hur du skapar den här resursen finns i [etablera data science Virtual Machine för Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning stöder bara virtuella datorer som kör **Ubuntu**. När du skapar en virtuell dator eller väljer en befintlig virtuell dator måste du välja en virtuell dator som använder Ubuntu.
    > 
    > Azure Machine Learning kräver också att den virtuella datorn har en __offentlig IP-adress__.

1. **Bifoga**: om du vill koppla en befintlig virtuell dator som ett beräknings mål måste du ange resurs-ID, användar namn och lösen ord för den virtuella datorn. Resurs-ID: t för den virtuella datorn kan konstrueras med prenumerations-ID, resurs grupp namn och namn på virtuell dator med följande sträng format: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Eller så kan du koppla DSVM till din arbets yta [med Azure Machine Learning Studio](#portal-reuse).

1. **Konfigurera**: skapa en körnings konfiguration för DSVM Compute Target. Docker och Conda används för att skapa och konfigurera utbildnings miljön på DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Nu när du har kopplat beräkningen och konfigurerat din körning är nästa steg att [Skicka utbildningen](#submit).

### <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight är en populär plattform för stor data analys. Plattformen ger Apache Spark som kan användas för att träna din modell.

1. **Skapa**: skapa HDInsight-klustret innan du använder det för att träna din modell. Information om hur du skapar ett spark på HDInsight-kluster finns i [skapa ett Spark-kluster i HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    > [!WARNING]
    > Azure Machine Learning kräver att HDInsight-klustret har en __offentlig IP-adress__.

    När du skapar klustret måste du ange ett SSH-användarnamn och-lösen ord. Anteckna dessa värden eftersom du behöver dem för att använda HDInsight som ett beräknings mål.
    
    När klustret har skapats ansluter du till det med hostname \<clustername> -SSH.azurehdinsight.net, där \<clustername> är det namn som du har angett för klustret. 

1. **Bifoga**: om du vill ansluta ett HDInsight-kluster som ett beräknings mål måste du ange resurs-ID, användar namn och lösen ord för HDInsight-klustret. Resurs-ID för HDInsight-klustret kan konstrueras med prenumerations-ID, resurs grupp namn och HDInsight-kluster namn med följande sträng format: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Eller så kan du ansluta HDInsight-klustret till din arbets yta [med Azure Machine Learning Studio](#portal-reuse).

1. **Konfigurera**: skapa en körnings konfiguration för HDI Compute Target. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Nu när du har kopplat beräkningen och konfigurerat din körning är nästa steg att [Skicka utbildningen](#submit).


### <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch används för att köra storskaliga parallella program och HPC-program (data behandling med höga prestanda) i molnet. AzureBatchStep kan användas i en Azure Machine Learning pipeline för att skicka jobb till en Azure Batch pool med datorer.

Om du vill bifoga Azure Batch som ett beräknings mål måste du använda Azure Machine Learning SDK och ange följande information:

-    **Azure Batch Compute Name**: ett eget namn som ska användas för data bearbetningen inom arbets ytan
-    **Azure Batch konto namn**: namnet på det Azure Batch kontot
-    **Resurs grupp**: den resurs grupp som innehåller det Azure Batch kontot.

Följande kod visar hur du kopplar Azure Batch som ett beräknings mål:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks är en Apache Spark-baserad miljö i Azure-molnet. Den kan användas som ett beräknings mål med en Azure Machine Learning pipeline.

Skapa en Azure Databricks arbets yta innan du använder den. Information om hur du skapar en arbets ytas resurs finns i [köra ett Spark-jobb på Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) -dokument.

Om du vill bifoga Azure Databricks som ett beräknings mål anger du följande information:

* __Databricks Compute Name__: det namn som du vill tilldela till den här beräknings resursen.
* __Databricks namn på arbets yta__: namnet på arbets ytan Azure Databricks.
* __Databricks__: åtkomst-token som används för att autentisera till Azure Databricks. Om du vill generera en åtkomsttoken, se [Authentication](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) -dokumentet.

Följande kod visar hur du kopplar Azure Databricks som ett beräknings mål med Azure Machine Learning SDK (Databricks-__arbetsytan måste finnas i samma prenumeration som din AML-arbetsyta__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Ett mer detaljerat exempel finns i en [exempel antecknings bok](https://aka.ms/pl-databricks) på GitHub.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics är en stor data analys plattform i Azure-molnet. Den kan användas som ett beräknings mål med en Azure Machine Learning pipeline.

Skapa ett Azure Data Lake Analytics konto innan du använder det. Information om hur du skapar den här resursen finns i dokumentet [Kom igång med Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) .

Om du vill bifoga Data Lake Analytics som ett beräknings mål måste du använda Azure Machine Learning SDK och ange följande information:

* __Compute-namn__: det namn som du vill tilldela till den här beräknings resursen.
* __Resurs grupp__: den resurs grupp som innehåller det data Lake Analytics kontot.
* __Konto namn__: namnet på data Lake Analytics kontot.

Följande kod visar hur du kopplar Data Lake Analytics som ett beräknings mål:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Ett mer detaljerat exempel finns i en [exempel antecknings bok](https://aka.ms/pl-adla) på GitHub.

> [!TIP]
> Azure Machine Learning pipelines kan bara arbeta med data som lagras i standard data lagret för det Data Lake Analytics kontot. Om de data du behöver arbeta med finns i ett lager som inte är standard kan du använda en [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) för att kopiera data före träning.

## <a name="set-up-in-azure-machine-learning-studio"></a>Konfigurera i Azure Machine Learning Studio

Du kan komma åt de beräknings mål som är associerade med din arbets yta i Azure Machine Learning Studio.  Du kan använda Studio för att:

* [Visa beräknings mål](#portal-view) som är kopplade till din arbets yta
* [Skapa ett beräknings mål](#portal-create) på din arbets yta
* [Bifoga ett beräknings mål](#portal-reuse) som har skapats utanför arbets ytan


När ett mål har skapats och kopplats till din arbets yta, kommer du att använda det i din körnings konfiguration med ett `ComputeTarget` objekt: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a name="view-compute-targets"></a><a id="portal-view"></a>Visa beräknings mål


Använd följande steg för att se beräknings målen för din arbets yta:

1. Navigera till [Azure Machine Learning Studio](https://ml.azure.com).
 
1. Under __program__väljer du __beräkning__.

    [![Visa fliken beräkning](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a name="create-a-compute-target"></a><a id="portal-create"></a>Skapa ett beräknings mål

Följ föregående steg för att visa listan över beräknings mål. Använd sedan de här stegen för att skapa ett beräknings mål: 

1. Välj plus tecknet (+) för att lägga till ett beräknings mål.

    ![Lägg till ett beräknings mål](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Ange ett namn för beräknings målet. 

1. Välj **Machine Learning-beräkning** som den typ av beräkning som ska användas för __träning__. 

    >[!NOTE]
    >Azure Machine Learning Compute är den enda hanterade beräknings resursen som du kan skapa i Azure Machine Learning Studio.  Alla andra beräknings resurser kan bifogas när de har skapats.

1. Fyll i formuläret. Ange värden för de obligatoriska egenskaperna, särskilt **VM-serien**och de **maximalt antal noder** som ska användas för att öka beräkningen.  

1. Välj __Skapa__.


1. Visa status för åtgärden Skapa genom att välja Compute-målet i listan:

    ![Välj ett beräknings mål för att visa status för att skapa åtgärd](./media/how-to-set-up-training-targets/View_list.png)

1. Sedan kan du se information om Compute-målet: 

    ![Visa information om dator mål](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a name="attach-compute-targets"></a><a id="portal-reuse"></a>Bifoga Compute-mål

Om du vill använda beräknings mål som skapats utanför arbets ytan Azure Machine Learning måste du koppla dem. Om du kopplar ett beräknings mål blir det tillgängligt för din arbets yta.

Följ stegen som beskrivs ovan för att visa listan över beräknings mål. Använd sedan följande steg för att ansluta ett beräknings mål: 

1. Välj plus tecknet (+) för att lägga till ett beräknings mål. 
1. Ange ett namn för beräknings målet. 
1. Välj den typ av beräkning som ska bifogas för __träning__:

    > [!IMPORTANT]
    > Alla beräknings typer kan inte kopplas från Azure Machine Learning Studio. De beräknings typer som för närvarande kan bifogas för utbildning är:
    >
    > * En virtuell fjärrdator
    > * Azure Databricks (används i maskin inlärnings pipeliner)
    > * Azure Data Lake Analytics (används i maskin inlärnings pipeliner)
    > * Azure HDInsight

1. Fyll i formuläret och ange värden för de obligatoriska egenskaperna.

    > [!NOTE]
    > Microsoft rekommenderar att du använder SSH-nycklar, som är säkrare än lösen ord. Lösen ord är utsatta för angrepp med brute force. SSH-nycklar är beroende av kryptografiska signaturer. Information om hur du skapar SSH-nycklar för användning med Azure Virtual Machines finns i följande dokument:
    >
    > * [Skapa och använda SSH-nycklar på Linux eller macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Skapa och Använd SSH-nycklar i Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Välj __bifoga__. 
1. Visa status för åtgärden Anslut genom att välja Compute-målet i listan.

## <a name="set-up-with-cli"></a>Konfigurera med CLI

Du kan komma åt de beräknings mål som är kopplade till din arbets yta med [CLI-tillägget](reference-azure-machine-learning-cli.md) för Azure Machine Learning.  Du kan använda CLI för att:

* Skapa ett hanterat beräknings mål
* Uppdatera ett hanterat beräknings mål
* Koppla ett ohanterat beräknings mål

Mer information finns i [resurs hantering](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Konfigurera med VS Code

Du kan komma åt, skapa och hantera de beräknings mål som är kopplade till din arbets yta med [vs Code-tillägget](how-to-manage-resources-vscode.md#compute-clusters) för Azure Machine Learning.

## <a name="submit-training-run-using-azure-machine-learning-sdk"></a><a id="submit"></a>Skicka utbildnings körning med hjälp av Azure Machine Learning SDK

När du har skapat en körnings konfiguration använder du den för att köra experimentet.  Kod mönstret för att skicka en tränings körning är detsamma för alla typer av beräknings mål:

1. Skapa ett experiment att köra
1. Skicka in körningen.
1. Vänta tills körningen har slutförts.

> [!IMPORTANT]
> När du skickar utbildningen skapas en ögonblicks bild av den katalog som innehåller dina utbildnings skript och skickas till beräknings målet. Den lagras också som en del av experimentet i din arbets yta. Om du ändrar filer och skickar körningen igen kommer bara de ändrade filerna att överföras.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Mer information finns i [ögonblicks bilder](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Skapa ett experiment

Börja med att skapa ett experiment i din arbets yta.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Skicka experimentet

Skicka experimentet med ett- `ScriptRunConfig` objekt.  Det här objektet innehåller:

* **source_directory**: käll katalogen som innehåller ditt utbildnings skript
* **skript**: identifiera utbildnings skriptet
* **run_config**: kör konfigurationen, som i sin tur definierar var träningen ska ske.

Om du till exempel vill använda [den lokala mål](#local) konfigurationen:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Växla samma experiment för att köra i ett annat beräknings mål genom att använda en annan körnings konfiguration, till exempel [amlcompute-målet](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> I det här exemplet används som standard bara en nod i Compute-målet för träning. Om du vill använda mer än en nod ställer du in `node_count` körnings konfigurationen på önskat antal noder. Följande kod anger till exempel antalet noder som används för utbildning till fyra:
>
> ```python
> src.run_config.node_count = 4
> ```

Eller så kan du:

* Skicka experimentet med ett- `Estimator` objekt som det visas i [träna ml-modeller med uppskattningar](how-to-train-ml-models.md).
* Skicka en HyperDrive-körning för inställning av min [parameter](how-to-tune-hyperparameters.md).
* Skicka ett experiment via [vs Code-tillägget](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

Mer information finns i dokumentationen om [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) och [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) .

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Skapa kör konfiguration och skicka körning med Azure Machine Learning CLI

Du kan använda [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och [Machine Learning CLI-tillägget](reference-azure-machine-learning-cli.md) för att skapa kör konfigurationer och skicka körningar på olika Compute-mål. Följande exempel förutsätter att du har en befintlig Azure Machine Learning-arbetsyta och att du har loggat in på Azure med `az login` CLI-kommandot. 

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

### <a name="create-run-configuration"></a>Skapa körnings konfiguration

Det enklaste sättet att skapa kör konfiguration är att navigera i mappen som innehåller dina Python-skript för Machine Learning och använda kommandot CLI

```azurecli
az ml folder attach
```

Det här kommandot skapar en undermapp `.azureml` som innehåller mallar som kör konfigurationsfiler för olika beräknings mål. Du kan kopiera och redigera dessa filer för att anpassa konfigurationen, till exempel för att lägga till python-paket eller ändra Docker-inställningar.  

### <a name="structure-of-run-configuration-file"></a>Struktur för körnings konfigurations filen

Kör konfigurations filen är YAML formaterad, med följande avsnitt
 * Skriptet som ska köras och dess argument
 * Compute Target Name, antingen "Local" eller namnet på en beräkning under arbets ytan.
 * Parametrar för att köra körning: Framework, Communicator för distribuerade körningar, maximal varaktighet och antal datornoder.
 * Miljö avsnitt. Mer information om fälten i det här avsnittet finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md) .
   * Om du vill ange python-paket som ska installeras för kör, skapa [Conda-miljöfilen](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)och ange __condaDependenciesFile__ -fältet.
 * Kör historik information för att ange loggmappen och aktivera eller inaktivera insamlingen av utdata och kör historik ögonblicks bilder.
 * Konfigurations information som är speciell för det valda ramverket.
 * Data referens och data lager information.
 * Konfigurations information som är speciell för Machine Learning-beräkning för att skapa ett nytt kluster.

Se JSON- [filen](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) för ett fullständigt runconfig-schema.

### <a name="create-an-experiment"></a>Skapa ett experiment

Börja med att skapa ett experiment för dina körningar

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Skript körning

Kör ett kommando om du vill skicka en skript körning

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>HyperDrive körning

Du kan använda HyperDrive med Azure CLI för att utföra parameter justerings körningar. Börja med att skapa en HyperDrive-konfigurations fil i följande format. Se [Justera egenskaper för en modell](how-to-tune-hyperparameters.md) artikel för information om parametrar för parameter justering.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Lägg till den här filen tillsammans med konfigurationsfilerna för körning. Skicka sedan en HyperDrive-körning med:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Observera avsnittet *argument* i runconfig och *parameter utrymme* i HyperDrive config. De innehåller kommando rads argument som ska skickas till övnings skriptet. Värdet i runconfig förblir detsamma för varje iteration, medan intervallet i HyperDrive-config upprepas. Ange inte samma argument i båda filerna.

Mer information om dessa ```az ml``` CLI-kommandon finns i [referens dokumentationen](reference-azure-machine-learning-cli.md).

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git-spårning och integrering

När du startar en utbildning som kör där käll katalogen är en lokal git-lagringsplats, lagras information om lagrings platsen i körnings historiken. Mer information finns i [git-integrering för Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Exempel på bärbara datorer

Se dessa antecknings böcker för exempel på utbildning med olika beräknings mål:
* [instruktion för att använda – azureml/utbildning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Självstudier/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* [Självstudie: träna en modell](tutorial-train-models-with-aml.md) använder ett hanterat beräknings mål för att träna en modell.
* Lär dig hur du [effektivt justerar disponeringsparametrarna](how-to-tune-hyperparameters.md) för att bygga bättre modeller.
* När du har en tränad modell lär du dig [hur och var modeller ska distribueras](how-to-deploy-and-where.md).
* Visa SDK-referens för [RunConfiguration-klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) .
* [Använda Azure Machine Learning med virtuella Azure-nätverk](how-to-enable-virtual-network.md)
