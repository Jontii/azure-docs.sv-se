---
title: Starta om Server – Azure PowerShell-Azure Database for PostgreSQL
description: I den här artikeln beskrivs hur du startar om en Azure Database for PostgreSQL-server med PowerShell.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5037975bcfbf9b19de5cc203d26e1caf0f85893f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710166"
---
# <a name="restart-azure-database-for-postgresql-server-using-powershell"></a>Starta om Azure Database for PostgreSQL server med PowerShell

I det här avsnittet beskrivs hur du kan starta om en Azure Database for PostgreSQL-Server. Du kan behöva starta om servern för underhålls orsaker, vilket orsakar ett kort avbrott under driften.

Omstart av servern blockeras om tjänsten är upptagen. Tjänsten kan till exempel bearbeta en tidigare begärd åtgärd, till exempel skalnings virtuella kärnor.

Hur lång tid det tar att slutföra en omstart beror på återställnings processen för PostgreSQL. För att minska omstarts tiden rekommenderar vi att du minimerar mängden aktivitet som inträffar på servern innan du startar om.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

- [AZ PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps) installeras lokalt eller [Azure Cloud Shell](https://shell.azure.com/) i webbläsaren
- En [Azure Database for postgresql-server](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Även om PowerShell-modulen AZ. PostgreSql är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av följande kommando: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> När AZ. PostgreSql PowerShell-modulen är allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-modulen som är tillgängliga internt inifrån Azure Cloud Shell.

Om du väljer att använda PowerShell lokalt ansluter du till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Starta om servern

Starta om servern med följande kommando:

```azurepowershell-interactive
Restart-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en Azure Database for PostgreSQL-server med PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md)
