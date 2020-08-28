---
title: Publicera Windows-datorer till Azure Security Center
description: Den här snabb starten visar hur du etablerar Log Analytics-agenten på en Windows-dator.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 2cec55b7c9450070d2e692fae50eff9ea880c54e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013749"
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>Snabbstart: Publicera Windows-datorer till Azure Security Center
När du har publicerat dina Azure-prenumerationer kan du aktivera Security Center för resurser som körs utanför Azure, till exempel lokalt eller i andra moln, genom att tillhandahålla Log Analytics agenten.

Den här snabb starten visar hur du installerar Log Analytics-agenten på en Windows-dator.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Om du inte har någon prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

Du måste vara på Security Center standard pris nivån innan du påbörjar den här snabb starten. Läs [Publicera din Azure-prenumeration till Security Center Standard](security-center-get-started.md) för instruktioner om uppgradering. Du kan prova Security Center Standard utan kostnad. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="add-new-windows-computer"></a>Lägg till ny Windows-dator

1. Logga in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/).
2. Välj **Security Center**på **Microsoft Azure** -menyn. **Security Center översikt** öppnas.

   ![Översikt över Security Center][2]

3. På huvudmenyn i Security Center väljer du **Komma igång**.
4. Välj fliken **Kom igång**.

   ![Kom igång][3]

5. Klicka på **Konfigurera** under **Lägg till datorer som inte är Azure-datorer**. En lista över dina Log Analytics-arbetsytor visas. Om det är tillämpligt innehåller listan standardarbetsytan som har skapats för dig av Security Center när automatisk etablering aktiverades. Välj den här arbetsytan eller någon annan arbetsyta som du vill använda.

    ![Lägga till en dator som inte är en Azure-dator](./media/quick-onboard-windows-computer/non-azure.png)

   Bladet **Direct Agent** öppnas med en nedladdningslänk för en Windows-agent och nycklar för arbetsyte-ID, för användning vid konfigureringen av agenten.

6. Välj den länk för att **ladda ned Windows-agent** som gäller för din typ av processor, så att du kan ladda ned installationsfilen.

7. Till höger om **arbetsyte-ID** väljer du ikonen för kopiera och klistra in ID:t i Anteckningar.

8. Till höger om **primärnyckel**, väljer du ikonen för kopiera och klistra in nyckeln i Anteckningar.

## <a name="install-the-agent"></a>Installera agenten
Nu måste du installera den hämtade filen på måldatorn.

1. Kopiera filen till måldatorn och kör installationsprogrammet.
2. På sidan **Välkommen** klickar du på **Nästa**.
3. På sidan **Licensvillkor** läser du licensen och väljer **Jag accepterar**.
4. På sidan **Målmapp** ändrar du eller behåller standardinstallationsmappen och väljer **Nästa**.
5. På sidan **Installationsalternativ för agent** väljer du att ansluta agenten till Azure Log Analytics och väljer **Nästa**.
6. På sidan **Azure Log Analytics** klistrar du in **arbetsyte-ID** och **arbetsytenyckel (primär nyckel)** som du kopierade till Anteckningar i tidigare steg.
7. Om datorn ska rapportera till en Log Analytics-arbetsyta i Azure Government-molnet väljer du **Azure US Government** i listrutan **Azure Cloud**. Om datorn behöver kommunicera via en proxyserver till Log Analytics-tjänsten väljer du **Avancerat** och anger URL och portnummer för proxyservern.
8. Välj **Nästa** när du har gjort de konfigurationsinställningar som krävs.

   ![Installera agenten][5]

9. På sidan **Klar att installera** kontrollerar du valen och väljer sedan **Installera**.
10. På sidan **Konfigurationen har slutförts** väljer du **Slutför**

När du är klar visas **Log Analytics agenten** på **kontroll panelen**. Du kan granska konfigurationen där och bekräfta att agenten är ansluten.

Mer information om att installera och konfigurera agenten finns i dokumentationen om att [ansluta Windows-datorer](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).

Nu kan du övervaka dina virtuella Azure-datorer och datorer som inte är Azure-datorer på ett ställe. Under **Compute** (Beräkna) har du en översikt över alla virtuella datorer och datorer tillsammans med rekommendationer. I varje kolumn finns en typ av rekommendationer. Färgen representerar den virtuella datorns eller datorns aktuella säkerhetsstatus för den rekommendationen. Security Center visar också eventuella identifieringar för dessa datorer i Säkerhetsaviseringar.

  ![Compute-bladet][6]

Det finns två typer av ikoner på **Compute**-bladet:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Datorer som inte är Azure-datorer

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>Rensa resurser
När den inte längre behövs kan du ta bort agenten från Windows-datorn.

Så här tar du bort agenten:

1. Öppna **kontroll panelen**.
2. Öppna **Program och funktioner**.
3. I **program och funktioner**väljer du **Log Analytics agent** och klickar på **Avinstallera**.

## <a name="next-steps"></a>Nästa steg
I den här snabb starten etablerade du Log Analytics-agenten på en Windows-dator. Om du vill läsa mer om hur du använder Security Center fortsätter du till självstudien om konfiguration av en säkerhetsprincip och utvärderar resursers säkerhet.

> [!div class="nextstepaction"]
> [Självstudie: Definiera och utvärdera säkerhetsprinciper](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
