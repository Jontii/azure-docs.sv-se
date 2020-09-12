---
title: Skapa en offentlig IP-Azure Portal
description: Lär dig hur du skapar en offentlig IP-adress i Azure Portal
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 49a89ee90d28c9c7a3f59424b773ee0f221381e4
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89303535"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Snabb start: skapa en offentlig IP-adress med hjälp av Azure Portal

Den här artikeln visar hur du skapar en offentlig IP-adressresurs med hjälp av Azure Portal. Mer information om vilka resurser som kan kopplas till, skillnaden mellan Basic-och standard-SKU och annan relaterad information finns i [offentliga IP-adresser](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses).  I det här exemplet kommer vi bara att fokusera på IPv4-adresser. Mer information om IPv6-adresser finns i [IPv6 för Azure VNet](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

# <a name="standard-sku---using-zones"></a>[**Standard-SKU – använda zoner**](#tab/option-create-public-ip-standard-zones)

Använd följande steg för att skapa en standard zon med redundant offentlig IP-adress med namnet **myStandardZRPublicIP**.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Skapa en resurs**. 
3. I rutan Sök skriver du *offentlig IP-adress*.
4. I Sök resultaten väljer du **offentlig IP-adress**. Sedan väljer du **skapa**på sidan **offentlig IP-adress** .
5. På sidan **skapa offentlig IP-adress** anger eller väljer du följande information: 

    | Inställningen                 | Värde                       |
    | ---                     | ---                         |
    | IP-version              | Välj IPv4                 |    
    | SKU                     | Välj **standard**         |
    | Name                    | Ange *myStandardZRPublicIP*          |
    | Tilldelning av IP-adress   | OBS! detta kommer att låsas som "statisk"                                        |
    | Tids gräns för inaktivitet (minuter)  | Lämna värdet vid 4        |
    | DNS-namnetikett          | Lämna värdet tomt    |
    | Prenumeration            | Välj din prenumeration.   |
    | Resursgrupp          | Välj **Skapa ny** , ange myResourceGroup och välj sedan **OK** |
    | Plats                | Välj **USA, östra 2**      |
    | Tillgänglighetszon       | Välj **zon – redundant** eller plocka en speciell zon (se anmärkning nedan) |

Observera att dessa endast är giltiga val i regioner med [Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).  (Du kan också välja en speciell zon i dessa regioner, men den kommer inte att vara elastisk till zonindelade-problem.)

# <a name="standard-sku---no-zones"></a>[**Standard-SKU – inga zoner**](#tab/option-create-public-ip-standard)

Använd följande steg för att skapa en offentlig standard-IP-adress som en icke-zonindelade resurs med namnet **myStandardPublicIP**.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Skapa en resurs**. 
3. I rutan Sök skriver du *offentlig IP-adress*.
4. I Sök resultaten väljer du **offentlig IP-adress**. Sedan väljer du **skapa**på sidan **offentlig IP-adress** .
5. På sidan **skapa offentlig IP-adress** anger eller väljer du följande information: 

    | Inställningen                 | Värde                       |
    | ---                     | ---                         |
    | IP-version              | Välj IPv4                 |    
    | SKU                     | Välj **standard**         |
    | Name                    | Ange *myStandardPublicIP*          |
    | Tilldelning av IP-adress   | OBS! detta kommer att låsas som "statisk"                                        |
    | Tids gräns för inaktivitet (minuter)  | Lämna värdet vid 4        |
    | DNS-namnetikett          | Lämna värdet tomt    |
    | Prenumeration            | Välj din prenumeration.   |
    | Resursgrupp          | Välj **Skapa ny** , ange myResourceGroup och välj sedan **OK** |
    | Plats                | Välj **USA, östra 2**      |
    | Tillgänglighetszon       | Välj **ingen zon** (och se OBS! nedan) |

Det här valet är giltigt i alla regioner och är standard valet för offentliga standard-IP-adresser i regioner utan att [Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="basic-sku"></a>[**Grundläggande SKU**](#tab/option-create-public-ip-basic)

Använd följande steg för att skapa en grundläggande statisk offentlig IP-adress med namnet **myBasicPublicIP**.  Grundläggande offentliga IP-adresser har inte konceptet tillgänglighets zoner.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Skapa en resurs**. 
3. I rutan Sök skriver du *offentlig IP-adress*.
4. I Sök resultaten väljer du **offentlig IP-adress**. Sedan väljer du **skapa**på sidan **offentlig IP-adress** .
5. På sidan **skapa offentlig IP-adress** anger eller väljer du följande information: 

    | Inställningen                 | Värde                       |
    | ---                     | ---                         |
    | IP-version              | Välj IPv4                 |    
    | SKU                     | Välj **standard**         |
    | Name                    | Ange *myBasicPublicIP*          |
    | Tilldelning av IP-adress   | Välj **statisk** (se anmärkning nedan)                                     |
    | Tids gräns för inaktivitet (minuter)  | Lämna värdet vid 4        |
    | DNS-namnetikett          | Lämna värdet tomt    |
    | Prenumeration            | Välj din prenumeration.   |
    | Resursgrupp          | Välj **Skapa ny** , ange myResourceGroup och välj sedan **OK** |
    | Plats                | Välj **USA, östra 2**      |

Om det är acceptabelt att IP-adressen kan ändras över tid kan du välja **dynamisk** IP-tilldelning.

---

## <a name="additional-information"></a>Ytterligare information 

Mer information om de enskilda fälten ovan finns i [hantera offentliga IP-adresser](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Nästa steg
- Associera en [offentlig IP-adress till en virtuell dator](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)
- Läs mer om [offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) i Azure.
- Läs mer om [Inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address).