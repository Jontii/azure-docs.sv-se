---
title: Installera agenten för Azure AD Connect-molnetablering
description: Den här artikeln beskriver hur du installerar Azure AD Connect Cloud Provisioning-agenten.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3960b8afeb9d7ecc80aa49fc13eee4977fa5494
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173978"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Installera agenten för Azure AD Connect-molnetablering
Det här dokumentet vägleder dig genom installations processen för den Azure Active Directory (Azure AD) Connect-programetablerings agenten och hur du konfigurerar den för första gången i Azure Portal.

>[!IMPORTANT]
>Följande installations anvisningar förutsätter att alla [krav](how-to-prerequisites.md) är uppfyllda.

Att installera och konfigurera Azure AD Connect etablering sker i följande steg:

- [Grupphanterade tjänst konton](#group-managed-service-accounts) 
- [Installera agenten](#install-the-agent)
- [Verifiera agent installation](#verify-agent-installation)


## <a name="group-managed-service-accounts"></a>Grupphanterade tjänstkonton
Ett grupphanterat tjänst konto är ett hanterat domän konto som tillhandahåller automatisk lösen ords hantering, förenklad hantering av tjänst huvud namn (SPN), möjlighet att delegera hanteringen till andra administratörer och även utöka den här funktionaliteten över flera servrar.  Azure AD Connect Cloud Sync stöder och rekommenderar att ett grupphanterat tjänst konto används för att köra agenten.  Mer information om en gMSA finns i [gruppera hanterade tjänst konton](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 


### <a name="upgrading-an-existing-agent-to-use-the-gmsa-account"></a>Uppgradera en befintlig agent för att använda gMSA-kontot
Om du vill uppgradera en befintlig agent för att använda gMSA-kontot som skapades under installationen uppdaterar du bara Agent tjänsten till den senaste versionen genom att köra AADConnectProvisioningAgent.msi.  Då uppgraderas tjänsten till den senaste versionen.  Kör nu installations guiden igen och ange autentiseringsuppgifterna för att skapa kontot när du uppmanas till det.



## <a name="install-the-agent"></a>Installera agenten
Följ dessa steg om du vill installera agenten.

 1. Logga in på den server som du ska använda med företags administratörens behörigheter.
 2. Logga in på Azure Portal och gå sedan till **Azure Active Directory**.
 3. På den vänstra menyn väljer du **Azure AD Connect**.
 4. Välj **Hantera etablering (för hands version)**  >  **Granska alla agenter**.
 5. Hämta Azure AD Connect etablerings agenten från Azure Portal.
   ![Hämta lokal agent](media/how-to-install/install-9.png)</br>
 6. Kör installations programmet för Azure AD Connect etablerings AADConnectProvisioningAgent.msi.
 7. På skärmen **Microsoft Azure AD koppla etablerings agent paket** godkänner du licens villkoren och väljer **Installera**.
   ![Skärmen Microsoft Azure AD koppla etablerings agent paket](media/how-to-install/install-1.png)</br>
 8. När den här åtgärden har slutförts startar konfigurations guiden. Logga in med ditt globala administratörs konto för Azure AD.
 9. På **skärmen konfigurera tjänst konto** väljer du antingen **skapa gMSA** eller **Använd anpassade gMSA**.  Om du tillåter agenten att skapa kontot får den namnet provAgentgMSA $. Om du anger **Använd anpassade gMSA** uppmanas du att ange det här kontot.
 10. Ange autentiseringsuppgifter för domän administratören för att skapa det grupphanterade tjänst kontot som ska användas för att köra Agent tjänsten. Klicka på **Nästa**.  
   ![Skapa gMSA](media/how-to-install/install-12.png)</br>
 11. På skärmen **anslut Active Directory** väljer du **Lägg till katalog**. Logga sedan in med ditt Active Directory administratörs konto. Den här åtgärden lägger till din lokala katalog. 
 12. Du kan också hantera inställningen för de domänkontrollanter som agenten ska använda genom att välja **Välj domänkontrollantens prioritet** och sortera listan över domänkontrollanter.   Klicka på **OK**.
  ![Beställ domän controlllers](media/how-to-install/install-2a.png)</br>
 13. Välj **Nästa**.
  ![Anslut Active Directory skärmen](media/how-to-install/install-3a.png)</br>
 14.  På skärmen **installations** skärm för Agent bekräftar du inställningar och kontot som ska skapas och klickar på **Bekräfta**.
  ![Bekräfta settngs](media/how-to-install/install-11.png)</br>
 15. När den här åtgärden har slutförts bör du se **att Agent installationen är klar.** Välj **Avsluta**.
  ![Skärmen konfiguration slutförd](media/how-to-install/install-4a.png)</br>
1. Välj **Stäng** om du fortfarande ser det första **Microsoft Azure AD ansluta agenten för etablering av agent paket** .

## <a name="verify-agent-installation"></a>Verifiera agent installation
Agent verifiering sker i Azure Portal och på den lokala server som kör agenten.

### <a name="azure-portal-agent-verification"></a>Azure Portal agent verifiering
Följ dessa steg om du vill kontrol lera att agenten visas av Azure.

1. Logga in på Azure-portalen.
1. Välj **Azure Active Directory**  >  **Azure AD Connect** till vänster. I mitten väljer du **Hantera etablering (för hands version)**.

   ![Azure Portal](media/how-to-install/install-6.png)</br>

1.  På skärmen **Azure AD Provisioning (för hands version)** väljer du **Granska alla agenter**.

    ![Alternativet granska alla agenter](media/how-to-install/install-7.png)</br>
 
1. På skärmen **lokala etablerings agenter** visas de agenter som du har installerat. Kontrol lera att agenten i fråga finns där och har marker ATS som *aktiv*.

   ![Skärmen lokala etablerings agenter](media/how-to-install/verify-1.png)</br>



### <a name="on-the-local-server"></a>På den lokala servern
Kontrol lera att agenten körs genom att följa dessa steg.

1.  Logga in på servern med ett administratörs konto.
1.  Öppna **tjänster** genom att antingen navigera till den eller genom att **Starta**  >  **köra**  >  **Services. msc**.
1.  Under **tjänster** kontrollerar du att **Microsoft Azure AD ansluter agent uppdaterings** **agenten och Microsoft Azure AD ansluta etablerings agenten** är där och att deras status är *igång*.

    ![Sidan tjänster](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>Agenten har installerats men måste konfigureras och aktive ras innan den kommer att starta synkronisering av användare. Information om hur du konfigurerar en ny agent finns i [skapa en ny konfiguration för Azure AD Connect molnbaserad etablering](how-to-configure.md).




## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)
