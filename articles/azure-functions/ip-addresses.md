---
title: IP-adresser i Azure Functions
description: Lär dig hur du hittar inkommande och utgående IP-adresser för Function-appar och vad som gör att de ändras.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 1d2cf34ee4712705eaa1c0da5ad63712f9e649fe
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2020
ms.locfileid: "91652473"
---
# <a name="ip-addresses-in-azure-functions"></a>IP-adresser i Azure Functions

I den här artikeln förklaras följande avsnitt om IP-adresser för Function-appar:

* Så här hittar du de IP-adresser som används av en Function-app.
* Vad gör en funktion i appens IP-adresser att ändras.
* Så här begränsar du IP-adresserna som har åtkomst till en Function-app.
* Så här hämtar du dedikerade IP-adresser för en Function-app.

IP-adresser är kopplade till Function-appar, inte med enskilda funktioner. Inkommande HTTP-begäranden kan inte använda den inkommande IP-adressen för att anropa enskilda funktioner. de måste använda standard domän namnet (functionappname.azurewebsites.net) eller ett eget domän namn.

## <a name="function-app-inbound-ip-address"></a>Funktion appens inkommande IP-adress

Varje Function-app har en enda inkommande IP-adress. Så här hittar du IP-adressen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till Function-appen.
3. Välj **Plattformsfunktioner**.
4. Välj **Egenskaper**så visas den inkommande IP-adressen under **virtuell IP-adress**.

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>Funktion app utgående IP-adresser

Varje Function-app har en uppsättning tillgängliga utgående IP-adresser. Utgående anslutningar från en funktion, till exempel till en backend-databas, använder en av de tillgängliga utgående IP-adresserna som ursprungs-IP-adress. Du kan inte veta i förväg vilken IP-adress en anslutning kommer att använda. Av den anledningen måste din backend-tjänst öppna dess brand vägg till alla funktioner i funktions appens utgående IP-adresser.

Så här hittar du de utgående IP-adresserna som är tillgängliga för en Function-app:

1. Logga in på [Azure Resource Explorer](https://resources.azure.com).
2. Välj **prenumerationer > {din prenumeration} > leverantörer > Microsoft. Web >-platser**.
3. I JSON-panelen letar du reda på platsen med en `id` egenskap som slutar i namnet på din Function-app.
4. Se `outboundIpAddresses` och `possibleOutboundIpAddresses` . 

Uppsättningen `outboundIpAddresses` är för närvarande tillgänglig för Function-appen. Uppsättningen `possibleOutboundIpAddresses` inkluderar IP-adresser som endast är tillgängliga om Function-appen [skalar till andra pris nivåer](#outbound-ip-address-changes).

Ett annat sätt att hitta tillgängliga utgående IP-adresser är genom att använda [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

> [!NOTE]
> När en Function-app som körs i [förbruknings planen](functions-scale.md#consumption-plan) eller [Premium-planen](functions-scale.md#premium-plan) skalas, kan ett nytt intervall med utgående IP-adresser tilldelas. När du kör på någon av dessa planer kan du behöva lägga till hela data centret i en lista över tillåtna.

## <a name="data-center-outbound-ip-addresses"></a>Utgående IP-adresser för data Center

Om du behöver lägga till de utgående IP-adresser som används av dina funktions program i en lista över tillåtna, är ett annat alternativ att lägga till funktionen Apps data Center (Azure-region) i en lista över tillåtna. Du kan [Hämta en JSON-fil som visar IP-adresser för alla Azure-datacenter](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Leta sedan reda på JSON-fragmentet som gäller den region som din Function-App körs i.

Detta är till exempel det västra Europa-JSON-fragmentet som kan se ut så här:

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 Om du vill ha information om när filen uppdateras och när IP-adresserna ändras, expanderar du avsnittet **information** på [sidan Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Ändringar av inkommande IP-adress

Den inkommande IP-adressen **kan** ändras när du:

- Ta bort en Function-app och återskapa den i en annan resurs grupp.
- Ta bort den sista Function-appen i en kombination av resurs grupp och region och återskapa den sedan.
- Ta bort en TLS-bindning, till exempel vid [förnyelse av certifikat](../app-service/configure-ssl-certificate.md#renew-certificate).

När din Function-App körs i en [förbruknings plan](functions-scale.md#consumption-plan) eller i en [Premium-plan](functions-scale.md#premium-plan)kan den inkommande IP-adressen också ändras även om du inte har vidtagit några åtgärder som de som [anges ovan](#inbound-ip-address-changes).

## <a name="outbound-ip-address-changes"></a>Utgående IP-adress ändringar

Uppsättningen tillgängliga utgående IP-adresser för en Function-app kan ändras när du:

* Vidta alla åtgärder som kan ändra den inkommande IP-adressen.
* Ändra pris nivån för App Service plan. Listan över alla möjliga utgående IP-adresser som din app kan använda, för alla pris nivåer, finns i `possibleOutboundIPAddresses` egenskapen. Se [hitta utgående IP-adresser](#find-outbound-ip-addresses).

När din Function-App körs i en [förbruknings plan](functions-scale.md#consumption-plan) eller i en [Premium-plan](functions-scale.md#premium-plan)kan den utgående IP-adressen också ändras även om du inte har vidtagit några åtgärder som de som [anges ovan](#inbound-ip-address-changes).

För att avsiktligt framtvinga en utgående IP-adress ändring:

1. Skala din App Service plan uppåt eller nedåt mellan pris nivåerna standard och Premium v2.
2. Vänta 10 minuter.
3. Skala tillbaka till den plats där du startade.

## <a name="ip-address-restrictions"></a>IP-adressbegränsningar

Du kan konfigurera en lista med IP-adresser som du vill tillåta eller neka åtkomst till en Function-app. Mer information finns i [Azure App Service statiska IP-begränsningar](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Dedikerade IP-adresser

Om du behöver statiska, dedikerade IP-adresser rekommenderar vi [App Service miljöer](../app-service/environment/intro.md) (den [isolerade nivån](https://azure.microsoft.com/pricing/details/app-service/) App Service planer). Mer information finns i [App Service-miljön IP-adresser](../app-service/environment/network-info.md#ase-ip-addresses) och [hur du styr inkommande trafik till en app service-miljön](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Ta reda på om din Function-App körs i en App Service-miljön:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till Function-appen.
3. Välj fliken **Översikt**.
4. App Service plan nivån visas under **App Service plan/pris nivå**. Pris nivån för App Service-miljön är **isolerad**.
 
Alternativt kan du använda [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

App Service-miljön `sku` är `Isolated` .

## <a name="next-steps"></a>Nästa steg

En vanlig orsak till IP-ändringar är att skal ändringar i Function-appar. [Lär dig mer om skalning av Function-appar](functions-scale.md).
