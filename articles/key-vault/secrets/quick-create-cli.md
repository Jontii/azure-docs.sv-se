---
title: 'Snabbstart: Ställ in och hämta en hemlighet från Azure Key Vault'
description: Snabbstart som visar hur du ställer in och hämtar en hemlighet från Azure Key Vault med hjälp av Azure CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 61907c53e0b0b0a58ea8a167583f02af35572f6f
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940489"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Snabbstart: Ställ in och hämta en hemlighet från Azure Key Vault med hjälp av Azure CLI

I den här snabb starten skapar du ett nyckel valv i Azure Key Vault med Azure CLI. Azure Key Vault är en molntjänst som fungerar som ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Mer information om Key Vault kan du läsa [översikten](../general/overview.md). Med Azure-CLI:t kan du skapa och hantera Azure-resurser med hjälp av kommandon eller skript. När du har gjort det kommer du att lagra en hemlighet.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI:t lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

Du kan logga in i Azure via CLI genom att skriva:

```azurecli
az login
```

Mer information om inloggningsalternativen via CLI finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *ContosoResourceGroup* på platsen *eastus*.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning

Därefter skapar du ett nyckelvalv i resursgruppen du skapade i föregående steg. Du måste ange en del information:

- I den här snabbstarten använder vi **Contoso-vault2**. Du måste ange ett unikt namn när du testar funktionen.
- Resursgruppnamnet **ContosoResourceGroup**.
- Platsen är **USA, östra**.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

Utdata från denna cmdlet visar egenskaper för nyckelvalvet du precis skapade. Anteckna de två egenskaperna som visas nedan:

- **Valvnamn**: I det här exemplet är namnet **Contoso-vault2**. Du kommer att använda det här namnet i andra Key Vault-kommandon.
- **Valvets URI**: I det här exemplet är det https://contoso-vault2.vault.azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="add-a-secret-to-key-vault"></a>Lägga till en hemlighet i Key Vault

När du ska lägga till en hemlighet i valvet behöver du bara utföra några ytterligare steg. Det här lösenordet kan användas av ett program. Lösenordet kallas **ExamplePassword** och vi lagrar värdet **hVFkk965BuUv** i det.

Skriv kommandona nedan för att skapa en hemlighet i Key Vault med namnet **ExamplePassword**, där värdet **hVFkk965BuUv** lagras:

```azurecli
az keyvault secret set --vault-name "Contoso-Vault2" --name "ExamplePassword" --value "hVFkk965BuUv"
```

Nu kan du referera till det här lösenordet som du lagt till i Azure Key Vault med hjälp av dess URI. Använd **för https://Contoso-Vault2.vault.azure.net/secrets/ExamplePassword ** att hämta den aktuella versionen. 

Så här visar du värdet som finns i hemligheten som oformaterad text:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "Contoso-Vault2"
```

Nu har du skapat ett nyckelvalv, lagrat en hemlighet och hämtat den.

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.
När du inte behöver resursgruppen längre kan du använda kommandot [az group delete](/cli/azure/group) till att ta bort resursgruppen och alla relaterade resurser. Så här tar du bort resurserna:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Key Vault och sparade en hemlighet. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Se referensen för [kommandon för Azure CLI-AZ](/cli/azure/keyvault?view=azure-cli-latest)
- Granska [Azure Key Vault bästa praxis](../general/best-practices.md)
