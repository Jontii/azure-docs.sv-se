---
title: inkludera fil
description: inkludera fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 74d7bd087df4b00c0bafb5ec33fbbdfa5c57b379
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "67187183"
---
Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/) och följ anvisningarna på skärmen. Mer information om att logga in finns i [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).

```azurecli
az login
```

Om du har fler än en Azure-prenumeration anger du prenumerationerna för kontot.

```azurecli
az account list --all
```

Ange den prenumeration som du vill använda.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```
