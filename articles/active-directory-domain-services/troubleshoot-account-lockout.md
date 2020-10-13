---
title: Felsöka konto utelåsning i Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du felsöker vanliga problem som gör att användar konton blir utelåsta i Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 4a5ae321a4a97df5b5fa91bb239589c76c6601fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86039763"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Felsöka problem med konto utelåsning med en Azure Active Directory Domain Services hanterad domän

För att förhindra upprepad skadlig inloggning försöker en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän låsa konton efter ett definierat tröskelvärde. Den här konto utelåsning kan också inträffa av en olycka utan en inloggnings attack. Om en användare till exempel ofta anger fel lösen ord eller om en tjänst försöker använda ett gammalt lösen ord, blir kontot utelåst.

Den här fel söknings artikeln beskriver hur konto utelåsning sker och hur du kan konfigurera beteendet och hur du granskar säkerhets granskningar för att felsöka utelåsnings händelser.

## <a name="what-is-an-account-lockout"></a>Vad är ett konto utelåsning?

Ett användar konto i en Azure AD DS-hanterad domän är utelåst när ett definierat tröskelvärde för misslyckade inloggnings försök har uppfyllts. Detta konto utelåsning är utformat för att skydda dig från upprepade återkrävda inloggnings försök som kan tyda på ett automatiskt digitalt angrepp.

**Om det finns fem dåliga lösen ords försök på 2 minuter låses kontot som standard i 30 minuter.**

Standard tröskelvärdena för konto utelåsning konfigureras med detaljerade lösen ords principer. Om du har en speciell uppsättning krav kan du åsidosätta de här tröskelvärdena för standard konto utelåsning. Men vi rekommenderar inte att du ökar tröskelvärdena för att försöka minska antalet utelåsta konton. Felsök källan för konto utelåsnings beteendet först.

### <a name="fine-grained-password-policy"></a>Detaljerad lösen ords princip

Med detaljerade lösen ords principer (FGPP) kan du använda vissa begränsningar för lösen ord och konto utelåsnings principer för olika användare i en domän. FGPP påverkar endast användare inom en hanterad domän. Moln användare och domän användare som synkroniseras till den hanterade domänen från Azure AD påverkas endast av lösen ords principerna i den hanterade domänen. Deras konton i Azure AD eller i en lokal katalog påverkas inte.

Principer distribueras via grupp associationen i den hanterade domänen och eventuella ändringar som du gör tillämpas vid nästa användar inloggning. Att ändra principen låser inte upp ett användar konto som redan är låst.

Mer information om detaljerade lösen ords principer och skillnaderna mellan användare som skapats direkt i Azure AD DS jämfört med att synkroniseras i från Azure AD finns i [Konfigurera principer för lösen ord och konto utelåsning][configure-fgpp].

## <a name="common-account-lockout-reasons"></a>Vanliga orsaker till konto utelåsning

De vanligaste orsakerna till att ett konto har låsts, utan några skadliga avsikter eller faktorer, innehåller följande scenarier:

* **Användaren låste sig själva.**
    * Har användaren fortsatt att använda ett tidigare lösen ord efter en nyligen ändrad lösen ords ändring? Standard principen för konto utelåsning för fem misslyckade försök på 2 minuter kan orsakas av att användaren försöker med ett gammalt lösen ord av misstag.
* **Det finns ett program eller en tjänst med ett gammalt lösen ord.**
    * Om ett konto används av program eller tjänster kan dessa resurser upprepade gånger försöka logga in med ett gammalt lösen ord. Det här beteendet gör att kontot blir utelåst.
    * Försök att minimera konto användningen i flera olika program eller tjänster och anteckna var autentiseringsuppgifterna ska användas. Om ett konto lösen ord ändras uppdaterar du de associerade programmen eller tjänsterna efter detta.
* **Lösen ordet har ändrats i en annan miljö och det nya lösen ordet har ännu inte synkroniserats.**
    * Om ett konto lösen ord ändras utanför den hanterade domänen, t. ex. i en lokal AD DS-miljö, kan det ta några minuter innan lösen ords ändringen synkroniseras via Azure AD och till den hanterade domänen.
    * En användare som försöker logga in på en resurs i den hanterade domänen innan synkroniseringen av lösen ord har slutförts orsakar att deras konto blir utelåst.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Felsöka konto utelåsning med säkerhets granskningar

Om du vill felsöka när konto utelåsnings händelser inträffar och var de kommer från [aktiverar du säkerhets granskningar för Azure AD DS][security-audit-events]. Gransknings händelser samlas bara in från den tidpunkt då du aktiverar funktionen. Vi rekommenderar att du aktiverar säkerhets granskningar *innan* det uppstår ett problem med konto utelåsning för fel sökning. Om ett användar konto ofta har problem med utelåsning kan du aktivera säkerhets granskningar som är klara för nästa gång situationen inträffar.

När du har aktiverat säkerhets granskning visar följande exempel frågor hur du granskar *konto Utelåsnings händelser*, kod *4740*.

Visa alla konto utelåsnings händelser under de senaste sju dagarna:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Visa alla konto utelåsnings händelser under de senaste sju dagarna för kontot med namnet *driley*.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Visa alla konto utelåsnings händelser mellan 26 juni 2020 kl. 09:00 den 1 juli 2020 midnatt, sorteras stigande efter datum och tid:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-26 09:00) and TimeGenerated <= datetime(2020-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>Nästa steg

Mer information om detaljerade lösen ords principer för att justera konto utelåsnings tröskelvärden finns i [Konfigurera principer för lösen ord och konto utelåsning][configure-fgpp].

Om du fortfarande har problem med att ansluta den virtuella datorn till den hanterade domänen kan du [hitta hjälp och öppna ett support ärende för Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
