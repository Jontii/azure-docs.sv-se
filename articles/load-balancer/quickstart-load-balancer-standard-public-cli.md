---
title: 'Snabb start: skapa en offentlig belastningsutjämnare – Azure CLI'
titleSuffix: Azure Load Balancer
description: Den här snabbstarten visar hur du skapar en offentlig lastbalanserare med Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: allensu
ms.custom: mvc, devx-track-javascript, devx-track-azurecli
ms.openlocfilehash: 95f8466944d4131b3356f44d65171bf1b6cc7a82
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640811"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Snabbstart: Skapa en offentlig lastbalanserare som lastbalanserar virtuella datorer med Azure CLI

Kom igång med Azure Load Balancer med hjälp av Azure CLI och skapa en offentlig belastningsutjämnare och tre virtuella datorer.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure CLI installerat lokalt eller Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Om du väljer att installera och använda CLI lokalt kräver den här snabb starten Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp med [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create):

* Med namnet **myResourceGroupLB**. 
* På den **östra** platsen.

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Standard-SKU-belastningsutjämnare rekommenderas för produktions arbets belastningar. Mer information om SKU: er finns i **[Azure Load Balancer SKU: er](skus.md)**.


## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

För att du ska kunna komma åt din webbapp på Internet behöver du en offentlig IP-adress för lastbalanseraren. 

Använd [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) to:

* Skapa en standard zon för redundant offentlig IP-adress med namnet **myPublicIP**.
* I **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard
```

Så här skapar du en zonindelade redundant offentlig IP-adress i Zon 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Skapa standard Load Balancer

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

  * En IP-pool för klient delen som tar emot inkommande nätverks trafik i belastningsutjämnaren.
  * En server dels-IP-pool där frontend-poolen skickar den belastningsutjämnade nätverks trafiken.
  * En hälso avsökning som avgör hälso tillståndet för VM-instanser i Server delen.
  * En belastnings Utjämnings regel som definierar hur trafiken distribueras till de virtuella datorerna.

### <a name="create-the-load-balancer-resource"></a>Skapa belastnings Utjämnings resursen

Skapa en offentlig belastningsutjämnare med [AZ Network lb Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create):

* Med namnet **myLoadBalancer**.
* En frontend-pool med namnet ' **frontend**'.
* En backend-pool med namnet **myBackEndPool**.
* Kopplad till den offentliga IP- **myPublicIP** som du skapade i föregående steg. 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Skapar hälsoavsökningen

En hälso avsökning kontrollerar alla virtuella dator instanser för att säkerställa att de kan skicka nätverks trafik. 

En virtuell dator med en misslyckad avsöknings kontroll har tagits bort från belastningsutjämnaren. Den virtuella datorn läggs tillbaka i belastningsutjämnaren när problemet är löst.

Skapa en hälso avsökning med [AZ Network lb PROBE Create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

* Övervakar hälso tillståndet för de virtuella datorerna.
* Med namnet **myHealthProbe**.
* Protokoll- **TCP**.
* Övervaknings **Port 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Skapa lastbalanseringsregeln

En belastnings Utjämnings regel definierar:

* IP-konfiguration för klient delen för inkommande trafik.
* Server delens IP-pool för att ta emot trafiken.
* Käll-och mål port som krävs. 

Skapa en belastnings Utjämnings regel med [AZ Network lb Rule Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create):

* Med namnet **myHTTPRule**
* Lyssnar på **Port 80** i frontend **-poolen för klient delen.**
* Skickar belastningsutjämnad nätverks trafik till Server dels adresspoolen **myBackEndPool** med **port 80**. 
* Använda **myHealthProbe**för hälso avsökning.
* Protokoll- **TCP**.
* Aktivera utgående käll Network Address Translation (SNAT) med klient delens IP-adress.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true 
```

## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk

Innan du distribuerar virtuella datorer och testar belastningsutjämnaren, skapar du de stödda virtuella nätverks resurserna.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med [AZ Network VNet Create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* Med namnet **myVNet**.
* Undernät med namnet **myBackendSubnet**.
* I resurs gruppen **myResourceGroupLB** .
* Plats för **öster**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --subnet-name myBackendSubnet
```

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

För en standard belastningsutjämnare måste de virtuella datorerna i Server dels adressen ha nätverks gränssnitt som tillhör en nätverks säkerhets grupp. 

Skapa en nätverks säkerhets grupp med [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* Med namnet **myNSG**.
* I resurs gruppen **myResourceGroupLB**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

Skapa en regel för nätverks säkerhets grupp med [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* Med namnet **myNSGRuleHTTP**.
* I den nätverks säkerhets grupp som du skapade i föregående steg, **myNSG**.
* I resurs gruppen **myResourceGroupLB**.
* Protokoll- **TCP**.
* Riktningen **inkommande**.
* Källa **(*)**.
* Mål **(*)**.
* Målport- **port 80**.
* **Tillåt**åtkomst.
* Prioritet **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Skapa nätverks gränssnitt för virtuella datorer

Skapa tre nätverks gränssnitt med [AZ Network NIC Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* Med namnet **myNicVM1**.
* I resurs gruppen **myResourceGroupLB**.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.
* Ansluten till belastningsutjämnare- **myLoadBalancer** i **myBackEndPool**.

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```
#### <a name="vm2"></a>VM2

* Med namnet **myNicVM2**.
* I resurs gruppen **myResourceGroupLB**.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.
* Ansluten till belastningsutjämnare- **myLoadBalancer** i **myBackEndPool**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```
#### <a name="vm3"></a>VM3

* Med namnet **myNicVM3**.
* I resurs gruppen **myResourceGroupLB**.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.
* Ansluten till belastningsutjämnare- **myLoadBalancer** i **myBackEndPool**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du:

* En moln konfigurations fil med namnet **cloud-init.txt** för Server konfigurationen.
* Tre virtuella datorer som ska användas som backend-servrar för belastningsutjämnaren.

### <a name="create-cloud-init-configuration-file"></a>Skapa konfigurations fil för Cloud-Init

Använd en konfigurations fil för Cloud-Init för att installera NGINX och köra en Hello World Node.js-app på en virtuell Linux-dator. 

I ditt nuvarande gränssnitt skapar du en fil med namnet cloud-init.txt. Kopiera och klistra in följande konfiguration i gränssnittet. Se till att du kopierar hela Cloud-Init-filen korrekt, särskilt den första raden:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```
### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa de virtuella datorerna med [AZ VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>VM1
* Med namnet **myVM1**.
* I resurs gruppen **myResourceGroupLB**.
* Ansluten till nätverks gränssnittet **myNicVM1**.
* **UbuntuLTS**för avbildning av virtuell dator.
* Konfigurations filen **cloud-init.txt** du skapade i steg ovan.
* I **zon 1**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Med namnet **myVM2**.
* I resurs gruppen **myResourceGroupLB**.
* Ansluten till nätverks gränssnittet **myNicVM2**.
* **UbuntuLTS**för avbildning av virtuell dator.
* Konfigurations filen **cloud-init.txt** du skapade i steg ovan.
* I **zon 2**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Med namnet **myVM3**.
* I resurs gruppen **myResourceGroupLB**.
* Ansluten till nätverks gränssnittet **myNicVM3**.
* **UbuntuLTS**för avbildning av virtuell dator.
* Konfigurations filen **cloud-init.txt** du skapade i steg ovan.
* I **zon 3**.

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 3 \
    --no-wait
```
Det kan ta några minuter innan de virtuella datorerna distribueras.

## <a name="create-outbound-rule-configuration"></a>Skapa utgående regel konfiguration
Utgående regler för belastningsutjämnare konfigurerar utgående SNAT för virtuella datorer i backend-poolen. 

Mer information om utgående anslutningar finns i [utgående anslutningar i Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-public-ip-address-or-public-ip-prefix"></a>Skapa utgående offentlig IP-adress eller offentligt IP-prefix.

Använd [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) för att skapa en enskild IP-adress för utgående anslutningar.  

Använd [AZ Network Public-IP prefix Create](https://docs.microsoft.com/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) för att skapa ett offentligt IP-prefix för den utgående anslutningen.

Mer information om skalning av utgående NAT och utgående anslutningar finns i [skala utgående NAT med flera IP-adresser](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scale).

#### <a name="public-ip"></a>Offentlig IP-adress

* Med namnet **myPublicIPOutbound**.
* I **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard
```

Så här skapar du en zonindelade redundant offentlig IP-adress i Zon 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```
#### <a name="public-ip-prefix"></a>Offentligt IP-prefix

* Med namnet **myPublicIPPrefixOutbound**.
* I **myResourceGroupLB**.
* Prefixlängden på **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Så här skapar du ett zonindelade redundant offentlig IP-prefix i Zon 1:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

### <a name="create-outbound-frontend-ip-configuration"></a>Skapa utgående IP-konfiguration för klient delen

Skapa en ny IP-konfiguration för klient delen med [AZ Network lb frontend-IP Create ](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create):

Välj kommandona offentlig IP-adress eller offentlig IP-prefix baserat på beslut i föregående steg.

#### <a name="public-ip"></a>Offentlig IP-adress

* Med namnet **myFrontEndOutbound**.
* I resurs gruppen **myResourceGroupLB**.
* Associerat med offentliga IP- **myPublicIPOutbound**.
* Kopplad till belastningsutjämnare **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Offentligt IP-prefix

* Med namnet **myFrontEndOutbound**.
* I resurs gruppen **myResourceGroupLB**.
* Associerat med det offentliga IP-prefixet **myPublicIPPrefixOutbound**.
* Kopplad till belastningsutjämnare **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Skapa utgående pool

Skapa en ny utgående pool med [AZ Network lb Address-pool Create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create):

* Med namnet **myBackEndPoolOutbound**.
* I resurs gruppen **myResourceGroupLB**.
* Kopplad till belastningsutjämnare **myLoadBalancer**.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Skapa utgående regel

Skapa en ny utgående regel för den utgående backend-poolen med [AZ Network lb utgående-Rule Create](https://docs.microsoft.com/cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-create):

* Med namnet **myOutboundRule**.
* I resurs gruppen **myResourceGroupLB**.
* Kopplad till belastningsutjämnare **myLoadBalancer**
* Associerad med frontend- **myFrontEndOutbound**.
* Protokoll **alla**.
* Tids gräns för inaktivitet på **15**.
* **10000** utgående portar.
* Kopplad till **myBackEndPoolOutbound**i Server delen.

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Lägg till virtuella datorer i utgående pool

Lägg till de virtuella datorerna i den utgående poolen med [AZ Network NIC IP-config Address-pool Add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* I backend-adresspoolen **myBackEndPoolOutbound**.
* I resurs gruppen **myResourceGroupLB**.
* Associerad med nätverks gränssnittet **myNicVM1** och **ipconfig1**.
* Kopplad till belastningsutjämnare **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* I backend-adresspoolen **myBackEndPoolOutbound**.
* I resurs gruppen **myResourceGroupLB**.
* Associerad med nätverks gränssnittet **myNicVM2** och **ipconfig1**.
* Kopplad till belastningsutjämnare **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* I backend-adresspoolen **myBackEndPoolOutbound**.
* I resurs gruppen **myResourceGroupLB**.
* Associerad med nätverks gränssnittet **myNicVM3** och **ipconfig1**.
* Kopplad till belastningsutjämnare **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**Grundläggande SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Standard-SKU-belastningsutjämnare rekommenderas för produktions arbets belastningar. Mer information om SKU: er finns i **[Azure Load Balancer SKU: er](skus.md)**.


## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

För att du ska kunna komma åt din webbapp på Internet behöver du en offentlig IP-adress för lastbalanseraren. 

Använd [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) to:

* Skapa en standard zon för redundant offentlig IP-adress med namnet **myPublicIP**.
* I **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Skapa Basic Load Balancer

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

  * En IP-pool för klient delen som tar emot inkommande nätverks trafik i belastningsutjämnaren.
  * En server dels-IP-pool där frontend-poolen skickar den belastningsutjämnade nätverks trafiken.
  * En hälso avsökning som avgör hälso tillståndet för VM-instanser i Server delen.
  * En belastnings Utjämnings regel som definierar hur trafiken distribueras till de virtuella datorerna.

### <a name="create-the-load-balancer-resource"></a>Skapa belastnings Utjämnings resursen

Skapa en offentlig belastningsutjämnare med [AZ Network lb Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create):

* Med namnet **myLoadBalancer**.
* En frontend-pool med namnet ' **frontend**'.
* En backend-pool med namnet **myBackEndPool**.
* Kopplad till den offentliga IP- **myPublicIP** som du skapade i föregående steg. 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Skapar hälsoavsökningen

En hälso avsökning kontrollerar alla virtuella dator instanser för att säkerställa att de kan skicka nätverks trafik. 

En virtuell dator med en misslyckad avsöknings kontroll har tagits bort från belastningsutjämnaren. Den virtuella datorn läggs tillbaka i belastningsutjämnaren när problemet är löst.

Skapa en hälso avsökning med [AZ Network lb PROBE Create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

* Övervakar hälso tillståndet för de virtuella datorerna.
* Med namnet **myHealthProbe**.
* Protokoll- **TCP**.
* Övervaknings **Port 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Skapa lastbalanseringsregeln

En belastnings Utjämnings regel definierar:

* IP-konfiguration för klient delen för inkommande trafik.
* Server delens IP-pool för att ta emot trafiken.
* Käll-och mål port som krävs. 

Skapa en belastnings Utjämnings regel med [AZ Network lb Rule Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create):

* Med namnet **myHTTPRule**
* Lyssnar på **Port 80** i frontend **-poolen för klient delen.**
* Skickar belastningsutjämnad nätverks trafik till Server dels adresspoolen **myBackEndPool** med **port 80**. 
* Använda **myHealthProbe**för hälso avsökning.
* Protokoll- **TCP**.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk

Innan du distribuerar virtuella datorer och testar belastningsutjämnaren, skapar du de stödda virtuella nätverks resurserna.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med [AZ Network VNet Create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* Med namnet **myVNet**.
* Undernät med namnet **myBackendSubnet**.
* I resurs gruppen **myResourceGroupLB** .
* Plats för **öster**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --subnet-name myBackendSubnet
```

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

För en standard belastningsutjämnare måste de virtuella datorerna i Server dels adressen ha nätverks gränssnitt som tillhör en nätverks säkerhets grupp. 

Skapa en nätverks säkerhets grupp med [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* Med namnet **myNSG**.
* I resurs gruppen **myResourceGroupLB**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

Skapa en regel för nätverks säkerhets grupp med [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* Med namnet **myNSGRuleHTTP**.
* I den nätverks säkerhets grupp som du skapade i föregående steg, **myNSG**.
* I resurs gruppen **myResourceGroupLB**.
* Protokoll- **TCP**.
* Riktningen **inkommande**.
* Källa **(*)**.
* Mål **(*)**.
* Målport- **port 80**.
* **Tillåt**åtkomst.
* Prioritet **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Skapa nätverks gränssnitt för virtuella datorer

Skapa tre nätverks gränssnitt med [AZ Network NIC Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* Med namnet **myNicVM1**.
* I resurs gruppen **myResourceGroupLB**.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.
* Ansluten till belastningsutjämnare- **myLoadBalancer** i **myBackEndPool**.

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```
#### <a name="vm2"></a>VM2

* Med namnet **myNicVM2**.
* I resurs gruppen **myResourceGroupLB**.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.
* Ansluten till belastningsutjämnare- **myLoadBalancer** i **myBackEndPool**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```
#### <a name="vm3"></a>VM3

* Med namnet **myNicVM3**.
* I resurs gruppen **myResourceGroupLB**.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.
* Ansluten till belastningsutjämnare- **myLoadBalancer** i **myBackEndPool**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du:

* En moln konfigurations fil med namnet **cloud-init.txt** för Server konfigurationen. 
* Tillgänglighets uppsättning för de virtuella datorerna
* Tre virtuella datorer som ska användas som backend-servrar för belastningsutjämnaren.


För att verifiera att belastningsutjämnaren har skapats installerar du NGINX på de virtuella datorerna.

### <a name="create-cloud-init-configuration-file"></a>Skapa konfigurations fil för Cloud-Init

Använd en konfigurations fil för Cloud-Init för att installera NGINX och köra en Hello World Node.js-app på en virtuell Linux-dator. 

I ditt nuvarande gränssnitt skapar du en fil med namnet cloud-init.txt. Kopiera och klistra in följande konfiguration i gränssnittet. Se till att du kopierar hela Cloud-Init-filen korrekt, särskilt den första raden:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```
### <a name="create-availability-set-for-virtual-machines"></a>Skapa tillgänglighets uppsättning för virtuella datorer

Skapa tillgänglighets uppsättningen med [AZ VM Availability-set Create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create):

* Med namnet **myAvSet**.
* I resurs gruppen **myResourceGroupLB**.
* **Östra**platser.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group myResourceGroupLB \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa de virtuella datorerna med [AZ VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>VM1
* Med namnet **myVM1**.
* I resurs gruppen **myResourceGroupLB**.
* Ansluten till nätverks gränssnittet **myNicVM1**.
* **UbuntuLTS**för avbildning av virtuell dator.
* Konfigurations filen **cloud-init.txt** du skapade i steg ovan.
* I tillgänglighets uppsättningens **myAvSet**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Med namnet **myVM2**.
* I resurs gruppen **myResourceGroupLB**.
* Ansluten till nätverks gränssnittet **myNicVM2**.
* **UbuntuLTS**för avbildning av virtuell dator.
* Konfigurations filen **cloud-init.txt** du skapade i steg ovan.
* I **zon 2**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Med namnet **myVM3**.
* I resurs gruppen **myResourceGroupLB**.
* Ansluten till nätverks gränssnittet **myNicVM3**.
* **UbuntuLTS**för avbildning av virtuell dator.
* Konfigurations filen **cloud-init.txt** du skapade i steg ovan.
* I **zon 3**.

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```
Det kan ta några minuter innan de virtuella datorerna distribueras.

---

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

Hämta lastbalanserarens offentliga IP-adress med [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show). 

Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Testa lastbalanseraren" border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda kommandot [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) för att ta bort resurs gruppen, belastningsutjämnaren och alla relaterade resurser.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>Nästa steg
I den här snabb starten

* Du har skapat en standard-eller offentlig belastningsutjämnare
* Anslutna virtuella datorer. 
* Konfigurerat trafik regel för belastnings utjämning och hälso avsökning.
* Belastnings utjämning har testats.

Om du vill veta mer om Azure Load Balancer kan du fortsätta till [Azure Load Balancer?](load-balancer-overview.md) och [Load Balancer vanliga frågor och svar](load-balancer-faqs.md).

Läs mer om [Load Balancer-och tillgänglighets zoner](load-balancer-standard-availability-zones.md).
