---
title: Utöka lagringen automatiskt – Azure CLI – Azure Database for MariaDB
description: I den här artikeln beskrivs hur du kan aktivera automatisk storleks ökning med hjälp av Azure CLI i Azure Database for MariaDB.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: e46539e76eec73554f6e57ea09a373b79520def9
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426085"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Utöka Azure Database for MariaDB lagring automatiskt med hjälp av Azure CLI
I den här artikeln beskrivs hur du kan konfigurera en Azure Database for MariaDB Server lagring så att den växer utan att arbets belastningen påverkas.

Servern som [når lagrings gränsen](concepts-pricing-tiers.md#reaching-the-storage-limit)är skrivskyddad. Om automatisk storleks ökning har Aktiver ATS för servrar med mindre än 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5 GB så snart det lediga lagrings utrymmet är lägre än 1 GB eller 10% av det allokerade lagrings utrymmet. För servrar som har mer än 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5% när det lediga lagrings utrymmet är under 5% av den allokerade lagrings storleken. De maximala lagrings gränser som anges [här](concepts-pricing-tiers.md#storage) gäller.

## <a name="prerequisites"></a>Krav
För att slutföra den här instruktions guiden behöver du:
- En [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Den här instruktions guiden kräver att du använder Azure CLI version 2,0 eller senare. Bekräfta versionen genom att ange i kommando tolken för Azure CLI `az --version` . Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="enable-mariadb-server-storage-auto-grow"></a>Aktivera MariaDB Server Storage Auto-växer

Aktivera server för automatisk storleks ökning på en befintlig server med följande kommando:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Aktivera automatisk storleks ökning av servern när du skapar en ny server med följande kommando:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hur du skapar aviseringar för mått](howto-alert-metric.md).
