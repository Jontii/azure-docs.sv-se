---
title: Självstudie – integrera en befintlig skog och en ny skog med en enda Azure AD-klient med Azure AD Connect moln etablering.
description: Lär dig hur du lägger till moln etablering i en befintlig hybrid identitets miljö. .
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25e70809becd59ceeb3c45867b9e35119df497a3
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526926"
---
# <a name="integrate-an-existing-forest-and-a-new-forest-with-a-single-azure-ad-tenant"></a>Integrera en befintlig skog med en ny skog med en enskild Azure AD-klientorganisation

Den här självstudien vägleder dig genom att lägga till moln etablering i en befintlig hybrid identitets miljö. 

![Skapa](media/tutorial-existing-forest/existing-forest-new-forest.png)

Du kan använda den miljö som du skapar i den här självstudien för att testa eller för att få bättre kunskaper om hur en hybrid identitet fungerar. 

I det här scenariot synkroniseras en befintlig skog med hjälp av Azure AD Connect synkronisering till en Azure AD-klient. Och du har en ny skog som du vill synkronisera till samma Azure AD-klient. Du kommer att konfigurera moln etablering för den nya skogen. 

## <a name="prerequisites"></a>Förutsättningar
### <a name="in-the-azure-active-directory-admin-center"></a>I Azure Active Directory administrations Center

1. Skapa ett globalt administratörs konto för molnet på Azure AD-klienten. På så sätt kan du hantera konfigurationen av din klient organisation om dina lokala tjänster kraschar eller blir otillgängliga. Lär dig mer om [att lägga till ett globalt administratörs konto för molnet](../fundamentals/add-users-azure-active-directory.md). Att slutföra det här steget är viktigt för att säkerställa att du inte blir utelåst från din klient.
2. Lägg till ett eller flera [anpassade domän namn](../fundamentals/add-custom-domain.md) i Azure AD-klienten. Användarna kan logga in med ett av dessa domän namn.

### <a name="in-your-on-premises-environment"></a>I din lokala miljö

1. Identifiera en domänansluten värd server som kör Windows Server 2012 R2 eller senare med minst 4 GB RAM-minne och .NET 4.7.1 + runtime 

2. Om det finns en brand vägg mellan dina servrar och Azure AD konfigurerar du följande objekt:
   - Se till att agenter kan göra *utgående* begär anden till Azure AD över följande portar:

     | Portnummer | Hur den används |
     | --- | --- |
     | **80** | Hämtar listor över återkallade certifikat (CRL) När TLS/SSL-certifikatet verifieras |
     | **443** | Hanterar all utgående kommunikation med tjänsten |
     | **8080** (valfritt) | Agenter rapporterar sin status var 10: e minut via port 8080, om port 443 inte är tillgänglig. Den här statusen visas på Azure AD-portalen. |
     
     Om brand väggen tillämpar regler enligt de ursprungliga användarna öppnar du portarna för trafik från Windows-tjänster som körs som en nätverks tjänst.
   - Om din brand vägg eller proxy låter dig ange säkra suffix, lägger du till anslutningar till ** \* . msappproxy.net** och ** \* . ServiceBus.Windows.net**. Om inte, Tillåt åtkomst till [Azure datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653)som uppdateras varje vecka.
   - Dina agenter behöver åtkomst till **login.Windows.net** och **login.microsoftonline.com** för inledande registrering. Öppna brand väggen för dessa URL: er även.
   - För certifikat validering, avblockera följande URL: er: **mscrl.Microsoft.com:80**, **CRL.Microsoft.com:80**, **OCSP.msocsp.com:80**och **www \. Microsoft.com:80**. Eftersom dessa URL: er används för certifikat validering med andra Microsoft-produkter kan dessa URL: er vara avblockerade.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Installera Azure AD Connect etablerings agenten
1. Logga in på den domänanslutna servern.  Om du använder  [Basic AD-och Azures miljö](tutorial-basic-ad-azure.md) vägledning är det DC1.
2. Logga in på Azure Portal med endast molnbaserad autentiseringsuppgifter för globala administratörer.
3. Till vänster väljer du **Azure Active Directory**, klickar på **Azure AD Connect** och i mitten väljer du **Hantera etablering (för hands version)**.</br>
![Azure-portalen](media/how-to-install/install6.png)</br>
4. Klicka på "Ladda ned agent"
5. Kör Azure AD Connect etablerings agenten
6. På Välkomst skärmen **godkänner** du licens villkoren och klickar på **Installera**.</br>
![Välkomstskärmen](media/how-to-install/install1.png)</br>

7. När den här åtgärden har slutförts startas konfigurations guiden.  Logga in med ditt globala administratörs konto för Azure AD.  Observera att om du har aktiverat förbättrad säkerhet i Internet Explorer blockeras inloggningen.  Om så är fallet, Stäng installationen, inaktivera Förbättrad säkerhet i Internet Explorer i Serverhanteraren och klicka på **guiden AAD Connect etablerings agent** för att starta om installationen.
8. På skärmen **anslut Active Directory** klickar du på **Lägg till katalog** och loggar sedan in med ditt Active Directory domän administratörs konto.  Obs! domän administratörs kontot ska inte ha krav på lösen ords ändring. Om lösen ordet går ut eller ändras måste du konfigurera om agenten med de nya autentiseringsuppgifterna. Den här åtgärden lägger till din lokala katalog.  Klicka på **Nästa**.</br>
![Välkomstskärmen](media/how-to-install/install3.png)</br>

9. Klicka på **Bekräfta**på sidan **konfiguration slutförd** .  Den här åtgärden registrerar och startar om agenten.</br>
![Välkomstskärmen](media/how-to-install/install4.png)</br>

10. När den här åtgärden har slutförts bör du se ett meddelande: **din agent konfiguration har verifierats.**  Du kan klicka på **Avsluta**.</br>
![Välkomstskärmen](media/how-to-install/install5.png)</br>
11. Om du fortfarande ser den inledande välkomst skärmen klickar du på **Stäng**.


## <a name="verify-agent-installation"></a>Verifiera agent installation
Agent verifiering sker i Azure Portal och på den lokala server som kör-agenten.

### <a name="azure-portal-agent-verification"></a>Azure Portal agent verifiering
Följ dessa steg för att kontrol lera att agenten visas i Azure:

1. Logga in på Azure Portal.
2. Till vänster väljer du **Azure Active Directory**, klickar på **Azure AD Connect** och i mitten väljer du **Hantera etablering (för hands version)**.</br>
![Azure-portalen](media/how-to-install/install6.png)</br>

3.  På skärmen **Azure AD Provisioning (för hands version)** klickar du på **Granska alla agenter**.
![Azure AD-etablering](media/how-to-install/install7.png)</br>
 
4. På **skärmen lokala etablerings agenter** visas de agenter som du har installerat.  Kontrol lera att agenten i fråga finns där och har marker ATS som **aktiv**.
![Etablerings agenter](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>På den lokala servern
Verifiera att agenten körs genom att följa dessa steg:

1.  Logga in på servern med ett administratörs konto
2.  Öppna **tjänster** genom att antingen navigera till den eller genom att gå till starta/köra/Services. msc.
3.  Under **tjänster**kontrollerar du att **Microsoft Azure AD ansluter agent uppdaterings** **agenten och Microsoft Azure AD ansluta etablerings agenten** finns och att statusen **körs**.
![Tjänster](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Konfigurera Azure AD Connect Cloud-etablering
 Använd följande steg för att konfigurera etableringen

1.  Logga in på Azure AD-portalen.
2.  Klicka på **Azure Active Directory**
3.  Klicka på **Azure AD Connect**
4.  Välj **Hantera Provisioning (för hands version)**- 
 ![ skärm bild som visar länken hantera etablering (för hands version).](media/how-to-configure/manage1.png)
5.  Klicka på **ny konfiguration** 
 ![ skärm bild av Azure AD Provisioning (för hands version) med länken "ny konfiguration" markerad.](media/tutorial-single-forest/configure1.png)
7.  På sidan konfiguration anger du ett **e-postmeddelande för aviseringar**, flyttar väljaren för att **Aktivera** och klickar på **Spara**.
![Skärm bild av konfigurera skärmen med e-postmeddelandet ifyllt och aktivera valt.](media/tutorial-single-forest/configure2.png)
1.  Konfigurations statusen bör nu vara **felfri**.
![Skärm bild av sidan för Azure AD Provisioning (för hands version) som visar felfri status.](media/how-to-configure/manage4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Verifiera att användare skapas och att synkronisering sker
Nu ska du kontrol lera att användarna som du hade i vår lokala katalog har synkroniserats och nu finns i vår Azure AD-klient.  Observera att det kan ta några timmar att slutföra.  För att verifiera att användarna synkroniseras gör du följande.


1. Bläddra till [Azure-portalen](https://portal.azure.com) och logga in med ett konto som har en Azure-prenumeration.
2. Välj **Azure Active Directory** till vänster
3. Under **Hantera** väljer du **Användare**.
4. Kontrollera att du ser de nya användarna i vår klient</br>
![Synch](media/tutorial-single-forest/synchronize1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Testa att logga in med någon av våra användare

1. Bläddra till [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Logga in med ett användarkonto som har skapats i vår nya klient.  Du måste logga in med följande format: (user@domain.onmicrosoft.com). Använd samma lösenord som användaren använder för att logga in lokalt.</br>
   ![Verifiera](media/tutorial-single-forest/verify1.png)</br>

Nu har du konfigurerat en hybrid identitets miljö som du kan använda för att testa och bekanta dig med vad Azure måste erbjuda.

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)