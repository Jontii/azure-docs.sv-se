---
title: Självbetjänings registrering för e-postverifierade användare – Azure AD | Microsoft Docs
description: Använda självbetjänings registrering i en Azure Active Directory (Azure AD)-organisation
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: overview
ms.workload: identity
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ec578370d32664e36ffcc4bd075c3f6a3ed68ab
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88795350"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Vad är självbetjänings registrering för Azure Active Directory?

Den här artikeln förklarar hur du använder självbetjänings registrering för att fylla i en organisation i Azure Active Directory (Azure AD). Om du vill ta över ett domän namn från en ohanterad Azure AD-organisation kan du läsa [ta över en ohanterad katalog som administratör](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Varför ska jag använda självbetjänings registrering?

* Få kunder till tjänster som de vill ha snabbare
* Skapa e-postbaserade erbjudanden för en tjänst
* Skapa e-postbaserade registrerings flöden som snabbt tillåter användare att skapa identiteter med hjälp av sina lättanvända e-postalias för arbete
* En Azure AD-katalog som skapats via självbetjäning kan omvandlas till en hanterad katalog som kan användas för andra tjänster

## <a name="terms-and-definitions"></a>Villkor och definitioner

* **Självbetjänings registrering**: det här är den metod som används när en användare registrerar sig för en moln tjänst och har en identitet som skapas automatiskt för dem i Azure AD baserat på sin e-postdomän.
* **Ohanterad Azure AD-katalog**: det här är den katalog där identiteten skapas. En ohanterad katalog är en katalog som inte har någon global administratör.
* **E-postverifierad användare**: Detta är en typ av användar konto i Azure AD. En användare som har en identitet som skapats automatiskt när du har registrerat dig för ett självbetjänings erbjudande kallas en e-postverifierad användare. En e-postverifierad användare är en vanlig medlem i en katalog som är märkt med creationmethod = EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Hur gör jag för att inställningar för självbetjänings kontroll?

Administratörer har två självbetjänings kontroller idag. De kan styra om:

* Användare kan ansluta till katalogen via e-post
* Användare kan licensiera sig själva för program och tjänster

### <a name="how-can-i-control-these-capabilities"></a>Hur kan jag kontrol lera dessa funktioner?

En administratör kan konfigurera dessa funktioner med hjälp av följande Azure AD cmdlet Set-MsolCompanySettings-parametrar:

* **AllowEmailVerifiedUsers** styr huruvida en användare kan skapa eller ansluta till en katalog. Om du anger parametern till $false kan ingen e-postverifierad användare ansluta till katalogen.
* **AllowAdHocSubscriptions** styr möjligheten för användare att utföra självbetjänings registrering. Om du anger parametern till $false kan ingen användare utföra självbetjänings registrering.
  
AllowEmailVerifiedUsers och AllowAdHocSubscriptions är inställningar för hela katalogen som kan tillämpas på en hanterad eller ohanterad katalog. Här är ett exempel där:

* Du administrerar en katalog med en verifierad domän, till exempel contoso.com
* Du använder B2B-samarbete från en annan katalog för att bjuda in en användare som inte redan finns ( userdoesnotexist@contoso.com ) i arbets katalogen contoso.com
* Arbets katalogen har AllowEmailVerifiedUsers aktive rad

Om föregående villkor är uppfyllda skapas en medlems användare i arbets katalogen, och en B2B-gäst användare skapas i den bjudande katalogen.

Mer information om flödes-och PowerApps för utvärderings registrering finns i följande artiklar:

* [Hur förhindrar jag att mina befintliga användare börjar använda Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Vanliga frågor och svar om flöden i din organisation](/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Hur fungerar kontrollerna tillsammans?
Dessa två parametrar kan användas tillsammans för att definiera mer exakt kontroll över självbetjänings registrering. Följande kommando tillåter till exempel att användare utför självbetjänings registrering, men bara om dessa användare redan har ett konto i Azure AD (med andra ord kan användare som behöver ett e-postkonto som verifierats för att kunna skapa ett e-postkonto först inte utföra självbetjänings registrering):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

Följande flödes diagram förklarar de olika kombinationerna för dessa parametrar och de resulterande villkoren för katalogen och registreringen av självbetjäningen.

![flödes schema för självbetjänings registrerings kontroller](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Mer information och exempel på hur du använder dessa parametrar finns i [set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Nästa steg

* [Lägga till ett anpassat domännamn i Azure AD](../fundamentals/add-custom-domain.md)
* [Hur du installerar och konfigurerar Azure PowerShell](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Azure Cmdlet-referens](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Stäng ditt arbets-eller skol konto i en ohanterad katalog](users-close-account.md)