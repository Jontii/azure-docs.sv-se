---
title: inkludera fil
description: inkludera fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "77471572"
---
Om du vill att användarna ska uppmanas att ange en andra faktor innan de beviljas åtkomst, kan du konfigurera Azure Multi-Factor Authentication (MFA). Du kan konfigurera MFA per användare eller använda MFA via [villkorlig åtkomst](../articles/active-directory/conditional-access/overview.md).

* MFA per användare kan aktive ras utan ytterligare kostnad. När du aktiverar MFA per användare uppmanas användaren att ange en andra Factor Authentication mot alla program som är kopplade till Azure AD-klienten. Se [alternativ 1](#peruser) för steg.
* Villkorlig åtkomst ger en detaljerad kontroll över hur en andra faktor ska höjas. Den kan tillåta tilldelning av MFA till endast VPN och utesluta andra program som är kopplade till Azure AD-klienten. Se [Alternativ 2](#conditional) för steg.