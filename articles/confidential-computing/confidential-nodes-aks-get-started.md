---
title: 'Snabb start: Distribuera ett Azure Kubernetes service-kluster (AKS) med hjälp av Azure CLI med konfidentiella data behandlings noder'
description: Lär dig hur du skapar ett AKS-kluster med konfidentiella noder och distribuerar en Hello World-app med hjälp av Azure CLI.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: c8c64dadebb092d7f376fd2b6590b26f4dde0ee0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91001084"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-using-azure-cli-preview"></a>Snabb start: Distribuera ett Azure Kubernetes service-kluster (AKS) med konfidentiella databeräknings-noder med Azure CLI (för hands version)

Den här snabb starten är avsedd för utvecklare eller kluster operatörer som snabbt vill skapa ett AKS-kluster och distribuera ett program för att övervaka program med hjälp av den hanterade Kubernetes-tjänsten i Azure.

## <a name="overview"></a>Översikt

I den här snabb starten får du lära dig hur du distribuerar ett Azure Kubernetes service-kluster (AKS) med konfidentiella beräknings noder med hjälp av Azure CLI och kör ett Hello World-program i en enklaven. AKS är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. Läs mer om AKS [här](https://docs.microsoft.com/azure/aks/intro-kubernetes).

> [!NOTE]
> Konfidentiell dator användning DCsv2 virtuella datorer utnyttjar specialiserad maskin vara som är föremål för högre priser och regions tillgänglighet. Mer information finns på sidan virtuella datorer för [tillgängliga SKU: er och regioner som stöds](virtual-machine-solutions.md).

### <a name="deployment-pre-requisites"></a>Krav för distribution

1. Ha en aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar
1. Ha Azure CLI-versionen 2.0.64 eller senare installerad och konfigurerad på din distributions dator (kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli)
1. [AKS –](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) lägsta version för för hands versions tillägg 0.4.62 
1. Ha minst sex DCSv2-kärnor som är tillgängliga i din prenumeration för användning. Som standard används kvoten för VM-kärnor för konfidentiella data behandling per Azure-prenumeration 8 kärnor. Om du planerar att etablera ett kluster som kräver mer än 8 kärnor, följer du [dessa](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests) anvisningar för att öka en kvots öknings biljett

### <a name="confidential-computing-node-features"></a>Funktioner i noden konfidentiellt data behandling

1. Linux Worker-noder som endast stöder Linux-behållare
1. Ubuntu generation 2 18,04 Virtual Machines
1. Intel SGX-baserad CPU med krypterad side cache-minne (EPC). Läs mer [här](https://docs.microsoft.com/azure/confidential-computing/faq)
1. Kubernetes-version 1.16 +
1. Förinstallerad Intel SGX DCAP-drivrutin. Läs mer [här](https://docs.microsoft.com/azure/confidential-computing/faq)
1. CLI-baserad distribuerad under för hands versionen


## <a name="installing-the-cli-pre-requisites"></a>Installera CLI-krav

Använd följande Azure CLI-kommandon för att installera AKS-Preview 0.4.62-tillägget eller senare:

```azurecli-interactive
az extension add --name aks-preview
az extension list
```
För att uppdatera AKS-Preview CLI-tillägget använder du följande Azure CLI-kommandon:

```azurecli-interactive
az extension update --name aks-preview
```

Registrera Gen2VMPreview:

```azurecli-interactive
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```
Det kan ta flera minuter innan statusen visas som registrerad. Du kan kontrol lera registrerings statusen med hjälp av kommandot "AZ feature list":

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```
När statusen visas som registrerad uppdaterar du registreringen av resurs leverantören Microsoft. container service med hjälp av kommandot AZ Provider register:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="creating-an-aks-cluster"></a>Skapa ett AKS-kluster

Om du redan har ett AKS-kluster som uppfyller ovanstående krav, [hoppar du till avsnittet befintligt kluster](#existing-cluster) för att lägga till en ny pool för konfidentiellt data behandling.

Skapa först en resurs grupp för klustret med kommandot AZ Group Create. I följande exempel skapas en resurs grupp med namnet *myResourceGroup* i *westus2* -regionen:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Skapa nu ett AKS-kluster med kommandot AZ AKS Create. I följande exempel skapas ett kluster med en enda nod med storlek `Standard_DC2s_v2` . Du kan välja andra lista över DCsv2 SKU: er som stöds [här](https://docs.microsoft.com/azure/virtual-machines/dcv2-series):

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_DC2s_v2 \
    --node-count 3 \
    --enable-addon confcom \
    --network-plugin azure \
    --vm-set-type VirtualMachineScaleSets \
    --aks-custom-headers usegen2vm=true
```
Kommandot ovan bör etablera ett nytt AKS-kluster med DCSv2-nodkonfigurationer och installera två daemon-uppsättningar automatiskt ([SGX-enhet plugin](confidential-nodes-aks-overview.md#sgx-plugin)-citat, med  &  [hjälp av hjälp](confidential-nodes-aks-overview.md#sgx-quote))

Hämta autentiseringsuppgifterna för ditt AKS-kluster med kommandot AZ AKS get-credentials:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Kontrol lera att noderna har skapats korrekt och att de SGX-relaterade daemon-uppsättningarna körs på DCSv2-noder med kommandot kubectl get poddar & Nodes (se nedan):

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Om resultatet matchar ovanstående är ditt AKS-kluster nu redo att köra konfidentiella program.

Gå till [Hello World](#hello-world) distributions avsnittet för enklaven för att testa en app i en enklaven. Du kan också följa anvisningarna nedan om du vill lägga till fler resurspooler på AKS (AKS stöder mixning av SGX-nodkonfigurationer och noder i andra länder än SGX)

>Om de SGX-relaterade daemon-uppsättningarna inte är installerade i DCSv2-nodkonfigurationer kör du följande.

```azurecli-interactive
az aks update --enable-addons confcom --resource-group myResourceGroup --name myAKSCluster
```

## <a name="adding-confidential-computing-node-to-existing-aks-cluster"></a>Lägga till en konfidentiell databearbetnings nod i befintligt AKS-kluster<a id="existing-cluster"></a>

Det här avsnittet förutsätter att du har ett AKS-kluster som kör redan och som uppfyller de kriterier som anges i avsnittet krav.

Först kan du aktivera de AKS-tillägg som är relaterade till konfidentiell dator i det befintliga klustret:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
Lägg nu till en DCSv2 Node-pool i klustret

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2 --aks-custom-headers usegen2vm=true

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

```console
kubectl get nodes
```
Utdata ska visa de nyligen tillagda confcompool1 i AKS-klustret.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
Om resultatet matchar ovanstående är ditt AKS-kluster nu redo att köra konfidentiella program.

## <a name="hello-world-from-isolated-enclave-application"></a>Hello World från isolerade enklaven-program <a id="hello-world"></a>
Skapa en fil med namnet *Hello-World-enklaven. yaml* och klistra in följande yaml-manifest. Du hittar den här öppna enklaven-baserade exempel program koden i det [Öppna enklaven-projektet](https://github.com/openenclave/openenclave/tree/master/samples/helloworld).

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

Använd nu kommandot kubectl Apply för att skapa ett exempel jobb som ska startas i en säker enklaven, som du ser i följande exempel utdata:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Du kan bekräfta att arbets belastningen har skapat en enklaven (Trusted Execution Environment) genom att köra följande kommandon:

```console
$ kubectl get jobs -l app=sgx-test
```

```console
$ kubectl get jobs -l app=sgx-test
NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test
```

```console
$ kubectl get pods -l app=sgx-test
NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test
```

```console
$ kubectl logs -l app=sgx-test
Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort associerade nodkonfigurationer eller ta bort AKS-klustret använder du följande kommandon:

Tar bort AKS-klustret
``````azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```
Removing the confidential computing node pool

``````azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
``````

## <a name="next-steps"></a>Nästa steg

Köra python, Node osv. Program konfidentiellt genom konfidentiella behållare genom att besöka [exempel på konfidentiella behållare](https://github.com/Azure-Samples/confidential-container-samples).

Kör enklaven-medvetna program genom att besöka [enklaven-medvetna Azure Container-exempel](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).



