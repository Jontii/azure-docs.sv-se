---
title: 'Snabb start: Distribuera Azure API för FHIR med Azure CLI'
description: I den här snabb starten får du lära dig hur du distribuerar Azure API för FHIR i Azure med hjälp av Azure CLI.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.custom: devx-track-azurecli
ms.openlocfilehash: b09a72538dd7a6886811b9a23c915316627da093
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92339449"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>Snabb start: Distribuera Azure API för FHIR med Azure CLI

I den här snabb starten får du lära dig hur du distribuerar Azure API för FHIR i Azure med hjälp av Azure CLI.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-healthcareapis-extension"></a>Lägg till HealthcareAPIs-tillägg

```azurecli-interactive
az extension add --name healthcareapis
```

Hämta en lista med kommandon för HealthcareAPIs:

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>Skapa Azure-resurs grupp

Välj ett namn för resurs gruppen som ska innehålla Azure API för FHIR och skapa den:

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>Distribuera Azure API för FHIR

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>Hämta FHIR API Capability-instruktion

Hämta en funktions sats från FHIR-API: et med:

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort resurs gruppen med följande steg:

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starts guiden har du distribuerat Azure API för FHIR i din prenumeration. Om du vill ange ytterligare inställningar i Azure-API: et för FHIR går du vidare till ytterligare inställningar instruktions guide. Läs mer om hur du registrerar program om du är redo att börja använda Azure API för FHIR.

>[!div class="nextstepaction"]
>[Ytterligare inställningar i Azure API för FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Översikt över program register](fhir-app-registration.md)
