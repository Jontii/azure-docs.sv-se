---
title: Publicera dina Azure Stack virtuella datorer till Azure Sentinel | Microsoft Docs
description: Den här artikeln visar hur du etablerar tillägget Azure Monitor, uppdatering och konfigurations hantering för virtuella datorer på Azure Stack virtuella datorer och börjar övervaka dem med kontroll.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 8114a5e6db7b82b846d221471f41dbdf418ddd9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89002002"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Anslut Azure Stack virtuella datorer till Azure Sentinel




Med Azure Sentinel kan du övervaka dina virtuella datorer som körs på Azure och Azure Stack på ett och samma ställe. Du måste först lägga till det virtuella dator tillägget till dina befintliga Azure Stack virtuella datorer för att kunna installera dina Azure Stack datorer i Azure Sentinel. 

När du har anslutit Azure Stack-datorer väljer du från ett galleri med instrument paneler som kan ligga insikter baserat på dina data. Dessa instrument paneler kan enkelt anpassas efter dina behov.



## <a name="add-the-virtual-machine-extension"></a>Lägg till tillägget för virtuell dator 

Lägg till tillägget **Azure Monitor, uppdatering och konfigurations hantering** för virtuell dator på de virtuella datorerna som körs på din Azure Stack. 

1. Logga in på din [Azure Stack-Portal](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal)på en ny flik i webbläsaren.
2. Gå till sidan **virtuella datorer** och välj den virtuella dator som du vill skydda med Azure Sentinel. Information om hur du skapar en virtuell dator på Azure Stack finns i [skapa en virtuell Windows Server-dator med Azure Stack portalen](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) eller [skapa en virtuell Linux-server med hjälp av Azure Stack Portal](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Välj **Tillägg**. Listan över virtuella dator tillägg som är installerade på den här virtuella datorn visas.
4. Klicka på fliken **Lägg till** . Bladet **Ny resurs** öppnas och visar en lista över tillgängliga tillägg för virtuella datorer. 
5. Välj tillägget **Azure Monitor, uppdatering och konfigurations hantering** och klicka på **skapa**. Fönstret **Installera tilläggs** konfiguration öppnas.

   ![Inställningar för Azure Monitor, uppdatering och konfigurations hantering](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Om du inte ser tillägget **Azure Monitor, uppdatering och konfigurations hantering** som visas på din Marketplace, kan du kontakta din Azure Stack-operatör för att göra det tillgängligt.

6. På menyn för Azure Sentinel väljer du **Inställningar för arbets yta** följt av **Avancerat**och kopierar **arbetsyte-ID: t** och **arbets ytans nyckel (primär nyckel)**. 
1. I fönstret Azure Stack **installations tillägg** klistrar du in dem i de angivna fälten och klickar på **OK**.
1. När tilläggs installationen har slutförts visas statusen som **etableringen har slutförts**. Det kan ta upp till en timme innan den virtuella datorn visas i Azure Sentinel-portalen.

Mer information om hur du installerar och konfigurerar agenten för Windows finns i [ansluta Windows-datorer](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).

Information om Linux-felsökning av agent problem finns i [Felsöka Azure Log Analytics Linux-agenten](../azure-monitor/platform/agent-linux-troubleshoot.md).

I Azure Sentinel-portalen på Azure, under **Virtual Machines**, har du en översikt över alla virtuella datorer och datorer tillsammans med deras status. 

## <a name="clean-up-resources"></a>Rensa resurser
När det inte längre behövs kan du ta bort tillägget från den virtuella datorn via Azure Stack-portalen.

Ta bort tillägget:

1. Öppna **Azure Stack-portalen**.
2. Gå till sidan **virtuella datorer** och välj den virtuella dator som du vill ta bort tillägget från.
3. Välj **tillägg**, Välj tillägget **Microsoft. EnterpriseCloud. Monitoring**.
4. Klicka på **Avinstallera**och bekräfta valet.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- Strömma data från [vanliga fel formats enheter](connect-common-event-format.md) till Azure Sentinel.
