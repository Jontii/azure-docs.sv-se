---
title: Ansluta till en virtuell Windows-dator med Azure skydds
description: I den här artikeln får du lära dig hur du ansluter till en virtuell Azure-dator som kör Windows med hjälp av Azure-skydds.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 79eb09a005f62846fc2f7e3e7b493d5e366edabc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84744331"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Ansluta till en virtuell Windows-dator med Azure skydds

Med hjälp av Azure skydds kan du på ett säkert och smidigt sätt ansluta till dina virtuella datorer via SSL direkt i Azure Portal. När du använder Azure skydds kräver inte de virtuella datorerna någon klient, agent eller ytterligare program vara. Den här artikeln visar hur du ansluter till dina virtuella Windows-datorer. Information om hur du ansluter till en virtuell Linux-dator finns i [ansluta till en virtuell dator med Azure skydds – Linux](bastion-connect-vm-ssh.md).

Azure skydds ger säker anslutning till alla virtuella datorer i det virtuella nätverk där det är etablerad. Med hjälp av Azure skydds kan du skydda dina virtuella datorer från att exponera RDP/SSH-portar till utsidan, samtidigt som du ger säker åtkomst med RDP/SSH. Mer information finns i [Översikt](bastion-overview.md).

## <a name="before-you-begin"></a>Innan du börjar

Kontrol lera att du har konfigurerat en Azure skydds-värd för det virtuella nätverk där den virtuella datorn finns. När skydds-tjänsten har tillhandahållits och distribuerats i det virtuella nätverket kan du använda den för att ansluta till en virtuell dator i det virtuella nätverket. Information om hur du konfigurerar en Azure skydds-värd finns i [skapa en Azure skydds-värd](bastion-create-host-portal.md).

### <a name="required-roles"></a>Nödvändiga roller

Följande roller krävs för att upprätta en anslutning:

* Rollen läsare på den virtuella datorn
* Rollen läsare på NÄTVERKSKORTet med den virtuella datorns privata IP-adress
* Läsar roll på Azure skydds-resursen

### <a name="ports"></a>Portar

För att ansluta till den virtuella Windows-datorn måste du ha följande portar öppna på din virtuella Windows-dator:

* Inkommande portar: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Ansluta

1. Öppna [Azure-portalen](https://portal.azure.com). Navigera till den virtuella dator som du vill ansluta till och klicka sedan på **Anslut** och välj **skydds** i list rutan.

   ![Anslut till virtuell dator](./media/bastion-connect-vm-rdp/connect.png)
1. När du klickar på skydds visas ett sido fält med tre flikar – RDP, SSH och skydds. Om skydds etablerades för det virtuella nätverket är fliken skydds aktiv som standard. Om du inte etablerar skydds för det virtuella nätverket kan du klicka på länken för att konfigurera skydds. Konfigurations anvisningar finns i [Konfigurera skydds](bastion-create-host-portal.md).

   ![fliken skydds](./media/bastion-connect-vm-rdp/bastion.png)
1. På fliken skydds skriver du in användar namn och lösen ord för den virtuella datorn och klickar sedan på **Anslut**. RDP-anslutningen till den virtuella datorn via skydds öppnas direkt i Azure Portal (via HTML5) med port 443 och skydds-tjänsten.

   ![RDP-anslutning](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md)
