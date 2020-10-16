---
title: Lösenordsbaserad autentisering med Azure Active Directory
description: Arkitektur vägledning för att uppnå detta autentiserings mönster
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f05bf8f5f7ec4907c2cd61ff48e3438dfa1e097a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114362"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>Lösenordsbaserad autentisering med Azure Active Directory

Password-baserad enkel Sign-On (SSO) använder den befintliga autentiseringsprocessen för programmet. När du aktiverar lösenordsbaserad SSO samlar Azure Active Directory (Azure AD), krypteras och lagrar användarautentiseringsuppgifter på ett säkert sätt i katalogen. Azure AD tillhandahåller användar namn och lösen ord för programmet när användaren försöker logga in.

Välj lösenordsbaserad SSO när ett program autentiseras med ett användar namn och lösen ord i stället för åtkomst-tokens och-rubriker. Lösenordsbaserad SSO stöder alla molnbaserade program som har en HTML-baserad inloggnings sida. 

## <a name="use-when"></a>Använd när

Du måste skydda med förautentisering och tillhandahålla SSO genom lösen ords valv till Web Apps.

![arkitektur diagram](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>System komponenter

* **Användare**: ansluter till ett utformat program från antingen Mina appar eller genom att direkt besöka webbplatsen. 

* **Webbläsare**: komponenten som användaren interagerar med för att få åtkomst till den externa URL: en för programmet. Användaren får åtkomst till det formulärbaserade programmet via tillägget för appar. 

* Mina **appar-tillägg**: identifierar det konfigurerade lösenordsbaserade SSO-programmet och skickar autentiseringsuppgifterna till inloggnings formuläret. Tillägget för Mina appar är installerat i webbläsaren. 

* **Azure AD**: autentiserar användaren.

## <a name="implement-password-based-sso-with-azure-ad"></a>Implementera lösenordsbaserad SSO med Azure AD

* [Vad är lösen ords baserat SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) 

* [Konfigurera lösenordsbaserad SSO för moln program ](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications)

* [Konfigurera lösenordsbaserad SSO för lokala program med Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting)

 
