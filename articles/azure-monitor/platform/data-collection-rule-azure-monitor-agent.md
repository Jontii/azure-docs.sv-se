---
title: Konfigurera data insamling för Azure Monitor agenten (för hands version)
description: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: aa3225378f921792d1e8ba0442f2c555d095fb9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90968398"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Konfigurera data insamling för Azure Monitor agenten (för hands version)
Data insamlings regler (DCR) definiera data som kommer till Azure Monitor och ange var de ska skickas. Den här artikeln beskriver hur du skapar en data insamlings regel för att samla in data från virtuella datorer med hjälp av Azure Monitor agenten.

En fullständig beskrivning av regler för data insamling finns [i data insamlings regler i Azure Monitor (för hands version)](data-collection-rule-overview.md).

> [!NOTE]
> Den här artikeln beskriver hur du konfigurerar data för virtuella datorer med Azure Monitor-agenten som för närvarande är en för hands version. Se [Översikt över Azure Monitor agenter](agents-overview.md) för en beskrivning av agenter som är allmänt tillgängliga och hur de används för att samla in data.


## <a name="dcr-associations"></a>DCR-associationer
Om du vill tillämpa en DCR på en virtuell dator skapar du en Association för den virtuella datorn. En virtuell dator kan ha en association till flera DCRs och en DCR kan ha flera virtuella datorer kopplade till den. På så sätt kan du definiera en uppsättning DCRs, var och en matcha ett visst krav och tillämpa dem på enbart de virtuella datorer där de gäller. 

Överväg till exempel en miljö med en uppsättning virtuella datorer som kör ett affärs program och andra som kör SQL Server. Du kan ha en standard regel för data insamling som gäller för alla virtuella datorer och separata data insamlings regler som samlar in data specifikt för affärs programmet och för SQL Server. Associationerna för de virtuella datorerna till data insamlings reglerna ser ut ungefär som i följande diagram.

![Diagrammet visar virtuella datorer som är värdar för affärs program och SQL Server kopplade till data insamlings regler med namnet Central-i t-standard och LOB-app för affärs program och Central-i-t-standard och s q l för SQL Server.](media/data-collection-rule-azure-monitor-agent/associations.png)

## <a name="create-using-the-azure-portal"></a>Skapa med Azure Portal
Du kan använda Azure Portal för att skapa en data insamlings regel och koppla virtuella datorer i din prenumeration till den regeln. Den Azure Monitor agenten installeras automatiskt och en hanterad identitet skapas för virtuella datorer som inte redan har den installerad.

I **Azure Monitor** -menyn i Azure Portal väljer du **data insamlings regler** i avsnittet **Inställningar** . Klicka på **Lägg** till för att lägga till en ny data insamlings regel och tilldelning.

[![Data insamlings regler](media/azure-monitor-agent/data-collection-rules.png)](media/azure-monitor-agent/data-collection-rules.png#lightbox)

Klicka på **Lägg till** för att skapa en ny regel och en uppsättning associationer. Ange ett **regel namn** och ange en **prenumeration** och en **resurs grupp**. Detta anger var DCR ska skapas. De virtuella datorerna och deras associationer kan finnas i alla prenumerationer eller resurs grupper i klienten.

[![Grunder för data insamlings regel](media/azure-monitor-agent/data-collection-rule-basics.png)](media/azure-monitor-agent/data-collection-rule-basics.png#lightbox)

På fliken **Virtual Machines (virtuella datorer** ) lägger du till virtuella datorer som ska använda regeln för data insamling. Azure Monitor Agent installeras på virtuella datorer som inte redan har den installerad.

[![Data insamlings regel för virtuella datorer](media/azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

På fliken **samla in och leverera** klickar du på **Lägg till data källa** för att lägga till en data källa och mål uppsättning. Välj en **typ av data källa**så visas motsvarande information som ska väljas. För prestanda räknare kan du välja från en fördefinierad uppsättning objekt och deras samplings frekvens. För händelser kan du välja bland en uppsättning loggar eller anläggningar och allvarlighets grad. 

[![Grundläggande data Källa](media/azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Välj **anpassad**om du vill ange andra loggar och prestanda räknare. Du kan sedan ange en [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) för alla angivna värden som ska samlas in. Se exempel på [DCR](data-collection-rule-overview.md#sample-data-collection-rule) .

[![Anpassad data Källa](media/azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

På fliken **mål** lägger du till en eller flera mål för data källan. Windows-händelse och syslog-datakällor kan bara skicka till Azure Monitor loggar. Prestanda räknare kan skicka till både Azure Monitor mått och Azure Monitor loggar.

[![Mål](media/azure-monitor-agent/data-collection-rule-destination.png)](media/azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Klicka på **Lägg till data källa** och **granska sedan + skapa** för att granska informationen för data insamlings regeln och associationen med uppsättningen med virtuella datorer. Skapa genom att klicka på **skapa** .

> [!NOTE]
> När data insamlings regeln och associationerna har skapats kan det ta upp till 5 minuter innan data skickas till målen.

## <a name="createusingrestapi"></a>Skapa med REST-API
Följ stegen nedan för att skapa en DCR-och Association med hjälp av REST API. 
1.Skapa DCR-filen manuellt med JSON-formatet som visas i [exempel-DCR](data-collection-rule-overview.md#sample-data-collection-rule).
2.Skapa regeln med hjälp av [REST API](https://docs.microsoft.com/rest/api/monitor/datacollectionrules/create#examples).
3.Skapa en koppling för varje virtuell dator till data insamlings regeln med hjälp av [REST API](https://docs.microsoft.com/rest/api/monitor/datacollectionruleassociations/create#examples).

## <a name="next-steps"></a>Nästa steg

- Läs mer om den [Azure Monitor agenten](azure-monitor-agent-overview.md).
- Lär dig mer om [data insamlings regler](data-collection-rule-overview.md).
