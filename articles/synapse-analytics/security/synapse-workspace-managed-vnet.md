---
title: Hanterat virtuellt nätverk
description: En artikel som förklarar hanterade virtuella nätverk i Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 06b535b25df19e5062d16184f4469d9e9253b9c0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "87042607"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Azure Synapse Analytics-hanterad Virtual Network (för hands version)

I den här artikeln förklaras hanterade Virtual Network i Azure Synapse Analytics.

## <a name="managed-workspace-virtual-network"></a>Hanterad arbets yta Virtual Network

När du skapar din Azure dataSynapses-arbetsyta kan du välja att koppla den till en Microsoft Azure Virtual Network. Virtual Network som är kopplad till din arbets yta hanteras av Azure dataSynapses. Den här Virtual Network kallas för en *hanterad arbets yta Virtual Network*.

I den hanterade arbets ytan Virtual Network får du ett värde på fyra sätt:

- Med en hanterad arbets yta Virtual Network kan du avlasta hanteringen av Virtual Network till Azure-Synapse.
- Du behöver inte konfigurera inkommande NSG-regler på dina egna virtuella nätverk för att tillåta Azure Synapse Management-trafik att ange Virtual Network. Felaktig konfiguration av dessa NSG-regler orsakar avbrott i tjänsten för kunder.
- Du behöver inte skapa ett undernät för dina Spark-kluster baserat på hög belastning.
- Hanterade arbets ytor Virtual Network tillsammans med hanterade privata slut punkter skyddar mot data exfiltrering. Du kan bara skapa hanterade privata slut punkter i en arbets yta som har en hanterad arbets yta Virtual Network associerad med den.

Om du skapar en arbets yta med en hanterad arbets yta Virtual Network associerad med den ser du till att din arbets yta är isolerad från andra arbets ytor. Azure Synapse innehåller olika analys funktioner i en arbets yta: data integrering, Apache Spark, SQL-pool och SQL på begäran.

Om arbets ytan har en hanterad arbets yta Virtual Network distribueras data integrering och Spark-resurser i den. En hanterad arbets yta Virtual Network även för isolering på användar nivå för Spark-aktiviteter eftersom varje Spark-kluster finns i ett eget undernät.

SQL-poolen och SQL på begäran är funktioner för flera klienter och finns därför utanför den hanterade arbets ytan Virtual Network. Kommunikation inom arbets ytan till SQL-poolen och SQL på begäran använder Azures privata länkar. De här privata länkarna skapas automatiskt åt dig när du skapar en arbets yta med en hanterad arbets yta Virtual Network kopplad till den.

>[!IMPORTANT]
>Du kan inte ändra den här arbets ytans konfiguration när arbets ytan har skapats. Du kan till exempel inte konfigurera om en arbets yta som inte har någon hanterad arbets yta Virtual Network kopplad till den och associera en Virtual Network till den. På samma sätt kan du inte konfigurera om en arbets yta med en hanterad arbets yta Virtual Network kopplad till den och ta bort kopplingen till Virtual Network från den.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-virtual-network"></a>Skapa en Azure dataSynapses-arbetsyta med en hanterad arbets yta Virtual Network

Registrera nätverks resurs leverantören om du inte redan har gjort det. När du registrerar en resurs leverantör konfigureras din prenumeration så att den fungerar med resurs leverantören. Välj *Microsoft. Network* i listan över resurs leverantörer när du [registrerar](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)dig.

Om du vill skapa en Azure Synapse-arbetsyta som har en hanterad arbets yta Virtual Network kopplad till den, väljer du fliken **säkerhet + nätverk** i Azure Portal och markerar kryss rutan **Aktivera hanterat virtuellt nätverk** .

Om du lämnar kryss rutan avmarkerad kommer din arbets yta inte ha någon Virtual Network kopplad till sig.

>[!IMPORTANT]
>Du kan bara använda privata länkar i en arbets yta som har en hanterad arbets yta Virtual Network.

![Aktivera hanterad arbets yta Virtual Network](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>All utgående trafik från den hanterade arbets ytan Virtual Network, förutom via hanterade privata slut punkter, kommer att blockeras i framtiden. Vi rekommenderar att du skapar hanterade privata slut punkter för att ansluta till alla dina Azure-datakällor utanför arbets ytan. 

Du kan kontrol lera om din Azure Synapse-arbetsyta är kopplad till en hanterad arbets yta Virtual Network genom att välja **Översikt** från Azure Portal.

![Översikt över arbets ytan i Azure Portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Nästa steg

Skapa en [Azure DataSynapses-arbetsyta](../quickstart-create-workspace.md)

Läs mer om [hanterade privata slut punkter](./synapse-workspace-managed-private-endpoints.md)

[Skapa hanterade privata slut punkter till dina data källor](./how-to-create-managed-private-endpoints.md)
