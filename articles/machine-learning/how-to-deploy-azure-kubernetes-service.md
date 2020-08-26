---
title: Distribuera ML-modeller till Kubernetes-tjänsten
titleSuffix: Azure Machine Learning
description: Lär dig hur du distribuerar dina Azure Machine Learning modeller som en webb tjänst med Azure Kubernetes-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: 6c85a7315fe05bb4fedabd176295523c2fa95d81
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855238"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Distribuera en modell till ett Azure Kubernetes service-kluster
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du använder Azure Machine Learning för att distribuera en modell som en webb tjänst på Azure Kubernetes service (AKS). Azure Kubernetes-tjänsten är lämplig för storskaliga produktions distributioner. Använd Azure Kubernetes-tjänsten om du behöver en eller flera av följande funktioner:

- __Snabb svars tid__.
- Automatisk __skalning__ av den distribuerade tjänsten.
- Alternativ för __maskin varu acceleration__ , t. ex. GPU och fält-programmerbara grind mat ris (FPGA).

> [!IMPORTANT]
> Kluster skalning tillhandahålls inte via Azure Machine Learning SDK. Mer information om hur du skalar noderna i ett AKS-kluster finns i 
- [Skala antalet noder manuellt i ett AKS-kluster](../aks/scale-cluster.md)
- [Konfigurera autoskalning för kluster i AKS](../aks/cluster-autoscaler.md)

När du distribuerar till Azure Kubernetes-tjänsten distribuerar du till ett AKS-kluster som är __anslutet till din arbets yta__. Det finns två sätt att ansluta ett AKS-kluster till din arbets yta:

* Skapa AKS-klustret med hjälp av Azure Machine Learning SDK, Machine Learning CLI eller [Azure Machine Learning Studio](https://ml.azure.com). Den här processen ansluter automatiskt klustret till arbets ytan.
* Koppla ett befintligt AKS-kluster till din Azure Machine Learning-arbetsyta. Ett kluster kan kopplas med hjälp av Azure Machine Learning SDK, Machine Learning CLI eller Azure Machine Learning Studio.

AKS-klustret och arbets ytan AML kan finnas i olika resurs grupper.

> [!IMPORTANT]
> Processen för att skapa eller bifogad fil är en engångs uppgift. När ett AKS-kluster är anslutet till arbets ytan kan du använda det för distributioner. Du kan koppla bort eller ta bort AKS-klustret om du inte längre behöver det. När du har kopplats bort eller tagits bort kommer du inte längre att kunna distribuera till klustret.

> [!IMPORTANT]
> Vi rekommenderar att du felsökr lokalt innan du distribuerar till webb tjänsten. Mer information finns i [Felsöka lokalt](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally)
>
> Du kan också läsa Azure Machine Learning – [Distribuera till lokal notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Förutsättningar

- En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

- En Machine Learning-modell som registrerats i din arbets yta. Om du inte har en registrerad modell, se [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

- [Azure CLI-tillägget för Machine Learning-tjänst](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller [Azure Machine Learning Visual Studio Code-tillägget](tutorial-setup-vscode-extension.md).

- I __python__ -kodfragmenten i den här artikeln förutsätter vi att följande variabler har angetts:

    * `ws` – Ställ in på din arbets yta.
    * `model` – Ställ in på din registrerade modell.
    * `inference_config` -Ange som modellens konfigurations konfiguration.

    Mer information om hur du ställer in dessa variabler finns i [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

- __CLI__ -kodfragmenten i den här artikeln förutsätter att du har skapat ett `inferenceconfig.json` dokument. Mer information om hur du skapar det här dokumentet finns i [så här distribuerar du modeller](how-to-deploy-and-where.md).

- Om du behöver ett Standard Load Balancer (SLB) distribuerat i klustret i stället för en grundläggande Load Balancer (BLB) skapar du ett kluster i AKS-portalen/CLI/SDK och kopplar det sedan till AML-arbetsytan.

- Om du har en Azure Policy som begränsar skapandet av offentliga IP-adresser kommer AKS-kluster inte att fungera. AKS kräver en offentlig IP-adress för [utgående trafik](https://docs.microsoft.com/azure/aks/limit-egress-traffic). Den här artikeln innehåller också vägledning för att övervaka utgående trafik från klustret via den offentliga IP-adressen förutom några få FQDN. Det finns två sätt att aktivera en offentlig IP-adress:
  - Klustret kan använda den offentliga IP-adressen som skapas som standard med BLB eller SLB, eller
  - Klustret kan skapas utan en offentlig IP-adress och en offentlig IP-adress konfigureras med en brand vägg med en användardefinierad väg som dokumenteras [här](https://docs.microsoft.com/azure/aks/egress-outboundtype) 
  
  Kontroll planet för AML pratar inte med den här offentliga IP-adressen. Den pratar med AKS-kontroll planet för distributioner. 

- Om du ansluter ett AKS-kluster, som har ett [auktoriserat IP-adressintervall som är aktiverat för att få åtkomst till API-servern](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), aktiverar du AML conto-planens IP-intervall för AKS-klustret. Kontroll planet för AML distribueras i kopplade regioner och distribuerar inferencing-poddar i AKS-klustret. Inferencing-poddar kan inte distribueras utan åtkomst till API-servern. Använd [IP-intervallen](https://www.microsoft.com/en-us/download/confirmation.aspx?id=56519) för båda [kopplade regionerna]( https://docs.microsoft.com/azure/best-practices-availability-paired-regions) när du aktiverar IP-intervall i ett AKS-kluster.


  Authroized IP-intervall fungerar endast med Standard Load Balancer.
 
 - Compute-namnet måste vara unikt inom en arbets yta
   - Namnet måste vara mellan 3 och 24 tecken långt.
   - Giltiga tecken är versaler, gemener, siffror och tecknet-Character.
   - Namnet måste börja med en bokstav
   - Namnet måste vara unikt för alla befintliga beräkningar i en Azure-region. En avisering visas om det namn du väljer inte är unikt
   
 - Om du vill distribuera modeller till GPU-noder eller FPGA-noder (eller vissa SKU: er) måste du skapa ett kluster med den angivna SKU: n. Det finns inget stöd för att skapa en sekundär Node-pool i ett befintligt kluster och distribuera modeller i den sekundära noden.

## <a name="create-a-new-aks-cluster"></a>Skapa ett nytt AKS-kluster

**Tids uppskattning**: cirka 10 minuter.

Att skapa eller ansluta ett AKS-kluster är en process för arbets ytan. Du kan återanvända det här klustret för flera distributioner. Om du tar bort klustret eller resurs gruppen som innehåller den måste du skapa ett nytt kluster nästa gången du behöver distribuera. Du kan ha flera AKS-kluster kopplade till din arbets yta.
 
Azure Machine Learning har nu stöd för användning av en Azure Kubernetes-tjänst som har privat länk aktive rad.
Om du vill skapa ett privat AKS-kluster följer du dokumenten [här](https://docs.microsoft.com/azure/aks/private-clusters)

> [!TIP]
> Om du vill skydda ditt AKS-kluster med hjälp av ett Azure-Virtual Network måste du först skapa det virtuella nätverket. Mer information finns i [säker experimentering och härledning med Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Om du vill skapa ett AKS-kluster för __utveckling__, __validering__och __testning__ i stället för produktion kan du ange ett __kluster syfte__ för __dev-test__.

> [!WARNING]
> Om du ställer in `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` är det kluster som skapas inte lämpligt för trafik på produktions nivå och kan öka eventuella härlednings tider. Utvecklings-och test kluster garanterar inte heller fel tolerans. Vi rekommenderar minst 2 virtuella processorer för dev/test-kluster.

Följande exempel visar hur du skapar ett nytt AKS-kluster med hjälp av SDK och CLI:

**Med SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()
# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> För [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) , om du väljer anpassade värden för `agent_count` och `vm_size` , och `cluster_purpose` inte, och inte `DEV_TEST` , måste du se till att `agent_count` multiplicerat med `vm_size` är större än eller lika med 12 virtuella processorer. Om du till exempel använder en `vm_size` av "Standard_D3_v2", som har 4 virtuella processorer, måste du välja en `agent_count` av 3 eller fler.
>
> Azure Machine Learning SDK ger inte stöd för skalning av ett AKS-kluster. Om du vill skala noderna i klustret använder du användar gränssnittet för ditt AKS-kluster i Azure Machine Learning Studio. Du kan bara ändra antalet noder, inte klustrets virtuella dator storlek.

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referens dokument:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget. wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Använda CLI**

```azurecli
az ml computetarget create aks -n myaks
```

Mer information finns i [AZ ml computetarget Create AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference.

## <a name="attach-an-existing-aks-cluster"></a>Koppla ett befintligt AKS-kluster

**Tids uppskattning:** Cirka 5 minuter.

Om du redan har AKS-kluster i din Azure-prenumeration och det är version 1,17 eller lägre kan du använda det för att distribuera avbildningen.

> [!TIP]
> Det befintliga AKS-klustret kan finnas i en annan Azure-region än din Azure Machine Learning-arbetsyta.
>
> Om du vill skydda ditt AKS-kluster med hjälp av ett Azure-Virtual Network måste du först skapa det virtuella nätverket. Mer information finns i [säker experimentering och härledning med Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

När du kopplar ett AKS-kluster till en arbets yta kan du definiera hur du ska använda klustret genom att ange `cluster_purpose` parametern.

Om du inte anger `cluster_purpose` parametern eller anger så `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD` måste klustret ha minst 12 virtuella processorer tillgängliga.

Om du ställer in `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` behöver klustret inte ha 12 virtuella processorer. Vi rekommenderar minst 2 virtuella processorer för utveckling/testning. Men ett kluster som har kon figurer ATS för utveckling/testning är inte lämpligt för trafik på produktions nivå och kan öka eventuella härlednings tider. Utvecklings-och test kluster garanterar inte heller fel tolerans.

> [!WARNING]
> Skapa inte flera, samtidiga bilagor till samma AKS-kluster från din arbets yta. Du kan till exempel koppla ett AKS-kluster till en arbets yta med två olika namn. Varje ny bilaga kommer att dela upp de tidigare befintliga bifogade filerna.
>
> Om du vill koppla ett AKS-kluster på nytt, t. ex. ändra TLS eller en annan kluster konfigurations inställning, måste du först ta bort den befintliga bilagan med hjälp av [AksCompute. Detached ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Mer information om hur du skapar ett AKS-kluster med hjälp av Azure CLI eller portalen finns i följande artiklar:

* [Skapa ett AKS-kluster (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Skapa ett AKS-kluster (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)
* [Skapa ett AKS-kluster (ARM-mall i Azure snabb starts mallar)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

Följande exempel visar hur du ansluter ett befintligt AKS-kluster till din arbets yta:

**Med SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referens dokument:

* [AksCompute. attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Använda CLI**

Om du vill koppla ett befintligt kluster med CLI måste du hämta resurs-ID för det befintliga klustret. Använd följande kommando för att hämta det här värdet. Ersätt `myexistingcluster` med namnet på ditt AKS-kluster. Ersätt `myresourcegroup` med resurs gruppen som innehåller klustret:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Det här kommandot returnerar ett värde som liknar följande text:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Använd följande kommando för att koppla det befintliga klustret till din arbets yta. Ersätt `aksresourceid` med det värde som returnerades av föregående kommando. Ersätt `myresourcegroup` med den resurs grupp som innehåller din arbets yta. Ersätt `myworkspace` med namnet på din arbets yta.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Mer information finns i [AZ ml computetarget Attach AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) reference.

## <a name="deploy-to-aks"></a>Distribuera till AKS

Om du vill distribuera en modell till Azure Kubernetes-tjänsten skapar du en __distributions konfiguration__ som beskriver de beräknings resurser som behövs. Till exempel antal kärnor och minne. Du behöver också en __konfiguration__med en konfiguration som beskriver den miljö som krävs för att vara värd för modellen och webb tjänsten. Mer information om hur du skapar en konfigurations konfiguration finns i [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

> [!NOTE]
> Antalet modeller som ska distribueras är begränsat till 1 000 modeller per distribution (per behållare).


### <a name="using-the-sdk"></a>Med SDK

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referens dokument:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Modell. Deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Använda CLI

Använd följande kommando för att distribuera med hjälp av CLI. Ersätt `myaks` med namnet på AKS Compute Target. Ersätt `mymodel:1` med namnet och versionen för den registrerade modellen. Ersätt `myservice` med namnet för att ge den här tjänsten:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Mer information finns i [distributions referens för AZ ml-modellen](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a name="using-vs-code"></a>Använda VS Code

Information om hur du använder VS Code finns i [distribuera till AKS via vs Code-tillägget](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Att distribuera via VS Code kräver att AKS-klustret skapas eller kopplas till din arbets yta i förväg.

### <a name="understand-the-deployment-processes"></a>Förstå distributions processerna

Ordet "Deployment" används i både Kubernetes och Azure Machine Learning. "Distribution" har olika betydelser i dessa två kontexter. I Kubernetes är en `Deployment` konkret entitet som anges med en DEKLARATIV yaml-fil. En Kubernetes `Deployment` har en definierad livs cykel och konkreta relationer till andra Kubernetes entiteter som `Pods` och `ReplicaSets` . Du kan lära dig mer om Kubernetes från dokument och videor på [Vad är Kubernetes?](https://aka.ms/k8slearning).

I Azure Machine Learning används "distribution" i den allmänna uppfattningen för att göra tillgängliga och rensa projekt resurserna. De steg som Azure Machine Learning anser en del av distributionen är:

1. Zippa upp filerna i projektmappen och ignorera dem som anges i. amlignore eller. gitignore
1. Skala upp beräknings klustret (relaterat till Kubernetes)
1. Skapa eller ladda ned Dockerfile till Compute-noden (relaterar till Kubernetes)
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
1. Skala ned beräkning, inklusive borttagning av tillfällig lagring (relatera till Kubernetes)

När du använder AKS styrs skalningen av data bearbetningen av Kubernetes med hjälp av Dockerfile som har skapats eller hittats enligt beskrivningen ovan. 

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Distribuera modeller till AKS med hjälp av kontrollerad distribution (för hands version)

Analysera och uppgradera modell versioner på ett kontrollerat sätt med hjälp av slut punkter. Du kan distribuera upp till sex versioner bakom en enda slut punkt. Slut punkter tillhandahåller följande funktioner:

* Konfigurera __procent andelen av bedömnings trafik som skickas till varje slut punkt__. Du kan till exempel skicka 20% av trafiken till slut punkten ' test ' och 80% till ' produktion '.

    > [!NOTE]
    > Om du inte tar hänsyn till 100% av trafiken dirigeras eventuella återstående procent till __standard__ slut punkts versionen. Om du till exempel konfigurerar slut punkts version "test" för att få 10% av trafiken och "Prod" i 30% skickas återstående 60% till standard slut punkts versionen.
    >
    > Den första slut punkts versionen som skapas konfigureras automatiskt som standard. Du kan ändra detta genom att ställa in `is_default=True` när du skapar eller uppdaterar en slut punkts version.
     
* Tagga en slut punkts version som __kontroll__ eller __behandling__. Till exempel kan den aktuella produktions slut punkts versionen vara kontrollen, medan eventuella nya modeller distribueras som behandlings versioner. När du har utvärderat prestandan för behandlings versionerna, om en utför den aktuella kontrollen, kan den höjas till den nya produktionen/kontrollen.

    > [!NOTE]
    > Du kan bara ha __en__ kontroll. Du kan ha flera behandlingar.

Du kan aktivera App Insights om du vill visa drifts mått för slut punkter och distribuerade versioner.

### <a name="create-an-endpoint"></a>Skapa en slutpunkt
När du är redo att distribuera dina modeller skapar du en poäng slut punkt och distribuerar din första version. I följande exempel visas hur du distribuerar och skapar slut punkten med hjälp av SDK. Den första distributionen definieras som standard versionen, vilket innebär att ospecificerad trafik percentil över alla versioner kommer att gå till standard versionen.  

> [!TIP]
> I följande exempel anger konfigurationen den ursprungliga slut punkts versionen för att hantera 20% av trafiken. Eftersom det här är den första slut punkten är det även standard versionen. Eftersom vi inte har några andra versioner av den andra 80% av trafiken dirigeras de också till standard. Till dess att andra versioner som tar en procent andel av trafiken distribueras, tar den här en effektiv emot 100% av trafiken.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Uppdatera och lägga till versioner till en slut punkt

Lägg till en annan version till din slut punkt och konfigurera bedömnings trafikens percentil till versionen. Det finns två typer av versioner, en kontroll och en behandlings version. Det kan finnas flera behandlings versioner som hjälper dig att jämföra med en enda kontroll version.

> [!TIP]
> Den andra versionen, som skapats av följande kodfragment, accepterar 10% av trafiken. Den första versionen har kon figurer ATS för 20%, så endast 30% av trafiken har kon figurer ATS för vissa versioner. Den återstående 70% skickas till den första slut punkts versionen, eftersom den också är standard versionen.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

Uppdatera befintliga versioner eller ta bort dem i en slut punkt. Du kan ändra versionens standard typ, kontroll typ och trafik percentilen. I följande exempel ökar den andra versionen sin trafik till 40% och är nu standard.

> [!TIP]
> Efter följande kodfragment är den andra versionen nu standard. Den har nu kon figurer ATS för 40%, medan den ursprungliga versionen fortfarande har kon figurer ATS för 20%. Det innebär att 40% av trafiken inte redovisas för av versions konfigurationerna. Den överblivna trafiken kommer att dirigeras till den andra versionen eftersom den nu är standard. Det tar effektivt emot 80% av trafiken.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Webb tjänstens autentisering

När du distribuerar till Azure Kubernetes-tjänsten aktive ras __nyckelbaserad__ autentisering som standard. Du kan också aktivera __tokenbaserad__ autentisering. Token-baserad autentisering kräver att klienter använder ett Azure Active Directory konto för att begära en autentiseringstoken, som används för att göra förfrågningar till den distribuerade tjänsten.

Om du vill __inaktivera__ autentisering ställer du in `auth_enabled=False` parametern när du skapar distributions konfigurationen. I följande exempel inaktive ras autentisering med hjälp av SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Information om hur du autentiserar från ett klient program finns i [använda en Azure Machine Learning modell som distribueras som en webb tjänst](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Autentisering med nycklar

Om du har aktiverat autentisering av nycklar kan du använda- `get_keys` metoden för att hämta en primär nyckel och en sekundär autentiseringsnyckel:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Om du behöver återskapa en nyckel använder du [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Autentisering med token

Om du vill aktivera token-autentisering anger `token_auth_enabled=True` du parametern när du skapar eller uppdaterar en distribution. I följande exempel aktive ras token-autentisering med SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Om token-autentisering har Aktiver ATS kan du använda `get_token` metoden för att hämta en JWT-token och dess förfallo tid för token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Du måste begära en ny token efter det att token har `refresh_by` uppnåtts.
>
> Microsoft rekommenderar starkt att du skapar din Azure Machine Learning arbets yta i samma region som ditt Azure Kubernetes service-kluster. För att autentisera med en token kommer webb tjänsten att ringa till den region där din Azure Machine Learning arbets yta skapas. Om arbets ytans region inte är tillgänglig kan du inte hämta en token för din webb tjänst även om klustret finns i en annan region än din arbets yta. Detta leder till att tokenbaserad autentisering inte är tillgängligt förrän arbets ytans region är tillgänglig igen. Dessutom ökar avståndet mellan klustrets region och arbets ytans region, desto längre tid tar det att hämta en token.
>
> Om du vill hämta en token måste du använda kommandot Azure Machine Learning SDK eller [AZ ml-tjänsten get-Access-token](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-get-access-token) .

## <a name="next-steps"></a>Nästa steg

* [Säker experimentering och härledning i ett virtuellt nätverk](how-to-enable-virtual-network.md)
* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Distributions fel sökning](how-to-troubleshoot-deployment.md)
* [Uppdatera webbtjänst](how-to-deploy-update-web-service.md)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Använda en ML-modell som distribueras som en webb tjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
