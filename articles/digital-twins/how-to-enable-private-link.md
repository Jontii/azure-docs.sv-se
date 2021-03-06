---
title: Aktivera privat åtkomst med privat länk (förhands granskning)
titleSuffix: Azure Digital Twins
description: Se Aktivera privat åtkomst för Azure Digitals dubbla lösningar med privat länk
author: baanders
ms.author: baanders
ms.date: 1/25/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a5328f52975e72298b93107792692b178dac8a97
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948821"
---
# <a name="enable-private-access-with-private-link-preview"></a>Aktivera privat åtkomst med privat länk (förhands granskning)

I den här artikeln beskrivs olika sätt att [aktivera privat länk med en privat slut punkt för en digital Azure-instans](concepts-security.md#private-network-access-with-azure-private-link-preview) (för närvarande i för hands version). Genom att konfigurera en privat slut punkt för Azure Digital-instansen kan du skydda Azure Digitals-instansen och eliminera offentlig exponering, samt undvika data exfiltrering från ditt [Azure-Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md).

Här följer de steg som beskrivs i den här artikeln: 
1. Aktivera en privat länk och konfigurera en privat slut punkt för en Azure Digital-instansen.
1. Inaktivera eller aktivera offentliga nätverks åtkomst flaggor för att begränsa API-åtkomst till privata länk anslutningar.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan konfigurera en privat slut punkt behöver du ett [**Azure-Virtual Network (VNet)**](../virtual-network/virtual-networks-overview.md) där slut punkten kan distribueras. Om du inte redan har ett VNet, kan du följa ett av Azure Virtual Network [snabb starter](../virtual-network/quick-create-portal.md) för att konfigurera detta.

## <a name="add-a-private-endpoint-for-an-azure-digital-twins-instance"></a>Lägg till en privat slut punkt för en digital Azure-instans 

Du kan aktivera en privat länk med en privat slut punkt för en digital Azure-instans som en del av instansens första konfiguration eller aktivera den senare på en instans som redan finns. 

Någon av dessa metoder för att skapa kommer att ge samma konfigurations alternativ och samma slut resultat för din instans. I det här avsnittet beskrivs hur du gör var och en av de [Azure Portal](https://portal.azure.com).

>[!TIP]
> Du kan också skapa en privat länk slut punkt via tjänsten för privata länkar i stället för via din Azure Digital-instansen. Detta ger också samma konfigurations alternativ och samma slut resultat.
>
> Mer information om hur du konfigurerar resurser för privata länkar finns i dokumentationen för privat länk för [Azure Portal](../private-link/create-private-endpoint-portal.md), [Azure CLI](../private-link/create-private-endpoint-cli.md), [arm](../private-link/create-private-endpoint-template.md)eller [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-during-instance-creation"></a>Lägg till en privat slut punkt när en instans skapas

I det här avsnittet ska du använda [Azure Portal](https://portal.azure.com) för att aktivera privat länk med en privat slut punkt på en digital Azure-instans som håller på att skapas. Det här avsnittet fokuserar på nätverks steget i skapande processen. en fullständig genom gång av hur du skapar en ny Azure Digital-instansen finns i [*instruktion: Konfigurera en instans och autentisering*](how-to-set-up-instance-portal.md).

Alternativen för privat länk finns på fliken **nätverk** i instans inställningar.

På den här fliken kan du aktivera privata slut punkter genom att välja alternativet **privat slut punkt** för **anslutnings metoden**.

Detta lägger till ett avsnitt som kallas **privata slut punkts anslutningar** där du kan konfigurera information om din privata slut punkt. Fortsätt genom att klicka på knappen **+ Lägg till** .

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Skärm bild av Azure Portal som visar fliken nätverk i dialog rutan skapa resurs för Azure Digitals. Det finns en markering runt fliken namn, alternativet privat slut punkt för anslutnings metoden och knappen + Lägg till för att skapa en ny privat slut punkts anslutning." lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

Då öppnas en sida där du kan ange information om en ny privat slut punkt.

:::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="Skärm bild av Azure Portal som visar sidan Skapa privat slut punkt. Det innehåller fälten som beskrivs nedan.":::

1. Fyll i valen för din **prenumeration** och **resurs grupp**. Ange **platsen** till samma plats som det virtuella nätverk som du kommer att använda. Välj ett **namn** för slut punkten och för **mål under resurser** väljer du *API*.

1. Välj sedan det **virtuella nätverk** och **undernät** som du vill använda för att distribuera slut punkten.

1. Slutligen väljer du om du vill **integrera med en privat DNS-zon**. Du kan använda standardinställningen **Ja** eller, om du vill ha hjälp med det här alternativet kan du följa länken i portalen för att [lära dig mer om privat DNS-integrering](../private-link/private-endpoint-overview.md#dns-configuration).

När du har fyllt i konfigurations alternativen trycker du på **OK** för att slutföra.

Då kommer du tillbaka till fliken **nätverk** i installations programmet för Azure Digitals-instansen, där den nya slut punkten ska vara synlig under * * privata slut punkts anslutningar.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Skärm bild av Azure Portal som visar fliken nätverk i dialog rutan skapa resurs för Azure Digitals. Det finns en markering runt den nya privata slut punkts anslutningen och navigerings knapparna (granska + skapa, föregående, nästa: Avancerat)." lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

Du kan sedan använda de nedre navigerings knapparna för att fortsätta med resten av instans installationen.

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Lägg till en privat slut punkt i en befintlig instans

I det här avsnittet ska du använda [Azure Portal](https://portal.azure.com) för att aktivera privat länk med en privat slut punkt för en digital Azure-instans som redan finns.

1. Börja med att navigera till [Azure Portal](https://portal.azure.com) i en webbläsare. Ta upp Azure Digitals-instansen genom att söka efter dess namn i portalens Sök fält.

1. Välj **nätverk (för hands version)** på den vänstra menyn.

1. Växla till fliken **anslutningar för privat slut punkt** .

1. Välj **+ privat slut punkt** för att öppna installations programmet för att **skapa en privat slut punkt** .

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Skärm bild av Azure Portal som visar sidan nätverk (för hands version) för en digital Azure-instans. Fliken anslutningar för privata slut punkter markeras och knappen + privat slut punkt är också markerad." lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1. På fliken  **grundläggande**   anger eller väljer du **prenumeration** och **resurs grupp** för projektet och ett **namn** och en **region** för slut punkten. Regionen måste vara samma som regionen för det virtuella nätverk som du använder.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="Skärm bild av Azure Portal som visar fliken första (grunderna) i dialog rutan skapa en privat slut punkt. Det innehåller fälten som beskrivs ovan.":::

    När du är klar väljer du knappen **Nästa: resurs >** för att gå till nästa flik.

1. På fliken **resurs** anger eller väljer du den här informationen: 
    * **Anslutnings metod**: Välj **Anslut till en Azure-resurs i min katalog** för att söka efter din Azure Digital-instansen.
    * **Prenumeration**: Ange din prenumeration.
    * **Resurs typ**: Välj **Microsoft. DigitalTwins/digitalTwinsInstances**
    * **Resurs**: Välj namnet på din Azure Digital-instansen.
    * **Mål under resurs**: Välj **API**.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="Skärm bild av Azure Portal som visar fliken andra (resurs) i dialog rutan skapa en privat slut punkt. Det innehåller fälten som beskrivs ovan.":::

    När du är klar väljer du knappen **Nästa: konfiguration >** för att gå till nästa flik.    

1. På fliken **konfiguration** anger eller väljer du den här informationen:
    * **Virtuellt nätverk**: Välj det virtuella nätverket.
    * **Undernät**: Välj ett undernät från det virtuella nätverket.
    * **Integrera med privat DNS-zon**: Välj om du vill **integrera med en privat DNS-zon**. Du kan använda standardinställningen **Ja** eller, om du vill ha hjälp med det här alternativet kan du följa länken i portalen för att [lära dig mer om privat DNS-integrering](../private-link/private-endpoint-overview.md#dns-configuration).
    Om du väljer **Ja** kan du lämna standard konfigurations informationen.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="Skärm bild av Azure Portal som visar fliken tredje (konfiguration) i dialog rutan skapa en privat slut punkt. Det innehåller fälten som beskrivs ovan.":::

    När du är klar kan du välja knappen **Granska + skapa** för att slutföra installationen. 

1. På fliken **Granska + skapa** granskar du dina val och väljer knappen  **skapa** . 

När slut punkten har distribuerats ska den visas i de privata slut punkts anslutningarna för din Azure Digital-instansen.

>[!TIP]
> Slut punkten kan också visas från det privata länk centret i Azure Portal.

## <a name="disable--enable-public-network-access-flags"></a>Inaktivera/aktivera offentliga nätverks åtkomst flaggor

Du kan konfigurera Azure Digitals-instansen för att neka alla offentliga anslutningar och bara tillåta anslutningar via privata slut punkter för att förbättra nätverks säkerheten. Den här åtgärden görs med en **offentlig nätverks åtkomst flagga**. 

Med den här principen kan du begränsa API-åtkomst till privata länk anslutningar. När den offentliga nätverks åtkomst flaggan är *inaktive rad*, kommer alla REST API anrop till data planet för Azure Digital-instansen från det offentliga molnet att returneras `403, Unauthorized` . När principen är inställd på *inaktive rad* och en begäran görs via en privat slut punkt, kommer API-anropet att lyckas.

Du kan uppdatera värdet för nätverks flaggan med hjälp av [Azure Portal](https://portal.azure.com), [Azure CLI](/cli/azure/)eller [kommando verktyget ARMClient](https://github.com/projectkudu/ARMClient).

### <a name="option-1-using-the-azure-portal"></a>Alternativ 1: använda Azure Portal

Om du vill inaktivera eller aktivera offentlig nätverks åtkomst i [Azure Portal](https://portal.azure.com)öppnar du portalen och navigerar till din Azure Digital-instansen.

1. Välj **nätverk (för hands version)** på den vänstra menyn.

1. På fliken **offentlig åtkomst** anger du **Tillåt offentligt nätverks åtkomst till** antingen **inaktiverade** eller **alla nätverk**.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Skärm bild av Azure Portal som visar sidan nätverk (för hands version) för en digital Azure-instans. Fliken offentlig åtkomst är markerad och alternativet att välja om du vill tillåta offentlig nätverks åtkomst också är markerat." lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    Välj **Spara**.

### <a name="option-2-using-the-azure-cli"></a>Alternativ 2: använda Azure CLI

Med Azure CLI görs den här åtgärden med hjälp av `az resource update` kommandot som visas nedan. Egenskapen publicNetworkAccess på en digital Azure Digital-instans kan anges till antingen `disabled` eller `enabled` .

Om du vill inaktivera flaggan använder du följande kommando och ersätter plats hållarna med dina instans värden.
 
```azurecli
az resource update --ids /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance> --set properties.publicNetworkAccess=disabled  
```

Här är en skärm bild av hur utdata ser ut.

:::image type="content" source="media/how-to-enable-private-link/network-flag-cli.png" alt-text="Skärm bild av ett terminalfönster som kör ovanstående Azure CLI-kommando. Utdata innehåller information om instansen, inklusive en egenskap med namnet publicNetworkAccess med värdet disabled." lightbox="media/how-to-enable-private-link/network-flag-cli.png":::

Använd följande liknande kommando för att aktivera flaggan.
 
```azurecli
az resource update --ids /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance> --set properties.publicNetworkAccess=enabled  
```

### <a name="option-3-usingarmclient"></a>Alternativ 3: använda ARMClient  

Med [kommando verktyget ARMClient](https://github.com/projectkudu/ARMClient)aktive ras eller inaktive ras offentliga nätverks åtkomst med hjälp av kommandona nedan. 

Så här inaktiverar du offentlig nätverks åtkomst:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

Så här aktiverar du offentlig nätverks åtkomst:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>Nästa steg

Läs mer om privat länk för Azure: 
* [*Vad är Azure Private Link service?*](../private-link/private-link-service-overview.md)