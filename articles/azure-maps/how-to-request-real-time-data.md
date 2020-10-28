---
title: Begär offentlig överförings data för Real tid | Microsoft Azure Maps
description: Läs om hur du begär data från offentliga data i real tid, t. ex. införsel i ett överförings stopp. Se hur du använder tjänsten Azure Maps Mobility för detta ändamål.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e6f6d0738cb1673b752e35761a112f2ca22a409e
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895723"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>Begär offentlig överförings information i real tid med tjänsten Azure Maps Mobility

Den här artikeln visar hur du använder Azure Maps [Mobility Service](/rest/api/maps/mobility) för att begära offentliga data i real tid.

I den här artikeln får du lära dig hur du begär nästa mottagna real tid för alla rader som kommer vid ett angivet stopp

## <a name="prerequisites"></a>Förutsättningar

Du måste först ha ett Azure Maps konto och en prenumerations nyckel för att kunna anropa de Azure Maps offentliga API: erna för överföring. Om du vill ha mer information följer du instruktionerna i [skapa ett konto](quick-demo-map-app.md#create-an-azure-maps-account) för att skapa ett Azure Maps-konto. Följ stegen i [Hämta primär nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account) för att hämta den primära nyckeln för ditt konto. Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](./how-to-manage-authentication.md).

I den här artikeln används [Postman-appen](https://www.getpostman.com/apps) för att bygga rest-anrop. Du kan använda valfri API utvecklings miljö som du föredrar.

## <a name="request-real-time-arrivals-for-a-stop"></a>Begär real tids mottagningar för ett stopp

För att begära ingångs data i real tid för en viss offentlig överförings stopp, måste du göra en begäran till [real tids ingångs-API: t](/rest/api/maps/mobility/getrealtimearrivalspreview) för [tjänsten Azure Maps Mobility](/rest/api/maps/mobility). Du behöver **metroID** och **stopID** för att slutföra begäran. Mer information om hur du begär dessa parametrar finns i vår guide om hur du [begär offentliga överförings vägar](./how-to-request-transit-data.md).

Vi använder "522" som vårt tunnelbane-ID, som är Metro-ID: t för "Seattle – Tacoma – Bellevue, WA"-ytan. Använd "522---2060603" som stopp-ID: t det här buss steget är "ne 24 st & 162nd Ave Ne, Bellevue WA". Om du vill begära nästa fem real tids mottagande data, för alla nästa Live-införsel i detta steg, slutför du följande steg:

1. Öppna Postman-appen och skapa en samling där du kan lagra begär Anden. Längst upp i Postman-appen väljer du **nytt** . I fönstret **Skapa nytt** väljer du **samling** .  Namnge samlingen och välj knappen **skapa** .

2. Välj **nytt** om du vill skapa en begäran. I fönstret **Skapa nytt** väljer du **begäran** . Ange ett **namn** för begäran. Välj den samling som du skapade i föregående steg, som den plats där du vill spara begäran. Välj sedan **Spara** .

    ![Skapa en begäran i Postman](./media/how-to-request-transit-data/postman-new.png)

3. Välj metoden **Hämta** http på fliken Builder och ange följande URL för att skapa en get-begäran. Ersätt `{subscription-key}` med Azure Maps primär nyckel.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Efter en lyckad begäran får du följande svar.  Observera att parametern "scheduleType" definierar om den uppskattade införsel tiden baseras på real tids data eller statiska data.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du begär överförings data med mobilitets tjänsten:

> [!div class="nextstepaction"]
> [Så här begär du överförings data](how-to-request-transit-data.md)

Utforska dokumentationen för Azure Maps Mobility Service API:

> [!div class="nextstepaction"]
> [API-dokumentation för Mobility Service](/rest/api/maps/mobility)