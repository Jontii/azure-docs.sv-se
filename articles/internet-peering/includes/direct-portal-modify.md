---
title: inkludera fil
titleSuffix: Azure
description: inkludera fil
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3894bf046ed4ee3f068e43dbc5bc5b7f2a1002b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "81681035"
---
I det här avsnittet beskrivs hur du utför följande ändrings åtgärder för direkt peering.

### <a name="add-direct-peering-connections"></a>Lägg till direkta peering-anslutningar
1. Välj knappen **+ Lägg till anslutningar** och konfigurera en ny peering-anslutning.
    > [!div class="mx-imgBorder"]
    > ![Vyn peering-resurs](../media/setup-direct-modify-addconnection.png)

1. Fyll i formuläret **direkt peering-anslutning** och välj **Spara**. Om du behöver hjälp med att konfigurera en peering-anslutning går du igenom stegen i avsnittet "skapa och etablera en direkt peering".
    > [!div class="mx-imgBorder"]
    > ![Anslutnings formulär för direkt peering](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Ta bort direkta peering-anslutningar

Det finns för närvarande inte stöd för att ta bort en anslutning i Azure Portal. Kontakta [Microsoft-peering](mailto:peeringexperience@microsoft.com)om du vill ha mer information.

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Uppgradera eller nedgradera bandbredd på aktiva anslutningar
1. Välj en peering-anslutning som du vill ändra och välj sedan **...**  >  **Redigera anslutning**.
    > [!div class="mx-imgBorder"]
    > ![Redigera anslutning](../media/setup-direct-modify-editconnection.png)

1. Ändra bandbredd genom att flytta skjutreglaget och välj sedan **Spara**.
    > [!div class="mx-imgBorder"]
    > ![Ändra bandbredd](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>Lägg till information om IPv4-eller IPv6-session på aktiva anslutningar
1. Välj en peering-anslutning som du vill ändra och välj sedan **...**  >  **Redigera anslutning** enligt vad som visas i steg 1.
1. Ange information om **IPv4-prefix** eller sessioner för session **IPv6** och välj **Spara**.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Ta bort information om IPv4-eller IPv6-session på aktiva anslutningar
Det finns för närvarande inte stöd för att ta bort **sessions-IPv4-prefix** eller **-sessioner** i portalen. Kontakta [Microsoft-peering](mailto:peeringexperience@microsoft.com)om du vill ha mer information.
