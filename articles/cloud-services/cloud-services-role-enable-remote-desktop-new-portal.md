---
title: Använd portalen för att aktivera fjärr skrivbord för en roll
titleSuffix: Azure Cloud Services
description: Så här konfigurerar du Azure Cloud Service-programmet för att tillåta fjärr skrivbords anslutningar
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: d65f4b55be317234c10a0e90cfe413d9e38a6a90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84696787"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Aktivera Anslutning till fjärrskrivbord för en roll i Azure Cloud Services

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Med fjärr skrivbord kan du komma åt Skriv bordet för en roll som körs i Azure. Du kan använda en anslutning till fjärr skrivbord för att felsöka och diagnostisera problem med programmet medan det körs.

Du kan aktivera en anslutning till fjärr skrivbord i din roll under utvecklingen genom att inkludera fjärr skrivbords-moduler i din tjänst definition eller så kan du välja att aktivera fjärr skrivbord via fjärr skrivbords tillägget. Den bästa metoden är att använda fjärr skrivbords tillägget som du kan aktivera fjärr skrivbord även efter att programmet har distribuerats utan att du behöver distribuera om ditt program.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Konfigurera fjärr skrivbord från Azure Portal

I Azure Portal används metoden för fjärr skrivbords tillägg så att du kan aktivera fjärr skrivbord även efter att programmet har distribuerats. Med **fjärr skrivbords** inställningarna för moln tjänsten kan du aktivera fjärr skrivbord, ändra det lokala administratörs kontot som används för att ansluta till de virtuella datorerna, certifikatet som används i autentisering och ange förfallo datum.

1. Klicka på **Cloud Services**, Välj namnet på moln tjänsten och välj sedan **fjärr skrivbord**.

    ![Moln tjänster, fjärr skrivbord](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Välj om du vill aktivera fjärr skrivbord för en enskild roll eller för alla roller och ändra värdet för växlaren till **aktive rad**.

3. Fyll i de obligatoriska fälten för användar namn, lösen ord, förfallo datum och certifikat.

    ![Moln tjänster, fjärr skrivbord](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Alla roll instanser kommer att startas om när du först aktiverar fjärr skrivbord och väljer **OK** (bock). För att förhindra en omstart måste certifikatet som används för att kryptera lösen ordet vara installerat på rollen. Du kan förhindra en omstart genom att [överföra ett certifikat för moln tjänsten](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) och sedan gå tillbaka till den här dialog rutan.

4. I **roller**väljer du den roll som du vill uppdatera eller väljer **alla** för alla roller.

5. När du är klar med konfigurations uppdateringarna väljer du **Spara**. Det tar en stund innan roll instanserna är redo att ta emot anslutningar.

## <a name="remote-into-role-instances"></a>Fjärran slut till roll instanser

När fjärr skrivbord är aktiverat på rollerna kan du starta en anslutning direkt från Azure Portal:

1. Öppna **instans** inställningarna genom att klicka på **instanser** .
2. Välj en roll instans som har fjärr skrivbord konfigurerat.
3. Klicka på **Anslut** för att ladda ned en RDP-fil för roll instansen.

    ![Moln tjänster, fjärr skrivbord](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Klicka på **Öppna** och **Anslut** för att starta anslutningen till fjärr skrivbordet.

>[!NOTE]
> Om din moln tjänst är placerad bakom en NSG kan du behöva skapa regler som tillåter trafik på portarna **3389** och **20000**.  Fjärr skrivbord använder port **3389**.  Moln tjänst instanser är belastningsutjämnade, så du kan inte styra vilken instans som ska anslutas direkt.  *RemoteForwarder* -och *RemoteAccess* -agenterna hanterar RDP-trafik och gör att klienten kan skicka en RDP-cookie och ange en enskild instans att ansluta till.  *RemoteForwarder* -och *RemoteAccess* -agenterna kräver att port **20000*** är öppen, vilket kan blockeras om du har en NSG.

## <a name="additional-resources"></a>Ytterligare resurser

[Så här konfigurerar du Cloud Services](cloud-services-how-to-configure-portal.md)
