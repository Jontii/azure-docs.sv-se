---
title: 'Registrera daemon-appar som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du skapar en daemon-app som anropar webb-API: er – app-registrering'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 508101ad615dd96559b1c68a61be7c08772545db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "80885488"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Daemon-app som anropar webb-API: er – app-registrering

För ett daemon-program måste du veta vad du behöver veta när du registrerar appen.

## <a name="supported-account-types"></a>Kontotyper som stöds

Daemon-program ser bara bra in i Azure AD-klienter. När du skapar programmet måste du välja något av följande alternativ:

- **Konton i den här organisations katalogen**. Det här är det vanligaste alternativet eftersom daemon-program vanligt vis skrivs av verksamhetsspecifika utvecklare.
- **Konton i valfri organisations katalog**. Du väljer själv om du är en ISV som tillhandahåller ett verktygs verktyg för dina kunder. Du behöver dina kunders klient administratörer för att godkänna den.

## <a name="authentication---no-reply-uri-needed"></a>Autentisering – ingen svars-URI behövs

Om det konfidentiella klient programmet *bara* använder flödet för klientautentiseringsuppgifter behöver inte svars-URI: n registreras. Det behövs inte för program konfigurationen eller konstruktionen. Flödet för klientens autentiseringsuppgifter använder det inte.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API-behörigheter – app-behörigheter och administratörs medgivande

Ett daemon-program kan endast begära program behörigheter till API: er (inte delegerade behörigheter). På sidan **API-behörigheter** för program registreringen när du har valt **Lägg till en behörighet** och valt API-serien väljer du **program behörigheter**och väljer sedan behörigheter.

![App-behörigheter och administratörs medgivande](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> Det webb-API som du vill anropa måste definiera *program behörigheter (app roles)*, inte delegerade behörigheter. Mer information om hur du exponerar ett sådant API finns i [Protected Web API: app Registration – när ditt webb-API anropas av en daemon-app](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).

Daemon-program kräver att en innehavaradministratör samtycker till programmet som anropar webb-API: et. Klient organisationens administratörer ger detta medgivande på samma **API-behörighet** genom att välja **bevilja administratörs medgivande till *vår organisation* **

Om du är en ISV-byggnad av ett program för flera innehavare bör du läsa avsnittet om [distribution av daemon-appar för flera innehavare](scenario-daemon-production.md#deployment---multitenant-daemon-apps).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Daemon App – app Code Configuration](./scenario-daemon-app-configuration.md)
