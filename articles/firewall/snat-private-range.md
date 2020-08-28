---
title: Azure Firewall SNAT privata IP-adressintervall
description: Du kan konfigurera IP-adressintervall för SNAT.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 08/27/2020
ms.author: victorh
ms.openlocfilehash: 25c2096de4c3643a4e6a3ba7bdad1e69ae93a179
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019087"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure Firewall SNAT privata IP-adressintervall

Azure-brandväggen tillhandahåller automatisk SNAT för all utgående trafik till offentliga IP-adresser. Som standard har Azure-brandväggen inte SNAT med nätverks regler när mål-IP-adressen är i ett privat IP-adressintervall per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Program regler tillämpas alltid med en [transparent proxy](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) oavsett mål-IP-adress.

Den här logiken fungerar bra när du dirigerar trafik direkt till Internet. Men om du har aktiverat [Tvingad tunnel](forced-tunneling.md)trafik är Internet-baserad trafik SNATed till en av brand väggens privata IP-adresser i AzureFirewallSubnet, vilket döljer källan från den lokala brand väggen.

Om din organisation använder ett offentligt IP-adressintervall för privata nätverk SNATs trafiken till någon av brand väggens privata IP-adresser i AzureFirewallSubnet med Azure-brandväggen. Du kan dock konfigurera Azure-brandväggen så att den **inte** bevarar ditt offentliga IP-adressintervall.

Om du vill konfigurera Azure-brandväggen till att aldrig SNAT, oavsett mål-IP-adress, använder du **0.0.0.0/0** som ditt privata IP-adressintervall. Med den här konfigurationen kan Azure-brandväggen aldrig dirigera trafik direkt till Internet. Om du vill konfigurera brand väggen så att den alltid är SNAT oavsett mål adress använder du **255.255.255.255/32** som ditt privata IP-adressintervall.

> [!IMPORTANT]
> Om du vill ange egna privata IP-adressintervall och behålla standardvärdena för IANA RFC 1918 kontrollerar du att din anpassade lista fortfarande innehåller IANA RFC 1918-intervallet. 

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Konfigurera SNAT-privata IP-adressintervall – Azure PowerShell

Du kan använda Azure PowerShell för att ange privata IP-adressintervall för brand väggen.

### <a name="new-firewall"></a>Ny brand vägg

För en ny brand vägg är Azure PowerShell kommandot:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges utökas till aktuella standardinställningar i Azure-brandväggen medan de andra intervallen läggs till i den. För att behålla IANAPrivateRanges-standardvärdet i ditt privata intervall, måste det finnas i din `PrivateRange` specifikation, som du ser i följande exempel.

Mer information finns i [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Befintlig brand vägg

Om du vill konfigurera en befintlig brand vägg använder du följande Azure PowerShell-kommandon:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Mallar

Du kan lägga till följande i `additionalProperties` avsnittet:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Konfigurera SNAT-privata IP-adressintervall – Azure Portal

Du kan använda Azure Portal för att ange privata IP-adressintervall för brand väggen.

1. Välj din resurs grupp och välj sedan brand väggen.
2. På sidan **Översikt** , **privata IP-intervall**väljer du standardvärdet **IANA RFC 1918**.

   Sidan **Redigera privata IP-prefix** öppnas:

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Redigera privata IP-prefix":::

1. Som standard konfigureras **IANAPrivateRanges** .
2. Redigera de privata IP-adressintervall för din miljö och välj sedan **Spara**.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Tvingad tunnel trafik i Azure-brandväggen](forced-tunneling.md).