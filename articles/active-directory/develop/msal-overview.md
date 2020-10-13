---
title: Läs mer om MSAL | Azure
titleSuffix: Microsoft identity platform
description: 'Microsoft Authentication Library (MSAL) gör det möjligt för programutvecklare att förvärva tokens för att anropa säkra webb-API: er. Dessa webb-API: er kan vara Microsoft Graph, andra Microsoft API: er, webb-API: er från tredje part eller ditt eget webb-API. MSAL stöder flera program arkitekturer och plattformar.'
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0c4da177644a1cdb648c00e8309c18031a905d7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825952"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Översikt över Microsoft Authentication Library (MSAL)
Med Microsoft Authentication Library (MSAL) kan utvecklare Hämta [tokens](developer-glossary.md#security-token) från slut punkten för Microsoft Identity Platform för att få åtkomst till säkra webb-API: er. Dessa webb-API: er kan vara Microsoft Graph, andra Microsoft API: er, webb-API: er från tredje part eller ditt eget webb-API. MSAL är tillgängligt för .NET, Java Script, Java, python, Android och iOS, som stöder många olika program arkitekturer och plattformar.

MSAL ger dig många sätt att hämta tokens med ett konsekvent API för ett antal plattformar. Användning av MSAL ger följande fördelar:

* Du behöver inte direkt använda OAuth-bibliotek eller kod mot protokollet i ditt program.
* Hämtar token för en användares räkning eller på uppdrag av ett program (om det är tillämpligt på plattformen).
* Underhåller en token-cache och uppdaterar tokens åt dig när de är nära att gå ut. Du behöver inte hantera förfallo datum för token.
* Hjälper dig att ange vilken mål grupp du vill att ditt program ska logga in på (din organisation, flera organisationer, arbets-och skol-och Microsoft personal-konton, sociala identiteter med Azure AD B2C, användare i suveräna och nationella moln).
* Hjälper dig att konfigurera ditt program från konfigurationsfiler.
* Hjälper dig att felsöka din app genom att exponera åtgärds bara undantag, loggning och telemetri.

## <a name="application-types-and-scenarios"></a>Programtyper och scenarier
Med hjälp av MSAL kan du hämta en token från ett antal program typer: webb program, webb-API: er, appar med enkel sida (Java Script), mobila program och inbyggda program, samt daemon och program på Server sidan.

MSAL kan användas i många program scenarier, inklusive följande:

* [Program med en sida (Java Script)](scenario-spa-overview.md)
* [Webb program som loggar in användare](scenario-web-app-sign-user-overview.md)
* [Webb program som loggar in en användare och anropar ett webb-API för användarens räkning](scenario-web-app-call-api-overview.md)
* [Skydda ett webb-API så att endast autentiserade användare kan komma åt det](scenario-protected-web-api-overview.md)
* [Webb-API som anropar ett annat underordnat webb-API å den inloggade användarens vägnar](scenario-web-api-call-api-overview.md)
* [Skriv bords program som anropar ett webb-API å den inloggade användarens vägnar](scenario-desktop-overview.md)
* [Mobil program som anropar ett webb-API åt användaren som är inloggad interaktivt](scenario-mobile-overview.md).
* [Desktop/Service daemon-program som anropar webb-API: et åt sig själv](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Språk och ramverk

| Bibliotek | Plattformar och ramverk som stöds|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, Universell Windows-plattform|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| JavaScript/TypeScript-ramverk som AngularJS, Ember.js eller Durandal.js|
| [MSAL för Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL för iOS och macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS och macOS|
| [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows, macOS, Linux|
| [MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows, macOS, Linux|

## <a name="differences-between-adal-and-msal"></a>Skillnader mellan ADAL och MSAL

Active Directory-autentiseringsbibliotek (ADAL) integreras med slut punkten för Azure AD för utvecklare (v 1.0), där MSAL integreras med slut punkten för Microsoft Identity Platform (v 2.0). V 1.0-slutpunkten stöder arbets konton, men inte personliga konton. V 2.0-slutpunkten är arbetskonton av Microsofts personliga konton och arbets konton i ett enda autentiseringspaket. Med MSAL kan du dessutom även hämta autentiseringar för Azure AD B2C.

Mer detaljerad information finns i [migrera till MSAL.net från ADAL.net](msal-net-migration.md) och [migrera till MSAL.js från ADAL.js](msal-compare-msal-js-and-adal-js.md).
