---
title: 'Skapa en Route-baserad VPN-Gateway: Portal'
titleSuffix: Azure VPN Gateway
description: Använd Azure Portal för att snabbt skapa en Route-baserad Azure VPN-gateway, för en VPN-anslutning till det lokala nätverket eller för att ansluta virtuella nätverk.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 104d911164d0194efba41f1405c17fc240e8906d
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89393776"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Skapa en Route-baserad VPN-gateway med hjälp av Azure Portal

Den här artikeln hjälper dig att snabbt skapa en Route-baserad Azure VPN-gateway med hjälp av Azure Portal.  En VPN-gateway används när du skapar en VPN-anslutning till ditt lokala nätverk. Du kan också använda en VPN-gateway för att ansluta virtuella nätverk. 

Stegen i den här artikeln skapar ett VNet, ett undernät, ett Gateway-undernät och en Route-baserad VPN-gateway (virtuell nätverksgateway). När gatewayen har skapats kan du skapa anslutningar. De här stegen kräver en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Skapa ett virtuellt nätverk

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="configure-and-create-the-gateway"></a><a name="gwvalues"></a>Konfigurera och skapa gatewayen

I det här steget ska du skapa den virtuella nätverksgatewayen för ditt virtuella nätverk. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

>[!NOTE]
>Basic Gateway-SKU: n stöder inte IKEv2-eller RADIUS-autentisering. Om du planerar att låta Mac-klienter ansluta till ditt virtuella nätverk ska du inte använda den grundläggande SKU: n.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Visa VPN-gatewayen

1. När gatewayen har skapats navigerar du till VNet1 i portalen. VPN-gatewayen visas på sidan Översikt som en ansluten enhet.

   ![Anslutna enheter](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Anslutna enheter")

2. I enhets listan klickar du på **VNet1GW** för att visa mer information.

   ![Visa VPN-gateway](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Visa VPN-gateway")

## <a name="next-steps"></a>Nästa steg

När gatewayen har skapats kan du skapa en anslutning mellan ditt virtuella nätverk och ett annat VNet. Eller skapa en anslutning mellan ditt virtuella nätverk och en lokal plats.

> [!div class="nextstepaction"]
> [Skapa en plats-till-plats-anslutning](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Skapa en punkt-till-plats-anslutning](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Skapa en anslutning till ett annat VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
