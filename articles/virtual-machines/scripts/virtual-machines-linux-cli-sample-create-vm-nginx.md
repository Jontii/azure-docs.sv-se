---
title: Skriptexempel för Azure CLI – Skapa en virtuell Linux-dator med NGINX
description: Skriptexempel för Azure CLI – Skapa en virtuell Linux-dator med NGINX
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 00bbc74a78be93dd7167625fa98aa7f9500c1be2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502077"
---
# <a name="create-a-vm-with-nginx"></a>Skapa en virtuell dator med NGINX

Det här skriptet skapar en virtuell Azure-dator och använder sedan det anpassade skripttillägget för virtuella Azure-datorer till att installera NGINX. När skriptet har körts kan du gå till demowebbplatsen med den virtuella datorns offentliga IP-adress.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nginx/create-vm-nginx.sh "Quick Create VM")]

## <a name="custom-script-extension"></a>Anpassat skripttillägg

Det anpassade skripttillägget kopierar skriptet till den virtuella datorn. Skriptet körs och installerar och konfigurerar en NGINX-webbserver.

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en virtuell dator och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az vm create](/cli/azure/vm) | Skapar den virtuella datorn. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter.  |
| [az vm open-port](/cli/azure/network/nsg/rule) | Skapar en regel för nätverkssäkerhetsgrupp som tillåter inkommande trafik. I det här exemplet öppnas port 80 för HTTP-trafik. |
| [azure vm extension set](/cli/azure/vm/extension) | Lägger till och kör ett VM-tillägg till en virtuell dator. I det här exemplet används det anpassade skripttillägget till att installera NGINX.|
| [az group delete](/cli/azure/vm/extension) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [Dokumentation för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
