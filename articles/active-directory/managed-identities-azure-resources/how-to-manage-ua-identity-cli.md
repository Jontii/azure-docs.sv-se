---
title: Hantera användardefinierad hanterad identitet – Azure CLI – Azure AD
description: Stegvisa instruktioner för hur du skapar, visar och tar bort en användardefinierad hanterad identitet med hjälp av Azure CLI.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 79ced126cb209759502e2960bf401349f811ca32
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89014378"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Skapa, Visa eller ta bort en användardefinierad hanterad identitet med hjälp av Azure CLI


Hanterade identiteter för Azure-resurser ger Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva ange autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du skapar, visar och tar bort en användardefinierad hanterad identitet med hjälp av Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en tilldelad och användardefinierad hanterad identitet](overview.md#managed-identity-types)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Det finns tre alternativ för att köra CLI-skript exempel:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure Portal (se nästa avsnitt).
    - Använd den inbäddade Azure Cloud Shell via knappen "prova", som finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda en lokal CLI-konsol. Logga in på Azure med hjälp `az login` av ett konto som är associerat med den Azure-prenumeration under vilken du vill distribuera den tilldelade hanterade identiteten.


> [!NOTE]
> För att kunna ändra användar behörigheter när du använder en app servivce-huvudobjekt med CLI måste du ge tjänstens huvud namn ytterligare behörigheter i Azure AD Graph API som delar av CLI utför GET-begäranden mot Graph API. Annars kan det hända att du får ett meddelande om otillräcklig behörighet att slutföra åtgärden. Om du vill göra detta måste du gå till appens registrering i Azure Active Directory, välja din app, klicka på API-behörigheter, rulla nedåt och välja Azure Active Directory Graph. Välj program behörigheter och Lägg sedan till de behörigheter som krävs. 



[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet 

För att skapa en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitets deltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Använd kommandot [AZ Identity Create](/cli/azure/identity#az-identity-create) för att skapa en användardefinierad hanterad identitet. `-g`Parametern anger den resurs grupp där den tilldelade hanterade identiteten ska skapas och `-n` parametern anger dess namn. Ersätt `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` parameter värden och med dina egna värden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista användare-tilldelade hanterade identiteter

Om du vill visa en lista över/läsa en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitet](/azure/role-based-access-control/built-in-roles#managed-identity-operator) eller [hanterad identitets deltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Om du vill visa en lista över användarspecifika hanterade identiteter använder du kommandot [AZ Identity List](/cli/azure/identity#az-identity-list) . Ersätt `<RESOURCE GROUP>` med ditt eget värde:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
I JSON-svaret har användare tilldelade hanterade identiteter `"Microsoft.ManagedIdentity/userAssignedIdentities"` värdet returnerat för nyckel `type` .

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en användare som tilldelats en hanterad identitet

För att ta bort en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitets deltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Om du vill ta bort en användardefinierad hanterad identitet använder du kommandot [AZ Identity Delete](/cli/azure/identity#az-identity-delete) .  Parametern-n anger dess namn och parametern-g anger resurs gruppen där den användare som tilldelats hanterade identiteten skapades. Ersätt- `<USER ASSIGNED IDENTITY NAME>` och- `<RESOURCE GROUP>` parameter värden med dina egna värden:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Om du tar bort en tilldelad hanterad identitet tas inte referensen bort från alla resurser som den har tilldelats. Ta bort de från VM/VMSS med `az vm/vmss identity remove` kommandot

## <a name="next-steps"></a>Nästa steg

En fullständig lista över Azure CLI Identity-kommandon finns i [AZ-identitet](/cli/azure/identity).

Information om hur du tilldelar en användardefinierad hanterad identitet till en virtuell Azure-dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
