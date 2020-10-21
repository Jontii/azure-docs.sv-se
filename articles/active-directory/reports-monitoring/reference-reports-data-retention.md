---
title: Hur länge rapporterar Azure AD Store-data? | Microsoft Docs
description: Lär dig hur länge Azure lagrar de olika typerna av rapporterings data.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5deb84cdc2dee61474c9f2bd1f93311d89f8918
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92308887"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Hur länge rapporterar Azure AD Store-data?


I den här artikeln får du lära dig om data lagrings principer för de olika aktivitets rapporterna i Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>När börjar Azure AD samla in data?

| Azure AD-utgåva | Samlings start |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | När du registrerar dig för en prenumeration |
| Azure AD Kostnadsfri| Första gången du öppnar [Azure Active Directory bladet](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) eller använder [rapporterings-API: erna](./overview-reports.md)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>När är aktivitets data tillgängliga i Azure Portal?

- **Omedelbart** – om du redan har arbetat med rapporter i Azure Portal.
- **Inom 2 timmar** – om du inte har aktiverat rapportering i Azure Portal.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Hur snart kan jag se aktivitets data när de har skaffat en Premium-licens?

Om du redan har aktivitets data med din kostnads fria licens kan du se den direkt vid uppgraderingen. Om du inte har några data, tar det en eller två dagar innan data visas i rapporterna efter att du har uppgraderat till en Premium-licens.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Kan jag se den senaste månadens data efter att ha skaffat en Azure AD Premium-licens?

Om du nyligen har bytt till en Premium-version (inklusive en utvärderings version) kan du se data upp till sju dagar från början. När data ackumuleras kan du se data för de senaste 30 dagarna.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>När börjar Azure AD samla in säkerhets signal data?  

För säkerhets signaler startar samlings processen när du väljer att använda **identitets skydds centret**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Hur länge lagrar Azure AD data?

**Aktivitetsrapporter**    

| Rapport                 | Azure AD Kostnadsfri | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Granskningsloggar             | 7 dagar        | 30 dagar             | 30 dagar             |
| Inloggningar               | 7 dagar        | 30 dagar             | 30 dagar             |
| Användning av Azure MFA        | 30 dagar       | 30 dagar             | 30 dagar             |

Du kan behålla gransknings-och inloggnings aktivitets data längre än den standard kvarhållningsperiod som beskrivs ovan genom att dirigera den till ett Azure Storage-konto med hjälp av Azure Monitor. Mer information finns i [arkivera Azure AD-loggar till ett Azure Storage-konto](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Säkerhets signaler**

| Rapport         | Azure AD Kostnadsfri | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Användare i riskzonen  | 7 dagar        | 30 dagar             | 90 dagar             |
| Riskfyllda inloggningar | 7 dagar        | 30 dagar             | 90 dagar             |

---