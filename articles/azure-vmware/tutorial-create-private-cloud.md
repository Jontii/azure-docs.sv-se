---
title: Självstudie – distribuera vSphere-kluster i Azure
description: Lär dig hur du distribuerar ett vSphere-kluster i Azure med hjälp av Azure VMware-lösningen
ms.topic: tutorial
ms.date: 11/19/2020
ms.openlocfilehash: 93937f8ca0918494810885f5cb45de571a6e1529
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966317"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>Självstudie: Distribuera ett privat moln för Azure VMware-lösningar i Azure

Med Azure VMware-lösningen kan du distribuera ett vSphere-kluster i Azure. Den minsta första distributionen är tre värdar. Ytterligare värdar kan läggas till en i taget, upp till högst 16 värdar per kluster. 

Eftersom Azure VMware-lösningen inte tillåter att du hanterar ditt privata moln med din lokala vCenter vid lanseringen krävs ytterligare konfiguration. Dessa procedurer och relaterade krav beskrivs i den här självstudien.

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Skapa ett privat moln för Azure VMware-lösningen
> * Verifiera att det privata molnet har distribuerats

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Lämpliga administrativa rättigheter och behörigheter för att skapa ett privat moln.
- Se till att du har rätt nätverks konfiguration enligt beskrivningen i [Självstudier: check lista för nätverk](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Registrera resursprovidern

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="create-a-private-cloud"></a>Skapa ett privat moln

Du kan skapa ett privat moln i Azure VMware-lösningen med hjälp av [Azure Portal](#azure-portal) eller med hjälp av [Azure CLI](#azure-cli).

### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

I stället för Azure Portal skapa ett privat moln i Azure VMware-lösningen kan du använda Azure CLI med hjälp av Azure Cloud Shell.  En lista över kommandon som du kan använda med Azure VMware-lösningen finns i [Azure VMware-kommandon](/cli/azure/ext/vmware/vmware).

#### <a name="open-azure-cloud-shell"></a>Öppna Azure Cloud Shell

Välj **testa det** övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Välj **Kopiera** för att kopiera kod blocken, klistra in den i Cloud Shell och tryck på **RETUR** för att köra den.

#### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot `[az group create](/cli/azure/group)`. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Skapa ett privat moln

Ange ett namn för resurs gruppen och det privata molnet, en plats och klustrets storlek.

| Egenskap  | Beskrivning  |
| --------- | ------------ |
| **-g** (resurs gruppens namn)     | Namnet på resurs gruppen för dina privata moln resurser.        |
| **-n** (namn på privat moln)     | Namnet på ditt privata moln i Azure VMware-lösningen.        |
| **--plats**     | Den plats som används för ditt privata moln.         |
| **--kluster storlek**     | Klustrets storlek. Minimivärdet är 3.         |
| **--Network-block**     | CIDR IP-adressens nätverks block som ska användas för ditt privata moln. Adress blocket får inte överlappa adress block som används i andra virtuella nätverk som finns i din prenumeration och lokala nätverk.        |
| **--SKU** | SKU-värdet: AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-an-azure-vmware-solution-private-cloud"></a>Ta bort ett privat moln i Azure VMware-lösningen

Om du har ett privat moln i Azure VMware-lösningen som du inte längre behöver kan du ta bort det. Ett privat moln i Azure VMware-lösningen innehåller en isolerad nätverks domän, ett eller flera etablerade vSphere-kluster på dedikerade server värdar och flera virtuella datorer. När ett privat moln tas bort tas alla virtuella datorer, deras data och kluster bort. De dedikerade Bare Metal-värdarna rensas säkert och returneras till den kostnads fria poolen. Den nätverks domän som etablerades för kunden tas bort.  

> [!CAUTION]
> Borttagning av det privata molnet är en åtgärd som inte kan ångras. När det privata molnet har tagits bort går det inte att återställa data eftersom de avslutar alla aktiva arbets belastningar och komponenter och förstör alla privata moln data och konfigurations inställningar, inklusive offentliga IP-adresser.

### <a name="prerequisites"></a>Krav

När ett privat moln har tagits bort finns det inget sätt att återställa de virtuella datorerna och deras data. Om data för den virtuella datorn kommer att krävas senare måste administratören först säkerhetskopiera alla data innan du tar bort det privata molnet.

### <a name="steps-to-delete-an-azure-vmware-solution-private-cloud"></a>Steg för att ta bort ett privat moln i Azure VMware-lösningen

1. Gå till sidan Azure VMware-lösningar i Azure Portal.

2. Välj det privata moln som ska tas bort.
 
3. Ange namnet på det privata molnet och välj **Ja**. Under några timmar slutförs borttagnings processen.  

## <a name="azure-vmware-commands"></a>Azure VMware-kommandon

En lista över kommandon som du kan använda med Azure VMware-lösningen finns i [Azure VMware-kommandon](/cli/azure/ext/vmware/vmware).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du lärt dig att:

> [!div class="checklist"]
> * Skapa ett privat moln för Azure VMware-lösningen
> * Verifiera att det privata molnet har distribuerats
> * Ta bort ett privat moln i Azure VMware-lösningen

Fortsätt till nästa självstudie och lär dig hur du skapar en hopp ruta. Du använder rutan hopp för att ansluta till din miljö så att du kan hantera ditt privata moln lokalt.


> [!div class="nextstepaction"]
> [Få åtkomst till ett privat moln i Azure VMware-lösningen](tutorial-access-private-cloud.md)