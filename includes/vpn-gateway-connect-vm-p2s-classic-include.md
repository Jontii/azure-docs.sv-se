---
title: inkludera fil
description: inkludera fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6d0737a7300b2a6025f776c1ed65a05cacf2141a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "67187209"
---
Skapa en anslutning till fjärrskrivbord för att ansluta till en virtuell dator som distribueras till ditt VNet. Det bästa sättet att kontrollera att du kan ansluta till den virtuella datorn är via dess privata IP-adress i stället för datornamnet. På så sätt testar du om du kan ansluta, inte huruvida namnmatchningen är korrekt konfigurerad. 

1. Leta upp den privata IP-adressen för den virtuella datorn. Du hittar den privata IP-adressen för en virtuell dator genom att granska egenskaperna för den virtuella datorn på Azure-portalen eller med hjälp av PowerShell.
2. Kontrollera att du är ansluten till ditt VNet med punkt-till-plats-anslutningen via VPN. 
3. Du öppnar fjärrskrivbordsanslutningen genom att ange *RDP* eller *Anslutning till fjärrskrivbord* i sökrutan i Aktivitetsfältet och sedan välja **Anslutning till fjärrskrivbord**. Du kan även öppna den med hjälp av kommandot **mstsc** i PowerShell. 
3. I **fjärrskrivbordsanslutningen** anger du den virtuella datorns privata IP-adress. Vid behov väljer du **Visa alternativ** för att justera ytterligare inställningar och ansluter därefter.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Felsöka en RDP-anslutning till en virtuell dator

Om du har problem med att ansluta till en virtuell dator via VPN-anslutningen finns det några saker som du kan kontrollera. 

- Kontrollera att VPN-anslutningen har genomförts.
- Kontrollera att du ansluter till den virtuella datorns privata IP-adress.
- Ange **ipconfig** för att kontrollera vilken IPv4-adress som har tilldelats till Ethernet-adaptern på den dator som du ansluter från. Ett överlappande adressutrymme uppstår om IP-adressen ligger inom adressintervallet för det virtuella nätverk som du ansluter till eller inom adressintervallet för din VPNClientAddressPool. När ditt adressutrymme överlappar på det här sättet når inte nätverkstrafiken Azure, utan stannar i det lokala nätverket.
- Om du kan ansluta till den virtuella datorn med hjälp av den privata IP-adressen, men inte med namnet på datorn, kontrollerar du att du har konfigurerat DNS korrekt. Mer information om hur namnmatchningen fungerar för virtuella datorer finns i [Namnmatchning för virtuella datorer](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Kontrollera att paketet för VPN-klientkonfiguration skapas efter att du anger IP-adresserna för DNS-server för det virtuella nätverket. Om du uppdaterar IP-adresserna för DNS-servern skapar och installerar du ett nytt paket för VPN-klientkonfiguration.

Mer felsökningsinformation finns i [Felsöka fjärrskrivbordsanslutningar till en virtuell dator](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).