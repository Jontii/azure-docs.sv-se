---
title: Planera distributionen av Azure VMware-lösningen
description: Den här artikeln beskriver ett arbets flöde för distribution av Azure VMware-lösningar.  Det slutliga resultatet är en miljö som är redo för generering och migrering av virtuella datorer (VM).
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: e279f14406d464171f0879d85cc33f9844d22ec3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802216"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Planera distributionen av Azure VMware-lösningen

I den här artikeln ger vi dig planerings processen för att identifiera och samla in data som används under distributionen. [Använd check listan före distribution](pre-deployment-checklist.md) för att dokumentera informationen och för enkel referens under distributionen.  

Processerna för den här snabb starten resulterar i en produktions klar miljö för att skapa virtuella datorer och migrering. 

>[!IMPORTANT]
>Innan du skapar din Azure VMware-lösnings resurs måste du skicka in ett support ärende om du vill att dina noder ska tilldelas. När support teamet har tagit emot din begäran tar det upp till fem arbets dagar för att bekräfta din begäran och allokera noderna. Om du har ett befintligt privat moln i Azure VMware-lösningen och vill att fler noder ska tilldelas, går du igenom samma process. Mer information finns i [så här aktiverar du Azure VMware-lösnings resurser](enable-azure-vmware-solution.md). 

## <a name="subscription"></a>Prenumeration

Identifiera den prenumeration som du planerar att använda för att distribuera Azure VMware-lösningen.  Du kan antingen skapa en ny prenumeration eller återanvända en befintlig.

>[!NOTE]
>Prenumerationen måste vara kopplad till en Microsoft Enterprise-avtal.

## <a name="resource-group"></a>Resursgrupp

Identifiera den resurs grupp som du vill använda för din Azure VMware-lösning.  I allmänhet skapas en resurs grupp specifikt för Azure VMware-lösningen, men du kan använda en befintlig resurs grupp.

## <a name="region"></a>Region

Identifiera den region där du vill distribuera Azure VMware-lösningen.  Mer information finns i Azure- [produkter som är tillgängliga enligt region guide](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware).

## <a name="resource-name"></a>Resursnamn

Definiera resurs namnet som du ska använda under distributionen.  Resurs namnet är ett eget och beskrivande namn som du kan använda för att ge ditt privata moln i Azure VMware-lösningen.

## <a name="size-nodes"></a>Noder storlek

Identifiera de storleks noder som du vill använda när du distribuerar Azure VMware-lösningen.  En fullständig lista finns i dokumentationen för [Azure VMware-lösningen privata moln och kluster](concepts-private-clouds-clusters.md#hosts) .

## <a name="number-of-hosts"></a>Antal värdar

Definiera antalet värdar som du vill distribuera till Azures privata moln för VMware-lösningen.  Det minsta antalet noder är tre och det maximala värdet är 16 per kluster.  Mer information finns i dokumentationen för [Azure VMware-lösningen privat moln och kluster](concepts-private-clouds-clusters.md#clusters) .

Du kan alltid utöka klustret senare om du behöver gå bortom det ursprungliga distributions numret.

## <a name="vcenter-admin-password"></a>administratörs lösen ord för vCenter
Definiera vCenter-administratörens lösen ord.  Under distributionen skapar du ett vCenter admin-lösenord. Lösen ordet är till cloudadmin@vsphere.local Administratörs kontot under vCenter-versionen. Du använder den för att logga in på vCenter.

## <a name="nsx-t-admin-password"></a>NSX-T admin-lösenord
Definiera NSX-T admin-lösenordet.  Under distributionen skapar du ett NSX-T-administratörs lösen ord. Lösen ordet tilldelas administratörs användaren i NSX-kontot under NSX-versionen. Du använder den för att logga in i NSX Manager.

## <a name="ip-address-segment"></a>IP-adress segment

Det första steget när du planerar distributionen är att planera IP-segmentering.  Azure VMware-lösningen matar in ett/22-nätverk som du anger. Carves sedan upp i mindre segment och använder sedan de här IP-segmenten för vCenter, VMware HCX, NSX-T och vMotion.

Azure VMware-lösningen ansluter till din Microsoft Azure Virtual Network via en intern ExpressRoute-krets. I de flesta fall ansluter den till ditt data Center via ExpressRoute Global Reach. 

Azure VMware-lösning, din befintliga Azure-miljö och din lokala miljö alla Exchange-vägar (vanligt vis). Det vill säga att det CIDR-adressblock för CIDR-nätverket som du definierar i det här steget inte överlappar något som du redan har lokalt eller Azure.

**Exempel:** 10.0.0.0/22

Mer information finns i [Check lista för nätverks planering](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identifiera IP-adress segment" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>IP-adress segment för virtuella dator arbets belastningar

Identifiera ett IP-segment för att skapa ditt första nätverk (NSX-segment) i ditt privata moln.  Med andra ord vill du skapa ett nätverks segment i Azure VMware-lösningen så att du kan distribuera virtuella datorer till Azure VMware-lösningen.   

Även om du bara planerar att utöka L2-nätverk skapar du ett nätverks segment som är användbart för att verifiera miljön.

Kom ihåg att alla IP-segment som skapats måste vara unika i Azure och lokalt.  

**Exempel:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identifiera IP-adress segment" border="false":::     

## <a name="optional-extend-networks"></a>Valfritt Utöka nätverk

Du kan utöka nätverks segmenten från lokal till Azure VMware-lösning. om du gör det kan du identifiera dessa nätverk nu.  

Tänk på följande:

- Om du planerar att utöka nätverk lokalt måste dessa nätverk ansluta till en [vSphere-distribuerad växel (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) i din lokala VMware-miljö.  
- Om de nätverk som du vill utöka Live på en [vSphere standard växel](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html), kan de inte utökas.

## <a name="expressroute-global-reach-peering-network"></a>ExpressRoute Global Reach peering Network

Identifiera ett `/29` CIDR-nätverk för adress block som krävs för ExpressRoute Global Reach-peering. Kom ihåg att alla IP-segment som skapats måste vara unika i din Azure VMware-lösning och lokalt. IP-adresserna i det här segmentet används i varje ände av ExpressRoute-Global Reach-anslutningen för att ansluta ExpressRoute-kretsen för Azure VMware-lösningen med den lokala ExpressRoute-kretsen. 

**Exempel:** 10.1.0.0/29

:::image type="content" source="media/pre-deployment/expressroute-global-reach-ip-diagram.png" alt-text="Identifiera IP-adress segment" border="false":::

## <a name="azure-virtual-network-to-attach-azure-vmware-solution"></a>Azure Virtual Network för att ansluta Azure VMware-lösning

För att få åtkomst till ditt privata moln i Azure VMware-lösningen måste ExpressRoute-kretsen, som medföljer Azure VMware-lösningen, kopplas till en Azure-Virtual Network.  Under distributionen kan du definiera ett nytt virtuellt nätverk eller välja ett befintligt.

ExpressRoute-kretsen från Azure VMware-lösningen ansluter till en ExpressRoute-gateway i Azure-Virtual Network som du definierar i det här steget.  

>[!IMPORTANT]
>Om du väljer ett befintligt virtuellt nätverk måste du välja ett som inte har ett redan befintligt Gateway-undernät.  

Om du vill ansluta ExpressRoute-kretsen från Azure VMware-lösningen till en befintlig ExpressRoute-Gateway kan du göra det efter distributionen.  

Så i sammanfattning vill du ansluta Azure VMware-lösningen till en befintlig Express Route-Gateway?  

* **Ja** = identifiera det virtuella nätverk som inte används under distributionen.
* **Nej** = identifiera ett befintligt virtuellt nätverk eller skapa ett nytt under distributionen.

Oavsett hur du vill kan du dokumentera vad du vill göra i det här steget.

>[!NOTE]
>Det här virtuella nätverket visas av din lokala miljö och Azure VMware-lösning, så se till att det IP-segment som du använder i det här virtuella nätverket och undernät inte överlappar.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identifiera IP-adress segment" border="false":::

## <a name="vmware-hcx-network-segments"></a>Nätverks segment för VMware HCX

VMware HCX är en teknik som ingår i Azure VMware-lösningen. De främsta användnings fallen för VMware HCX är migrering av arbets belastningar och haveri beredskap. Om du planerar att göra det, är det bäst att planera nätverk nu.   Annars kan du hoppa över och fortsätta till nästa steg.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du har samlat in och dokumenterat den information som behövs kan du fortsätta till nästa avsnitt för att skapa ett privat moln i Azure VMware-lösningen.

> [!div class="nextstepaction"]
> [Distribuera Azure VMware Solution](deploy-azure-vmware-solution.md)
