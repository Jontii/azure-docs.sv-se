---
title: inkludera fil
description: inkludera fil
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 66a3ecd82ab61f25c99fd1268d9ce7567b057d66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86050309"
---
## <a name="prepare-for-akv-integration"></a>Förbered för AKV-integrering
För att kunna använda Azure Key Vault-integrering för att konfigurera SQL Server VM finns det flera krav: 

1. [Installera Azure PowerShell](#install)
2. [Skapa en Azure Active Directory](#register)
3. [Skapa ett nyckel valv](#createkeyvault)

I följande avsnitt beskrivs dessa krav och den information som du behöver samla in för att köra PowerShell-cmdletar senare.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a> Installera Azure PowerShell
Kontrol lera att du har installerat den senaste Azure PowerShell-modulen. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a> Registrera ett program i Azure Active Directory

Först måste du ha en [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) i din prenumeration. På så sätt kan du med många fördelar ge dig behörighet till ditt nyckel valv för vissa användare och program.

Registrera sedan ett program med AAD. Detta ger dig ett tjänst huvud konto som har åtkomst till ditt nyckel valv, som den virtuella datorn kommer att behöva. I Azure Key Vault artikeln kan du hitta de här stegen i avsnittet [Registrera ett program med Azure Active Directory](../articles/key-vault/key-vault-manage-with-cli2.md#registering-an-application-with-azure-active-directory) , eller så kan du se stegen med skärm bilder i **avsnittet hämta en identitet för programmet** i [det här blogg inlägget](https://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Innan du slutför de här stegen måste du samla in följande information under registreringen som behövs senare när du aktiverar Azure Key Vault-integrering på den virtuella SQL-datorn.

* När programmet har lagts till hittar du **program-ID: t** (även kallat AAD-ClientID eller AppID) på bladet **registrerad app** .
    Program-ID: t tilldelas senare till **$spName** -parametern (tjänstens huvud namn) i PowerShell-skriptet för att aktivera Azure Key Vault-integrering.

   ![Program-ID](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Under de här stegen när du skapar din nyckel kopierar du hemligheten för nyckeln som visas i följande skärm bild. Den här nyckel hemligheten tilldelas senare till den **$spSecret** -parametern (tjänstens huvud namns hemlighet) i PowerShell-skriptet.

   ![AAD-hemlighet](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* Program-ID: t och hemligheten kommer också att användas för att skapa en autentiseringsuppgift i SQL Server.

* Du måste godkänna att det här nya program-ID: t (eller klient-ID: t) har följande åtkomst behörigheter: **Get**, **wrapKey**, **unwrapKey**. Detta görs med cmdleten [set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) . Mer information finns i [Azure Key Vault översikt](../articles/key-vault/key-vault-overview.md).

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a> Skapa ett nyckel valv
För att kunna använda Azure Key Vault för att lagra de nycklar som ska användas för kryptering i din virtuella dator måste du ha åtkomst till ett nyckel valv. Om du inte redan har konfigurerat nyckel valvet skapar du ett genom att följa stegen i artikeln [komma igång med Azure Key Vault](../articles/key-vault/key-vault-overview.md) . Innan du utför de här stegen finns det information som du behöver samla in under den här konfigurationen som behövs senare när du aktiverar Azure Key Vault-integrering på den virtuella SQL-datorn.

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'
```

När du kommer till steget Skapa ett nyckel valv noterar du den returnerade **vaultUri** -egenskapen, som är Key Vault-URL: en. I exemplet i det steget, som visas nedan, är nyckel valvets namn ContosoKeyVault, och därför skulle nyckel valvets URL vara https://contosokeyvault.vault.azure.net/ .

Nyckel valvets URL tilldelas senare till **$akvURL** -parametern i PowerShell-skriptet för att aktivera Azure Key Vault-integrering.

När nyckel valvet har skapats måste vi lägga till en nyckel till nyckel valvet. den här nyckeln kommer att hänvisas till när vi skapar en asymmetrisk nyckel skapa i SQL Server senare.
