---
title: Konfigurera Azure Storage brand väggar och virtuella nätverk | Microsoft Docs
description: Konfigurera skiktad nätverks säkerhet för ditt lagrings konto med hjälp Azure Storage brand väggar och Azure Virtual Network.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/05/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: b936460d98ce772e49f281a06f95da09fa34ccfb
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758820"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurera brandväggar och virtuella nätverk i Azure Storage

Azure Storage tillhandahåller en säkerhetsmodell i flera lager. Med den här modellen kan du skydda och kontrollera den nivå av åtkomst till dina lagringskonton som behövs för dina appar och företagsmiljöer, beroende på vilken typ och delmängd av nätverk som används. När nätverks regler har kon figurer ATS kan endast program som begär data i den angivna uppsättningen nätverk komma åt ett lagrings konto. Du kan begränsa åtkomsten till ditt lagrings konto till begär Anden som kommer från angivna IP-adresser, IP-intervall eller från en lista över undernät i ett Azure-Virtual Network (VNet).

Lagrings konton har en offentlig slut punkt som kan nås via Internet. Du kan också skapa [privata slut punkter för ditt lagrings konto](storage-private-endpoints.md), som tilldelar en privat IP-adress från ditt VNet till lagrings kontot och skyddar all trafik mellan ditt VNet och lagrings kontot över en privat länk. Azure Storage-brandväggen ger åtkomst kontroll för den offentliga slut punkten för ditt lagrings konto. Du kan också använda brand väggen för att blockera all åtkomst via den offentliga slut punkten när du använder privata slut punkter. Konfigurationen av lagrings brand väggen aktiverar även de betrodda Azure Platform-tjänsterna för att få åtkomst till lagrings kontot på ett säkert sätt.

Ett program som har åtkomst till ett lagrings konto när nätverks regler tillämpas kräver fortfarande korrekt auktorisering för begäran. Auktorisering stöds med Azure Active Directory (Azure AD)-autentiseringsuppgifter för blobbar och köer, med en giltig konto åtkomst nyckel eller med en SAS-token.

> [!IMPORTANT]
> Att aktivera brand Väggs regler för ditt lagrings konto blockerar inkommande begär Anden om data som standard, om inte begär Anden kommer från en tjänst som körs i ett Azure-Virtual Network (VNet) eller från tillåtna offentliga IP-adresser. Begär Anden som blockeras inkluderar de från andra Azure-tjänster, från Azure Portal, från loggnings-och mått tjänster och så vidare.
>
> Du kan bevilja åtkomst till Azure-tjänster som fungerar inifrån ett VNet genom att tillåta trafik från det undernät som är värd för tjänst instansen. Du kan också aktivera ett begränsat antal scenarier med hjälp av [undantags](#exceptions) mekanismen som beskrivs nedan. Om du vill komma åt data från lagrings kontot via Azure Portal måste du vara på en dator inom den betrodda gränser (antingen IP eller VNet) som du konfigurerar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenarier

Om du vill skydda ditt lagrings konto bör du först konfigurera en regel för att neka åtkomst till trafik från alla nätverk (inklusive Internet trafik) på den offentliga slut punkten som standard. Sedan bör du konfigurera regler som beviljar åtkomst till trafik från vissa virtuella nätverk. Du kan också konfigurera regler för att bevilja åtkomst till trafik från Välj offentliga IP-adressintervall för Internet, aktivera anslutningar från vissa Internet-eller lokala klienter. Med den här konfigurationen kan du bygga en säker nätverks gränser för dina program.

Du kan kombinera brand Väggs regler som tillåter åtkomst från vissa virtuella nätverk och från offentliga IP-adressintervall på samma lagrings konto. Lagrings brand Väggs regler kan tillämpas på befintliga lagrings konton eller när du skapar nya lagrings konton.

Lagrings brand Väggs regler gäller för den offentliga slut punkten för ett lagrings konto. Du behöver inga brand Väggs åtkomst regler för att tillåta trafik för privata slut punkter för ett lagrings konto. Processen med att godkänna skapandet av en privat slut punkt ger implicit åtkomst till trafik från det undernät som är värd för den privata slut punkten.

Nätverks regler tillämpas på alla nätverks protokoll till Azure Storage, inklusive REST och SMB. Om du vill komma åt data med hjälp av verktyg som Azure Portal, Storage Explorer och AZCopy måste explicita nätverks regler konfigureras.

När nätverks reglerna tillämpas tillämpas de för alla begär Anden. SAS-token som beviljar åtkomst till en speciell IP-adress som gör att du kan begränsa åtkomsten till token-innehavaren, men inte bevilja ny åtkomst utöver konfigurerade nätverks regler.

Disk trafik på den virtuella datorn (inklusive åtgärder för montering och demontering och disk-i/o) påverkas inte av nätverks regler. REST-åtkomst till Page blobbar skyddas av nätverks regler.

Klassiska lagrings konton stöder inte brand väggar och virtuella nätverk.

Du kan använda ohanterade diskar i lagrings konton med nätverks regler som används för att säkerhetskopiera och återställa virtuella datorer genom att skapa ett undantag. Den här processen dokumenteras i avsnittet [undantag](#exceptions) i den här artikeln. Brand Väggs undantag gäller inte för hanterade diskar eftersom de redan hanteras av Azure.

## <a name="change-the-default-network-access-rule"></a>Ändra standardåtkomstregeln för nätverk

Som standard godkänner lagringskonton anslutningar från klienter i alla nätverk. Om du vill begränsa åtkomsten till valda nätverk måste du först ändra standardåtgärden.

> [!WARNING]
> Om du gör ändringar av nätverksreglerna kan det påverka programmens möjlighet att ansluta till Azure Storage. Om du anger standard nätverks regeln till **neka** blockeras alla åtkomst till data, om inte vissa nätverks regler som **beviljar** åtkomst också tillämpas. Se till att bevilja åtkomst till alla tillåtna nätverk som använder nätverksregler innan du ändrar standardregeln för att neka åtkomst.

### <a name="managing-default-network-access-rules"></a>Hantera standardregler för nätverksåtkomst

Du kan hantera standard regler för nätverks åtkomst för lagrings konton via Azure Portal, PowerShell eller CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Gå till det lagringskonto som du vill skydda.

1. Klicka på menyn Inställningar, som kallas **brand väggar och virtuella nätverk**.

1. Om du vill neka åtkomst som standard väljer du att tillåta åtkomst från **valda nätverk**. Om du vill tillåta trafik från alla nätverk väljer du att tillåta åtkomst från **Alla nätverk**.

1. Klicka på **Spara** för att tillämpa dina ändringar.

#### <a name="powershell"></a>PowerShell

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) och [Logga](/powershell/azure/authenticate-azureps)in.

1. Visa status för standard regeln för lagrings kontot.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Ange standard regeln för att neka nätverks åtkomst som standard.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Ange standard regeln för att tillåta nätverks åtkomst som standard.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [Logga](/cli/azure/authenticate-azure-cli)in.

1. Visa status för standard regeln för lagrings kontot.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Ange standard regeln för att neka nätverks åtkomst som standard.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Ange standard regeln för att tillåta nätverks åtkomst som standard.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Bevilja åtkomst från ett virtuellt nätverk

Du kan konfigurera lagrings konton så att endast åtkomst från vissa undernät tillåts. De tillåtna under näten kan tillhöra ett VNet i samma prenumeration eller i en annan prenumeration, inklusive prenumerationer som tillhör en annan Azure Active Directory klient.

Aktivera en [tjänst slut punkt](/azure/virtual-network/virtual-network-service-endpoints-overview) för Azure Storage i VNet. Tjänst slut punkten dirigerar trafik från VNet via en optimal sökväg till Azure Storage tjänsten. Identiteterna för under nätet och det virtuella nätverket överförs också med varje begäran. Administratörer kan sedan konfigurera nätverks regler för det lagrings konto som tillåter att förfrågningar tas emot från vissa undernät i ett VNet. Klienter som beviljats åtkomst via dessa nätverks regler måste fortsätta att uppfylla kraven för auktorisering av lagrings kontot för att komma åt data.

Varje lagrings konto har stöd för upp till 200 virtuella nätverks regler, som kan kombineras med [IP-nätverks-regler](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Tillgängliga virtuella nätverks regioner

I allmänhet fungerar tjänst slut punkter mellan virtuella nätverk och tjänst instanser i samma Azure-region. När du använder tjänst slut punkter med Azure Storage växer det här omfånget för att inkludera den [kopplade regionen](/azure/best-practices-availability-paired-regions). Tjänst slut punkter tillåter kontinuitet under en regional redundans och till gång till skrivskyddade geo-redundanta lagrings instanser (RA-GRS). Nätverks regler som beviljar åtkomst från ett virtuellt nätverk till ett lagrings konto ger också åtkomst till valfri RA-GRS-instans.

När du planerar för haveri beredskap under ett regionalt avbrott bör du skapa virtuella nätverk i den kopplade regionen i förväg. Aktivera tjänstens slut punkter för Azure Storage med nätverks regler som beviljar åtkomst från dessa alternativa virtuella nätverk. Tillämpa sedan dessa regler på dina geo-redundanta lagrings konton.

> [!NOTE]
> Tjänstens slut punkter gäller inte för trafik utanför regionen för det virtuella nätverket och det angivna region paret. Du kan bara använda nätverks regler som beviljar åtkomst från virtuella nätverk till lagrings konton i den primära regionen för ett lagrings konto eller i den angivna kopplade regionen.

### <a name="required-permissions"></a>Behörigheter som krävs

Om du vill tillämpa en virtuell nätverks regel på ett lagrings konto måste användaren ha rätt behörighet för de undernät som läggs till. Den behörighet som krävs är *Anslut till ett undernät* och ingår i den inbyggda rollen *lagrings konto deltagare* . Den kan också läggas till i anpassade roll definitioner.

Lagrings kontot och de virtuella nätverk som beviljats åtkomst kan finnas i olika prenumerationer, inklusive prenumerationer som ingår i en annan Azure AD-klient.

> [!NOTE]
> Konfiguration av regler som beviljar åtkomst till undernät i virtuella nätverk som ingår i en annan Azure Active Directory klient stöds för närvarande bara via PowerShell-, CLI-och REST-API: er. Sådana regler kan inte konfigureras via Azure Portal, men de kan visas i portalen.

### <a name="managing-virtual-network-rules"></a>Hantera virtuella nätverks regler

Du kan hantera virtuella nätverks regler för lagrings konton via Azure Portal, PowerShell eller CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Gå till det lagringskonto som du vill skydda.

1. Klicka på menyn Inställningar, som kallas **brand väggar och virtuella nätverk**.

1. Kontrol lera att du har valt att tillåta åtkomst från **valda nätverk**.

1. Om du vill bevilja åtkomst till ett virtuellt nätverk med en ny nätverks regel klickar du på **Lägg till befintligt virtuellt nätverk**under **virtuella nätverk**, väljer alternativ för **virtuella nätverk** och **undernät** och klickar sedan på **Lägg till**. Om du vill skapa ett nytt virtuellt nätverk och bevilja det åtkomst klickar du på **Lägg till nytt virtuellt nätverk**. Ange den information som krävs för att skapa det nya virtuella nätverket och klicka sedan på **skapa**.

    > [!NOTE]
    > Om en tjänst slut punkt för Azure Storage inte tidigare har kon figurer ATS för det valda virtuella nätverket och under nätet, kan du konfigurera den som en del av den här åtgärden.
    >
    > För närvarande visas endast virtuella nätverk som hör till samma Azure Active Directory klient organisation för val av skapande av regel. Om du vill bevilja åtkomst till ett undernät i ett virtuellt nätverk som tillhör en annan klient organisation använder du PowerShell-, CLI-eller REST-API: er.

1. Om du vill ta bort ett virtuellt nätverk eller en under näts regel klickar du på **...** för att öppna snabb menyn för det virtuella nätverket eller under nätet och klicka på **ta bort**.

1. Klicka på **Spara** för att tillämpa dina ändringar.

#### <a name="powershell"></a>PowerShell

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) och [Logga](/powershell/azure/authenticate-azureps)in.

1. Lista över virtuella nätverks regler.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Aktivera tjänstens slut punkt för Azure Storage på ett befintligt virtuellt nätverk och undernät.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Lägg till en nätverks regel för ett virtuellt nätverk och undernät.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Om du vill lägga till en nätverks regel för ett undernät i ett virtuellt nätverk som tillhör en annan Azure AD-klient använder du en fullständigt kvalificerad **VirtualNetworkResourceId** -parameter i formatet "/Subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-Name/subnets/Subnet-Name".

1. Ta bort en nätverks regel för ett virtuellt nätverk och undernät.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) för **neka**eller att nätverks regler inte har någon påverkan.

#### <a name="cliv2"></a>CLIv2

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [Logga](/cli/azure/authenticate-azure-cli)in.

1. Lista över virtuella nätverks regler.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Aktivera tjänstens slut punkt för Azure Storage på ett befintligt virtuellt nätverk och undernät.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Lägg till en nätverks regel för ett virtuellt nätverk och undernät.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Om du vill lägga till en regel för ett undernät i ett virtuellt nätverk som tillhör en annan Azure AD-klient använder du ett fullständigt kvalificerat undernät-ID i formatet "/Subscriptions/ \<subscription-ID\> /ResourceGroups/ \<resourceGroup-Name\> /providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /subnets/ \<subnet-name\> ".
    >
    > Du kan använda parametern **Subscription** för att hämta Undernäts-ID: t för ett VNet som tillhör en annan Azure AD-klient.

1. Ta bort en nätverks regel för ett virtuellt nätverk och undernät.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) för **neka**eller att nätverks regler inte har någon påverkan.

## <a name="grant-access-from-an-internet-ip-range"></a>Bevilja åtkomst från ett IP-intervall på internet

Du kan konfigurera lagrings konton så att de tillåter åtkomst från vissa offentliga IP-adressintervall för Internet. Den här konfigurationen beviljar åtkomst till vissa Internetbaserade tjänster och lokala nätverk och blockerar allmän Internet trafik.

Tillhandahålla tillåtna Internet adress intervall med [CIDR-notering](https://tools.ietf.org/html/rfc4632) i formatet *16.17.18.0/24* eller enskilda IP-adresser som *16.17.18.19*.

   > [!NOTE]
   > Små adress intervall som använder sig av prefixlängden "/31" eller "/32" stöds inte. Dessa intervall ska konfigureras med hjälp av enskilda IP-adressintervall.

IP-nätverks regler tillåts endast för **offentliga Internet** -IP-adresser. IP-adressintervall som är reserverade för privata nätverk (enligt definitionen i [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) tillåts inte i IP-regler. Privata nätverk innehåller adresser som börjar med _10. *_, _172,16. *_  -  _172,31. *_ och _192,168. *_.

   > [!NOTE]
   > IP-nätverksanslutningar har ingen påverkan på begär Anden som kommer från samma Azure-region som lagrings kontot. Använd [regler för virtuella nätverk](#grant-access-from-a-virtual-network) för att tillåta begäran om samma region.

  > [!NOTE]
  > Tjänster som distribueras i samma region som lagrings kontot använder privata Azure IP-adresser för kommunikation. Därför kan du inte begränsa åtkomsten till vissa Azure-tjänster baserat på deras offentliga utgående IP-adressintervall.

Endast IPV4-adresser stöds för konfiguration av lagrings brand Väggs regler.

Varje lagrings konto har stöd för upp till 200 IP-nätverksanslutningar.

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurera åtkomst från lokala nätverk

Om du vill bevilja åtkomst från dina lokala nätverk till ditt lagrings konto med en IP-nätverks regel måste du identifiera de Internet adresser som IP-adresser används i nätverket. Kontakta nätverks administratören om du behöver hjälp.

Om du använder [ExpressRoute](/azure/expressroute/expressroute-introduction) lokalt för offentlig peering eller Microsoft-peering, måste du identifiera de NAT IP-adresser som används. För offentlig peering, använder varje ExpressRoute-krets som standard två NAT IP-adresser, som används för Azure-tjänsttrafik när trafiken kommer till Microsoft Azure-stamnätverket. För Microsoft-peering används de NAT-IP-adresser som används antingen för kunden eller tillhandahålls av tjänst leverantören. Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta dessa offentliga IP-adresser i resursens IP-brandväggsinställning. För att kunna hitta ExpressRoute-kretsens IP-adresser för offentlig peering [öppnar du en supportbegäran hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen. Lär dig mer om [NAT för ExpressRoute offentliga peering och Microsoft-peering.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Hantera IP-nätverks regler

Du kan hantera IP-nätverks regler för lagrings konton via Azure Portal, PowerShell eller CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Gå till det lagringskonto som du vill skydda.

1. Klicka på menyn Inställningar, som kallas **brand väggar och virtuella nätverk**.

1. Kontrol lera att du har valt att tillåta åtkomst från **valda nätverk**.

1. Om du vill bevilja åtkomst till ett Internet-IP-intervall anger du IP-adressen eller adress intervallet (i CIDR-format) under **brand Väggs**  >  **adress intervall**.

1. Om du vill ta bort en IP-nätverks regel klickar du på pappers korgs ikonen bredvid adress intervallet.

1. Klicka på **Spara** för att tillämpa dina ändringar.

#### <a name="powershell"></a>PowerShell

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) och [Logga](/powershell/azure/authenticate-azureps)in.

1. Lista IP-nätverksnummer.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Lägg till en nätverks regel för en enskild IP-adress.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Lägg till en nätverks regel för ett IP-adressintervall.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Ta bort en nätverks regel för en enskild IP-adress.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Ta bort en nätverks regel för ett IP-adressintervall.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) för **neka**eller att nätverks regler inte har någon påverkan.

#### <a name="cliv2"></a>CLIv2

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [Logga](/cli/azure/authenticate-azure-cli)in.

1. Lista IP-nätverksnummer.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Lägg till en nätverks regel för en enskild IP-adress.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Lägg till en nätverks regel för ett IP-adressintervall.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Ta bort en nätverks regel för en enskild IP-adress.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Ta bort en nätverks regel för ett IP-adressintervall.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) för **neka**eller att nätverks regler inte har någon påverkan.

## <a name="exceptions"></a>Undantag

Nätverks regler hjälper till att skapa en säker miljö för anslutningar mellan dina program och dina data för de flesta scenarier. Vissa program är dock beroende av Azure-tjänster som inte kan isoleras unikt genom virtuella nätverks-eller IP-adress regler. Men sådana tjänster måste beviljas till lagring för att möjliggöra fullständig program funktion. I sådana fall kan du använda inställningen ***Tillåt betrodda Microsoft-tjänster...*** för att aktivera sådana tjänster för att få åtkomst till dina data, loggar eller analyser.

### <a name="trusted-microsoft-services"></a>Betrodda Microsoft-tjänster

Vissa Microsoft-tjänster körs från nätverk som inte kan ingå i dina nätverks regler. Du kan ge en delmängd av dessa betrodda Microsoft-tjänster åtkomst till lagrings kontot, samtidigt som nätverks reglerna för andra appar upprätthålls. Dessa betrodda tjänster kommer sedan att använda stark autentisering för att ansluta till ditt lagrings konto på ett säkert sätt. Vi har aktiverat två lägen för betrodd åtkomst för Microsoft-tjänster.

- Resurser för vissa tjänster, **när de registreras i din prenumeration**, kan komma åt ditt lagrings konto **i samma prenumeration** för Select-åtgärder, till exempel skriva loggar eller säkerhets kopiering.
- Resurser i vissa tjänster kan beviljas uttrycklig åtkomst till ditt lagrings konto genom att **tilldela en Azure-roll** till sin systemtilldelade hanterade identitet.


När du aktiverar inställningen **Tillåt betrodda Microsoft-tjänster...** , beviljas resurser för följande tjänster som är registrerade i samma prenumeration som ditt lagrings konto åtkomst för en begränsad uppsättning åtgärder enligt beskrivningen:

| Tjänst                  | Namn på resurs leverantör     | Tillåtna åtgärder                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft. RecoveryServices | Kör säkerhets kopiering och återställning av ohanterade diskar i virtuella IAAS-datorer. (krävs inte för Managed Disks). [Läs mer](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft. data-          | Gör det möjligt att importera data till Azure med hjälp av Data Box-enhet. [Läs mer](/azure/databox/data-box-overview). |
| Azure DevTest Labs       | Microsoft. DevTestLab       | Skapande av anpassad avbildning och artefakt installation. [Läs mer](../../devtest-labs/devtest-lab-overview.md). |
| Azure Event Grid         | Microsoft. EventGrid        | Aktivera Blob Storage händelse publicering och Tillåt Event Grid att publicera till lagrings köer. Lär dig mer om [Blob Storage-händelser](/azure/event-grid/event-sources) och [publicering till köer](/azure/event-grid/event-handlers). |
| Azure Event Hubs         | Microsoft. EventHub         | Arkivera data med Event Hubs avbildning. [Läs mer](/azure/event-hubs/event-hubs-capture-overview). |
| Azure File Sync          | Microsoft. StorageSync      | Gör att du kan omvandla din lokal fil server till ett cacheminne för Azure-filresurser. Tillåter synkronisering av flera platser, snabb katastrof återställning och säkerhets kopiering på moln sidan. [Läs mer](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft. HDInsight        | Etablera det inledande innehållet i standard fil systemet för ett nytt HDInsight-kluster. [Läs mer](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Azure import-export      | Microsoft. ImportExport     | Möjliggör import av data till Azure Storage eller export av data från Azure Storage med hjälp av Azure Storage import/export-tjänsten. [Läs mer](/azure/storage/common/storage-import-export-service).  |
| Azure Monitor            | Microsoft. Insights         | Tillåter skrivning av övervaknings data till ett skyddat lagrings konto, inklusive resurs loggar, Azure Active Directory inloggnings-och gransknings loggar och Microsoft Intune loggar. [Läs mer](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Azure-nätverk         | Microsoft.Network          | Lagra och analysera nätverks trafik loggar, inklusive via Network Watcher-och Trafikanalys-tjänsterna. [Läs mer](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Azure Site Recovery      | Microsoft. SiteRecovery     | Aktivera replikering för haveri beredskap för virtuella Azure IaaS-datorer när du använder brand Väggs-aktiverade cache-, käll-eller mål lagrings konton.  [Läs mer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

Inställningen **Tillåt betrodda Microsoft-tjänster...** tillåter också att en viss instans av nedanstående tjänster får åtkomst till lagrings kontot, om du uttryckligen [tilldelar en Azure-roll](storage-auth-aad.md#assign-azure-roles-for-access-rights) till den [systemtilldelade hanterade identiteten](../../active-directory/managed-identities-azure-resources/overview.md) för den resurs instansen. I det här fallet motsvarar åtkomst omfånget för instansen den Azure-roll som tilldelats den hanterade identiteten.

| Tjänst                        | Namn på resurs leverantör                 | Syfte            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure API Management           | Microsoft.ApiManagement/service        | Aktiverar API Management-tjänstens åtkomst till lagrings konton bakom brand väggen med hjälp av principer. [Läs mer](/azure/api-management/api-management-authentication-policies#use-managed-identity-in-send-request-policy). |
| Azure Cognitive Search         | Microsoft. search/searchServices        | Ger Kognitiv sökning-tjänster åtkomst till lagrings konton för indexering, bearbetning och frågor. |
| Azure Container Registry Tasks | Microsoft. ContainerRegistry/register | ACR-aktiviteter kan komma åt lagrings konton när du skapar behållar avbildningar. |
| Azure Data Factory             | Microsoft. DataFactory/fabriker        | Ger åtkomst till lagrings konton via ADF-körningen. |
| Azure Data Share               | Microsoft. DataShare/konton           | Ger åtkomst till lagrings konton via data resurs. |
| Azure IoT Hub                  | Microsoft. Devices/IotHubs              | Tillåter att data från en IoT-hubb skrivs till Blob Storage. [Läs mer](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft. Logic/arbets flöden              | Gör att Logic Apps kan komma åt lagrings konton. [Läs mer](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity). |
| Azure Machine Learning-tjänsten | Microsoft.MachineLearningServices      | Auktoriserade Azure Machine Learning arbets ytor skriver experiment, modeller och loggar till Blob Storage och läser data. [Läs mer](/azure/machine-learning/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). | 
| Azure Synapse Analytics (tidigare SQL Data Warehouse)       | Microsoft.Sql                          | Tillåter import och export av data från vissa SQL-databaser med hjälp av KOPIERINGs instruktionen eller polybasen. [Läs mer](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure SQL Database       | Microsoft.Sql                          | Tillåter [import](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql?view=sql-server-ver15#f-importing-data-from-a-file-in-azure-blob-storage) av data från lagrings konton och [skrivning](https://docs.microsoft.com/azure/azure-sql/database/audit-write-storage-account-behind-vnet-firewall) av gransknings data till lagrings konton bakom brand väggen. |
| Azure Stream Analytics         | Microsoft. StreamAnalytics             | Tillåter att data från ett strömmande jobb skrivs till Blob Storage. Den här funktionen finns för närvarande som en förhandsversion. [Läs mer](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | Microsoft. Synapse/arbets ytor          | Ger åtkomst till data i Azure Storage från Synapse Analytics. |


### <a name="storage-analytics-data-access"></a>Åtkomst till Storage Analytics-data

I vissa fall krävs åtkomst till läsa resurs loggar och mät värden utanför nätverks gränserna. När du konfigurerar betrodda tjänster till lagrings kontot kan du tillåta Läs åtkomst för loggfiler, mått tabeller eller både och. [Lär dig mer om hur du arbetar med lagrings analys.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Hantera undantag

Du kan hantera nätverks regel undantag via Azure Portal, PowerShell eller Azure CLI v2.

#### <a name="azure-portal"></a>Azure Portal

1. Gå till det lagringskonto som du vill skydda.

1. Klicka på menyn Inställningar, som kallas **brand väggar och virtuella nätverk**.

1. Kontrol lera att du har valt att tillåta åtkomst från **valda nätverk**.

1. Under **undantag**väljer du de undantag som du vill bevilja.

1. Klicka på **Spara** för att tillämpa dina ändringar.

#### <a name="powershell"></a>PowerShell

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) och [Logga](/powershell/azure/authenticate-azureps)in.

1. Visa undantagen för lagrings kontots nätverks regler.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Konfigurera undantagen för lagrings kontots nätverks regler.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Ta bort undantagen till lagrings kontots nätverks regler.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) som **neka**eller ta bort undantag har ingen påverkan.

#### <a name="cliv2"></a>CLIv2

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [Logga](/cli/azure/authenticate-azure-cli)in.

1. Visa undantagen för lagrings kontots nätverks regler.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Konfigurera undantagen för lagrings kontots nätverks regler.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Ta bort undantagen till lagrings kontots nätverks regler.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) som **neka**eller ta bort undantag har ingen påverkan.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Network Service-slutpunkter i [tjänst slut punkter](/azure/virtual-network/virtual-network-service-endpoints-overview).

Gå djupare i Azure Storage säkerhet i [Azure Storage säkerhets guide](../blobs/security-recommendations.md).
