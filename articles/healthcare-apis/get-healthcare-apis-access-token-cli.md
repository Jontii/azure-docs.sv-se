---
title: Hämta åtkomsttoken med Azure CLI – Azure API för FHIR
description: Den här artikeln beskriver hur du hämtar en åtkomsttoken för Azure API för FHIR med hjälp av Azure CLI.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: matjazl
ms.openlocfilehash: 4d1c4cfcb15d97a2c54a04344f0bd098f65c1392
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660380"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Hämta åtkomsttoken för Azure API för FHIR med Azure CLI

I den här artikeln får du lära dig hur du skaffar en åtkomsttoken för Azure API för FHIR med hjälp av Azure CLI. När du [etablerar Azure API för FHIR](fhir-paas-portal-quickstart.md)konfigurerar du en uppsättning användare eller tjänst huvud namn som har åtkomst till tjänsten. Om ditt användar objekt-ID finns i listan över tillåtna objekt-ID: er kan du komma åt tjänsten med hjälp av en token som erhållits med Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="obtain-a-token"></a>Hämta en token

Azure API för FHIR använder en `resource`  eller `Audience` med URI som är lika med URI: n för FHIR-servern `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` . Du kan hämta en token och lagra den i en variabel (med namnet `$token` ) med följande kommando:

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>Använd med Azure API för FHIR

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skaffar en åtkomsttoken för Azure API för FHIR med hjälp av Azure CLI. Om du vill veta mer om hur du kommer åt FHIR-API: et med Postman fortsätter du till självstudien för Postman.

>[!div class="nextstepaction"]
>[Åtkomst till FHIR-API med Postman](access-fhir-postman-tutorial.md)
