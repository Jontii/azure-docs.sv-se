---
title: Använda hanterade identiteter för åtkomst kontroll (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder hanterade identiteter för att kontrol lera åtkomsten till Azure-resurser från Azure Machine Learning arbets ytan.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 6bcc4ac5561a8bdb721018aa05bf2376579b627b
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079677"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>Använda hanterade identiteter med Azure Machine Learning (förhands granskning)

Med [hanterade identiteter](/azure/active-directory/managed-identities-azure-resources/overview) kan du konfigurera din arbets yta med *minsta behörighet som krävs för att få åtkomst till resurser*. 

När du konfigurerar Azure Machine Learning arbets ytan på ett tillförlitligt sätt är det viktigt att se till att olika tjänster som är kopplade till arbets ytan har rätt åtkomst nivå. Arbets ytan för Machine Learning måste till exempel ha åtkomst till Azure Container Registry (ACR) för Docker-avbildningar och lagrings konton för tränings data. 

Dessutom tillåter hanterade identiteter detaljerad kontroll över behörigheter, till exempel att du kan bevilja eller återkalla åtkomst från vissa beräknings resurser till en speciell ACR.

I den här artikeln får du lära dig hur du använder hanterade identiteter för att:

 * Konfigurera och Använd ACR för din Azure Machine Learning-arbetsyta utan att behöva aktivera administratörs användarens åtkomst till ACR.
 * Få åtkomst till en privat ACR extern till din arbets yta, för att hämta bas avbildningar för utbildning eller härledning.
 * Få åtkomst till data uppsättningar för utbildning genom att använda hanterade identiteter i stället för lagrings åtkomst nycklar.

> [!IMPORTANT]
> Att använda hanterade identiteter för att kontrol lera åtkomst till resurser med Azure Machine Learning för närvarande finns som för hands version. Förhands gransknings funktionerna tillhandahålls "i befintligt skick", utan garanti av support eller service nivå avtal. Mer information finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.
 
## <a name="prerequisites"></a>Förutsättningar

- En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
- [Azure CLI-tillägget för Machine Learning tjänst](reference-azure-machine-learning-cli.md)
- [Azure Machine Learning python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
- För att tilldela roller måste inloggningen för din Azure-prenumeration ha rollen [hanterad identitets operatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) eller annan roll som ger nödvändiga åtgärder (till exempel __ägare__).
- Du måste vara bekant med att skapa och arbeta med [hanterade identiteter](/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="configure-managed-identities"></a>Konfigurera hanterade identiteter

I vissa situationer är det nödvändigt att inte tillåta administratörs användarens åtkomst till Azure Container Registry. Till exempel kan ACR delas och du måste neka administratörs åtkomst av andra användare. Eller också är det inte tillåtet att skapa ACR med administratörs användare aktiverat av en princip på prenumerations nivå.

> [!IMPORTANT]
> När du använder Azure Machine Learning för att få en utgångs punkt på Azure Container Instance (ACI) __krävs__administratörs användarens åtkomst på ACR. Inaktivera det inte om du planerar att distribuera modeller till ACI för att kunna bli mer härlednings.

När du skapar ACR utan att aktivera administratörs användarens åtkomst används hanterade identiteter för att få åtkomst till ACR för att skapa och hämta Docker-avbildningar.

Du kan ta med din egen ACR med administratörs användaren inaktive rad när du skapar arbets ytan. Du kan också låta Azure Machine Learning skapa arbets yta ACR och inaktivera administratörs användare efteråt.

### <a name="bring-your-own-acr"></a>Ta med din egen ACR

Om ACR admin-användare inte tillåts av en prenumerations princip bör du först skapa ACR utan Administratörs användare och sedan koppla den till arbets ytan. Om du har en befintlig ACR med administratörs användaren inaktive rad kan du också koppla den till arbets ytan.

[Skapa ACR från Azure CLI](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli) utan att ange ```--admin-enabled``` argument eller från Azure Portal utan att aktivera administratörs användare. När du sedan skapar Azure Machine Learning arbets ytan anger du Azure-resurs-ID: t för ACR. I följande exempel visas hur du skapar en ny Azure ML-arbetsyta som använder en befintlig ACR:

> [!TIP]
> Hämta värdet för `--container-registry` parametern genom att använda kommandot [AZ ACR show](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az_acr_show) för att visa information för din ACR. `id`Fältet innehåller resurs-ID för din ACR.

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>Låt Azure Machine Learning tjänsten skapa arbets yta ACR

Om du inte tar med din egen ACR skapar Azure Machine Learning tjänsten en åt dig när du utför en åtgärd som behöver en. Du kan till exempel skicka en övnings körning till Machine Learning-beräkning, skapa en miljö eller distribuera en webb tjänst slut punkt. Administratörs användaren har Aktiver ATS för ACR som skapats av arbets ytan och du måste inaktivera administratörs användaren manuellt.

1. Skapa en ny arbetsyta

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. Utför en åtgärd som kräver ACR. Till exempel [kursen om att träna en modell](tutorial-train-models-with-aml.md).

1. Hämta ACR-namnet som skapats av klustret:

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    Det här kommandot returnerar ett värde som liknar följande text. Du vill bara den sista delen av texten, som är ACR-instansens namn:

    ```text
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. Uppdatera ACR så att administratörs användaren inaktive ras:

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>Skapa beräkning med hanterad identitet för att komma åt Docker-avbildningar för utbildning

För att få åtkomst till arbets ytans ACR skapar du beräknings kluster för maskin inlärning med systemtilldelad hanterad identitet aktive rad. Du kan aktivera identiteten från Azure Portal eller Studio när du skapar beräkning eller från Azure CLI med hjälp av

# <a name="python"></a>[Python](#tab/python)

När du skapar ett beräknings kluster med [AmlComputeProvisioningConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration?view=azure-ml-py)använder du `identity_type` parametern för att ange den hanterade identitets typen.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Information om hur du konfigurerar hanterad identitet när du skapar ett beräknings kluster i Studio finns i [Konfigurera hanterad identitet](how-to-create-attach-compute-studio.md#managed-identity).

---

En hanterad identitet beviljas automatiskt ACRPull-rollen på arbets ytan ACR för att kunna hämta Docker-avbildningar för utbildning.

> [!NOTE]
> Om du skapar beräkning först måste du tilldela ACRPull-rollen manuellt innan du har skapat ACR.

## <a name="access-base-images-from-private-acr"></a>Åtkomst till bas avbildningar från privata ACR

Som standard använder Azure Machine Learning Docker Base-avbildningar som kommer från ett offentligt lager som hanteras av Microsoft. Sedan skapas din utbildnings-eller härlednings miljö på dessa avbildningar. Mer information finns i [Vad är ml-miljöer?](concept-environments.md).

Om du vill använda en anpassad bas avbildning internt i företaget kan du använda hanterade identiteter för att få åtkomst till din privata ACR. Det finns två användnings fall:

 * Använd bas avbildning för utbildning som är.
 * Bygg Azure Machine Learning hanterad avbildning med anpassad avbildning som bas.

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>Hämta Docker Base-avbildningen till Machine Learning Compute-kluster för utbildning som är

Skapa beräknings kluster för maskin inlärning med systemtilldelad hanterad identitet aktive rad enligt beskrivningen ovan. Bestäm sedan ID: t för den hanterade identiteten.

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

Du kan också uppdatera beräknings klustret om du vill tilldela en användardefinierad hanterad identitet:

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

Om du vill tillåta att beräknings klustret hämtar bas avbildningarna ger du rollen hanterad tjänst identitet ACRPull på den privata ACR

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

När du skickar in en utbildning ska du slutligen ange bas avbildnings platsen i [miljö definitionen](how-to-use-environments.md#use-existing-environments).

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> För att se till att bas avbildningen hämtas direkt till beräknings resursen anger du `user_managed_dependencies = True` och anger inte en Dockerfile. Annars försöker Azure Machine Learning tjänsten att bygga en ny Docker-avbildning och Miss lyckas, eftersom endast beräknings klustret har åtkomst till att hämta bas avbildningen från ACR.

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>Bygg Azure Machine Learning hanterad miljö till bas avbildning från privat ACR för utbildning eller härledning

I det här scenariot skapar Azure Machine Learning-tjänsten utbildnings-eller miljö utbyggnads miljön ovanpå en bas avbildning som du levererar från en privat ACR. Eftersom avbildnings Bygg aktiviteten sker på arbets ytan ACR med ACR-uppgifter måste du utföra ytterligare åtgärder för att tillåta åtkomst.

1. Skapa __användardefinierad hanterad identitet__ och ge identiteten ACRPull åtkomst till den __privata ACR__.  
1. Ge rollen __systemtilldelad hanterad__ identitet en hanterad identitets operatörs roll på den __användare som tilldelats den hanterade identiteten__ från föregående steg. Med den här rollen kan arbets ytan tilldela den användarspecifika hanterade identiteten till ACR-aktiviteten för att skapa den hanterade miljön. 

    1. Hämta ägar-ID för arbets ytans systemtilldelade identitet:

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. Ge rollen hanterad identitet operatör:

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        Resurs-ID: t för UAI är Azure-resurs-ID för den tilldelade användaren identitet i formatet `/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>` .

1. Ange det externa ACR och klient-ID: t för den __användarspecifika hanterade identiteten__ i arbets ytans anslutningar med hjälp av [Workspace.set_connection metod](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-):

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

När konfigurationen är klar kan du använda bas avbildningarna från privata ACR när du skapar miljöer för utbildning eller härledning. Följande kodfragment visar hur du anger bas avbildningens ACR och avbildnings namn i en miljö definition:

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

Alternativt kan du ange den hanterade identitets resurs-URL: en och klient-ID i själva miljö definitionen med hjälp av [RegistryIdentity](https://docs.microsoft.com/python/api/azureml-core/azureml.core.container_registry.registryidentity?view=azure-ml-py). Om du använder register identiteten explicit, åsidosätter den eventuella arbets ytans anslutningar som angetts tidigare:

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>”
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="access-training-data"></a>Åtkomst till tränings data

När du har skapat ett beräknings kluster för maskin inlärning med hanterad identitet enligt beskrivningen ovan kan du använda den identiteten för att få åtkomst till tränings data utan lagrings konto nycklar. Du kan använda antingen system-eller User-tilldelade hanterade identiteter för det här scenariot.

### <a name="grant-compute-managed-identity-access-to-storage-account"></a>Bevilja beräkning hanterad identitets åtkomst till lagrings kontot

[Ge den hanterade identiteten en läsar roll](https://docs.microsoft.com/azure/storage/common/storage-auth-aad#assign-azure-roles-for-access-rights) på det lagrings konto där du lagrar dina tränings data.

### <a name="register-data-store-with-workspace"></a>Registrera data lager med arbets yta

När du har tilldelat den hanterade identiteten kan du skapa ett data lager utan att behöva ange autentiseringsuppgifter för lagring.

```python
from azureml.core import Datastore

blob_dstore = Datastore.register_azure_blob_container(workspace=workspace,
                                                      datastore_name='my-datastore',
                                                      container_name='my-container',
                                                      account_name='my-storage-account')
```

### <a name="submit-training-run"></a>Skicka träningskörning

När du skickar en övnings körning med data lagret använder Machine Learning-beräkningen dess hanterade identitet för att få åtkomst till data.

## <a name="use-docker-images-for-inference"></a>Använd Docker-avbildningar för att göra en härledning

När du har konfigurerat ACR utan Administratörs användare som tidigare beskrivits, kan du komma åt Docker-avbildningar för att få en härledning utan Administratörs nycklar från Azure Kubernetes service (AKS). När du skapar eller ansluter AKS till arbets ytan tilldelas klustrets tjänstens huvud namn automatiskt ACRPull åtkomst till arbets ytan ACR.

> [!NOTE]
> Om du använder ett eget AKS-kluster måste tjänstens huvud namn vara aktiverat i stället för hanterad identitet.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [företags säkerhet i Azure Machine Learning](concept-enterprise-security.md).
