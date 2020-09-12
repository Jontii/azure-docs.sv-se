---
title: 'Azure-VPN Gateway: Felsöka punkt-till-plats-anslutningar: Mac OS X-klienter'
description: Lär dig hur du felsöker problem med punkt-till-plats-anslutning från Mac OS X med hjälp av den inbyggda VPN-klienten och IKEv2.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: 33e67df5ad68a78cc35f7168813716d1d794d527
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442810"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Felsöka punkt-till-plats-VPN-anslutningar från Mac OS X VPN-klienter

Den här artikeln hjälper dig att felsöka problem med punkt-till-plats-anslutningen från Mac OS X med hjälp av den inbyggda VPN-klienten och IKEv2. VPN-klienten i Mac för IKEv2 är mycket grundläggande och tillåter inte stor anpassning. Det finns bara fyra inställningar som behöver kontrol leras:

* Server adress
* Fjärr-ID
* Lokalt ID
* Autentiseringsinställningar
* OS-version (10,11 eller högre)


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a> Felsöka certifikatbaserad autentisering
1. Kontrol lera inställningarna för VPN-klienten. Gå till **nätverks inställningen** genom att trycka på Kommando + Skift och skriv sedan "VPN" för att kontrol lera inställningarna för VPN-klienten. I listan klickar du på den VPN-post som behöver undersökas.

   ![IKEv2-certifikatbaserad autentisering](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Kontrol lera att **Server adressen** är fullständigt FQDN och att den innehåller cloudapp.net.
3. **Fjärr-ID** ska vara detsamma som Server adressen (Gateway FQDN).
4. Det **lokala ID: t** ska vara samma som för klient certifikatets **ämne** .
5. Klicka på **autentiseringsinställningar** för att öppna sidan autentiseringsinställningar.

   ![Autentiseringsinställningar](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Kontrol lera att **certifikatet** är markerat i list rutan.
7. Klicka på knappen **Välj** och kontrol lera att rätt certifikat har valts. Spara ändringarna genom att klicka på **OK** .

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a>Felsöka användar namn och lösenordsautentisering

1. Kontrol lera inställningarna för VPN-klienten. Gå till **nätverks inställningen** genom att trycka på Kommando + Skift och skriv sedan "VPN" för att kontrol lera inställningarna för VPN-klienten. I listan klickar du på den VPN-post som behöver undersökas.

   ![Lösen ord för IKEv2 användar namn](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Kontrol lera att **Server adressen** är fullständigt FQDN och att den innehåller cloudapp.net.
3. **Fjärr-ID** ska vara detsamma som Server adressen (Gateway FQDN).
4. **Lokalt ID** kan vara tomt.
5. Klicka på knappen **autentiseringsinställningar** och kontrol lera att "username" är markerat i list rutan.

   ![Autentiseringsinställningar](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Kontrol lera att rätt autentiseringsuppgifter har angetts.

## <a name="additional-steps"></a><a name="additional"></a>Ytterligare steg

Om du försöker med föregående steg och allt är korrekt konfigurerat, laddar du ned [wireshark](https://www.wireshark.org/#download) och utför en paket fångst.

1. Filtrera på *ISAKMP* och titta på **IKE_SA** paket. Du bör kunna se information om SA-förslagen under **nytto lasten: säkerhets Association**. 
2. Kontrol lera att klienten och servern har en gemensam uppsättning.

   ![åtkomstaccepterande](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Om det inte finns något Server svar på nätverks spåren kontrollerar du att IKEv2-protokollet har Aktiver ATS på konfigurations sidan för Azure gateway på Azure Portal webbplats.

## <a name="next-steps"></a>Nästa steg
Mer hjälp finns i [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
