---
title: Bygg en app med en sida som anropar ett webb-API – Microsoft Identity Platform | Azure
description: Lär dig hur du skapar ett program med en enda sida som anropar ett webb-API
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 5b70b109f43e80fc3ec68f52aef2dba6823033bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "80882139"
---
# <a name="single-page-application-call-a-web-api"></a>Program med en sida: anropa ett webb-API

Vi rekommenderar att du anropar `acquireTokenSilent` metoden för att hämta eller förnya en åtkomsttoken innan du anropar ett webb-API. När du har en token kan du anropa ett skyddat webb-API.

## <a name="call-a-web-api"></a>Anropa en webb-API

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Använd den hämtade åtkomsttoken som en innehavare i en HTTP-begäran för att anropa alla webb-API: er, till exempel Microsoft Graph-API. Exempel:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL vinkel omslutningen drar nytta av HTTP-spärren för att automatiskt hämta åtkomsttoken och koppla dem till HTTP-begäranden till API: er. Mer information finns i [Hämta en token för att anropa ett API](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flytta till produktion](scenario-spa-production.md)
