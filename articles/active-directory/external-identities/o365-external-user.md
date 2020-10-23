---
title: Microsoft 365 extern delning och B2B-samarbete – Azure AD
description: Beskriver hur du delar resurser med externa partner med hjälp av Microsoft 365 och Azure Active Directory B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/24/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0950c92b5a7bb1e782537aa46acad2e949e565c7
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92441801"
---
# <a name="microsoft-365-external-sharing-and-azure-active-directory-azure-ad-b2b-collaboration"></a>Microsoft 365 extern delning och Azure Active Directory (Azure AD) B2B-samarbete

I både Azure AD B2B-samarbete och Microsoft 365 extern delning (OneDrive, SharePoint Online, enhetliga grupper osv.), autentiseras externa användare med hjälp av Azure AD B2B.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Hur skiljer sig Azure AD B2B från extern delning i SharePoint Online?

OneDrive/SharePoint Online har en separat Inbjudnings hanterare. Stöd för extern delning i OneDrive/SharePoint Online har startats innan Azure AD utvecklade sitt stöd. Över tid har den externa delningen av OneDrive/SharePoint Online flera funktioner och många miljoner användare som använder produktens inbyggda delnings mönster. Det finns dock några diskreta skillnader mellan hur extern delning i OneDrive/SharePoint Online fungerar och hur Azure AD B2B-samarbete fungerar. Du kan läsa mer om extern delning av OneDrive/SharePoint Online i [extern delnings översikt](/sharepoint/external-sharing-overview). Processen skiljer sig i allmänhet från Azure AD B2B på följande sätt:

- OneDrive/SharePoint Online lägger till användare i katalogen efter att användarna har löst deras inbjudningar. Därför visas inte användaren i Azure AD-portalen före inlösen. Om en annan plats bjuder in en användare under tiden skapas en ny inbjudan. Men när du använder Azure AD B2B-samarbete läggs användare omedelbart till på inbjudan så att de visas överallt.

- Inlösnings upplevelsen i OneDrive/SharePoint Online ser annorlunda ut jämfört med Azure AD B2B-samarbete. När en användare har löst en inbjudan ser upplevelsen likadan ut.

- Azure AD B2B-samarbete inbjudna användare kan plockas från dialog rutorna OneDrive/SharePoint Online-delning. OneDrive/SharePoint Online inbjudna användare visas också i Azure AD när de har löst in sina inbjudningar.

- Licensierings kraven skiljer sig åt. För varje betald Azure AD-licens kan du låta upp till fem gäst användare få åtkomst till dina betal funktioner i Azure AD. Läs mer om licensiering i [Azure AD B2B-licensiering](./external-identities-pricing.md) och ["Vad är en extern användare?" i Översikt över SharePoint Online-delning](/sharepoint/external-sharing-overview#what-happens-when-users-share).

Om du vill hantera extern delning i OneDrive/SharePoint Online med Azure AD B2B-samarbete ställer du in inställningen för extern delning i OneDrive/SharePoint Online så att **den bara tillåter delning med de externa användare som redan finns i din organisations katalog**. Användare kan gå till externt delade platser och välja från externa medarbetare som administratören har lagt till. Administratören kan lägga till externa medarbetare via API: er för inbjudan till B2B-samarbete.


![Inställningen för extern OneDrive/SharePoint-delning](media/o365-external-user/odsp-sharing-setting.png)

När du har aktiverat extern delning är möjligheten att söka efter befintliga gäst användare i användar väljaren för SharePoint Online (SPO) inaktive rad som standard för att matcha äldre beteende.

Du kan aktivera den här funktionen genom att använda inställningen "ShowPeoplePickerSuggestionsForGuestUsers" på klient-och webbplats samlings nivå. Du kan ställa in funktionen med hjälp av Set-SPOTenant-och Set-SPOSite-cmdlet: ar, vilket gör det möjligt för medlemmar att söka igenom alla befintliga gäst användare i katalogen. Ändringar i klient omfånget påverkar inte redan etablerade SPO-webbplatser.

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Lägga till en B2B-samarbets användare till en roll](add-guest-to-role.md)
* [Delegera B2B-samarbets inbjudningar](delegate-invitations.md)
* [Dynamiska grupper och B2B-samarbete](use-dynamic-groups.md)
* [Felsöka Azure Active Directory B2B-samarbete](troubleshoot.md)