---
title: Aktivera värdbaserad kryptering på Azure Kubernetes service (AKS)
description: Lär dig hur du konfigurerar en värdbaserad kryptering i ett Azure Kubernetes service-kluster (AKS)
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 4b5deeec0b76520952345e9b03135fa094a1f78e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87986873"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Värdbaserad kryptering på Azure Kubernetes service (AKS) (för hands version)

Med värdbaserad kryptering krypteras de data som lagras på VM-värden för virtuella datorer i AKS-agenten i vila och flöden som krypteras till lagrings tjänsten. Det innebär att de temporära diskarna är krypterade i vila med plattforms hanterade nycklar. Cacheminnet för operativ system och data diskar krypteras i vila med antingen plattforms hanterade nycklar eller Kundhanterade nycklar beroende på vilken krypterings typ som angetts på dessa diskar. När du använder AKS krypteras som standard operativ system och data diskar i vila med plattforms hanterade nycklar, vilket innebär att cacheminnena för de här diskarna också är som standard krypterade i vila med plattforms hanterade nycklar.  Du kan ange egna hanterade nycklar [genom att ta med dina egna nycklar (BYOK) med Azure-diskar i Azure Kubernetes-tjänsten](azure-disk-customer-managed-keys.md). Cachen för diskarna krypteras sedan med den nyckel som du anger i det här steget.


## <a name="before-you-begin"></a>Innan du börjar

Den här funktionen kan bara ställas in när klustret skapas eller när en nod skapas.

> [!NOTE]
> Värdbaserad kryptering är tillgänglig i Azure- [regioner][supported-regions] som har stöd för kryptering på Server sidan av Azure Managed disks och endast med vissa [storlekar som stöds för virtuella datorer][supported-sizes].

### <a name="prerequisites"></a>Förutsättningar

- Se till att du har `aks-preview` CLI-tillägget v 0.4.55 eller senare installerat
- Se till att du har `EncryptionAtHost` funktions flaggan under `Microsoft.Compute` aktive rad.
- Se till att du har `EnableEncryptionAtHostPreview` funktions flaggan under `Microsoft.ContainerService` aktive rad.

### <a name="register-encryptionathost--preview-features"></a>Registrera för `EncryptionAtHost`  hands versions funktioner

Om du vill skapa ett AKS-kluster som använder värdbaserad kryptering måste du aktivera- `EnableEncryptionAtHostPreview` och- `EncryptionAtHost` funktions flaggorna i din prenumeration.

Registrera `EncryptionAtHost` funktions flaggan med hjälp av kommandot [AZ Feature register][az-feature-register] som visas i följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.Compute" --name "EncryptionAtHost"

az feature register --namespace "Microsoft.ContainerService"  --name "EnableEncryptionAtHostPreview"
```

Det tar några minuter för statusen att visa *registrerad*. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Compute/EncryptionAtHost')].{Name:name,State:properties.state}"

az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableEncryptionAtHostPreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av- `Microsoft.ContainerService` och- `Microsoft.Compute` resurs leverantörerna med [AZ Provider register][az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.Compute

az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägget aks-preview

Om du vill skapa ett AKS-kluster som är värdbaserad kryptering, behöver du det senaste *AKS-Preview CLI-* tillägget. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ-tillägg][az-extension-add] , eller Sök efter tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Begränsningar

- Kan bara aktive ras på nya nodkonfigurationer eller nya kluster.
- Kan bara aktive ras i [Azure-regioner][supported-regions] som har stöd för kryptering på Server sidan av Azure Managed disks och endast med vissa storlekar som stöds för [virtuella datorer][supported-sizes].
- Kräver ett AKS-kluster och Node-pool baserad på Virtual Machine Scale Sets (VMSS) som *typ av virtuell dator*.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Använd värdbaserad kryptering på nya kluster (förhands granskning)

Konfigurera kluster agentens noder så att de använder värdbaserad kryptering när klustret skapas. Använd `--aks-custom-headers` flaggan för att ange `EnableEncryptionAtHost` sidhuvudet.

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

Om du vill skapa kluster utan värdbaserad kryptering kan du göra det genom att utesluta den anpassade `--aks-custom-headers` parametern.

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Använd värdbaserad kryptering på befintliga kluster (förhands granskning)

Du kan aktivera värdbaserad kryptering på befintliga kluster genom att lägga till en ny Node-pool i klustret. Konfigurera en ny Node-pool så att den använder värdbaserad kryptering med hjälp av `--aks-custom-headers` flaggan.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

Om du vill skapa nya resurspooler utan den värdbaserade krypterings funktionen kan du göra det genom att utesluta den anpassade `--aks-custom-headers` parametern.

## <a name="next-steps"></a>Nästa steg

Läs mer om den [värdbaserade krypteringen](../virtual-machines/linux/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)genom [att granska metod tips för AKS-kluster säkerhet][best-practices-security] .


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/linux/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/linux/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
