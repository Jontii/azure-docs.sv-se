---
title: Självstudie – ta bort ett Azure Red Hat OpenShift-kluster
description: I den här självstudien får du lära dig hur du tar bort ett Azure Red Hat OpenShift-kluster med Azure CLI
author: sakthi-vetrivel
ms.custom: fasttrack-edit
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 254a9737b805aeeae2008923a8178cd971602132
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677763"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>Självstudie: ta bort ett Azure Red Hat OpenShift 4-kluster

I den här självstudien, som är del tre av tre, är ett kluster med OpenShift i Azure Red Hat som kör OpenShift 4 borttaget. Lär dig att:

> [!div class="checklist"]
> * Ta bort ett Azure Red Hat OpenShift-kluster


## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier skapade du ett Azure Red Hat OpenShift-kluster och anslöt till med hjälp av webb konsolen OpenShift. Om du inte har gjort dessa steg och vill följa med, börjar du med [självstudie 1 – Skapa ett Azure Red Hat OpenShift 4-kluster.](tutorial-create-cluster.md)

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI-version 2.6.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Om du kör Azure CLI lokalt kan du köra `az login` för att logga in på Azure.

```bash
az login
```

Om du har åtkomst till flera prenumerationer kan `az account set -s {subscription ID}` du köra Ersätt `{subscription ID}` med den prenumeration som du vill använda.

## <a name="delete-the-cluster"></a>Ta bort klustret

I tidigare självstudier angav du följande variabler.

```bash
CLUSTER=yourclustername
RESOURCEGROUP=yourresourcegroup
```

Ta bort klustret med dessa värden:

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

Sedan uppmanas du att bekräfta om du vill ta bort klustret. När du har bekräftat med `y` , tar det flera minuter att ta bort klustret. När kommandot har slutförts tas hela resurs gruppen och alla resurser i den, inklusive klustret, bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:
> [!div class="checklist"]
> * Ta bort ett Azure Red Hat OpenShift 4-kluster

Lär dig mer om att använda OpenShift med den officiella [Red Hat OpenShift-dokumentationen](https://www.openshift.com/try)
