---
title: Utöka lagringen automatiskt – Azure PowerShell – Azure Database for MariaDB
description: I den här artikeln beskrivs hur du kan aktivera automatisk storleks ökning med PowerShell i Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2d03a67fc8a8172573598662ad9770b28493e9a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87497111"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-server-using-powershell"></a>Utöka lagringen automatiskt i Azure Database for MariaDB server med PowerShell

I den här artikeln beskrivs hur du kan konfigurera en Azure Database for MariaDB Server lagring så att den växer utan att arbets belastningen påverkas.

Med automatisk storleks ökning förhindrar du att servern [når lagrings gränsen](/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit) och blir skrivskyddad. För servrar med 100 GB eller mindre allokerat lagrings utrymme ökas storleken med 5 GB när det lediga utrymmet är under 10%. För servrar som har mer än 100 GB allokerat lagrings utrymme ökas storleken med 5% när det lediga utrymmet är lägre än 10 GB. De maximala lagrings gränserna gäller enligt vad som anges i avsnittet lagring på [Azure Database for MariaDB pris nivåer](/azure/mariadb/concepts-pricing-tiers#storage).

> [!IMPORTANT]
> Kom ihåg att lagringen bara kan skalas upp, inte nedåt.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

- [AZ PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps) installeras lokalt eller [Azure Cloud Shell](https://shell.azure.com/) i webbläsaren
- En [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Även om PowerShell-modulen AZ. MariaDb är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av följande kommando: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> När AZ. MariaDb PowerShell-modulen är allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-modulen som är tillgängliga internt inifrån Azure Cloud Shell.

Om du väljer att använda PowerShell lokalt ansluter du till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

## <a name="enable-mariadb-server-storage-auto-grow"></a>Aktivera automatisk storleks ökning i MariaDB Server Storage

Aktivera automatisk storleks lagring på servern på en befintlig server med följande kommando:

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Aktivera server utöka lagring automatiskt när du skapar en ny server med följande kommando:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och hantera Läs repliker i Azure Database for MariaDB med PowerShell](howto-read-replicas-powershell.md).
