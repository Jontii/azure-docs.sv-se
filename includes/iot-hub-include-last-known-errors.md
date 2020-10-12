---
title: inkludera fil
description: inkludera fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d03579f704879bd8d012bb0bb326659d1f778dee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793333"
---
[Hämta slut punkts hälsan](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) i REST API visar slut punkternas hälso status och det senaste kända felet för att identifiera orsaken till att en slut punkt inte är felfri. Tabellen nedan visar de vanligaste felen.

|Senaste kända fel|Beskrivning/när det inträffar|Möjlig minskning|
|-----|-----|-----|
|Tillfälliga|Ett tillfälligt fel har inträffat och IoT Hub försöker utföra åtgärden igen.|Observera [diagnostiska loggar](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health#routes)för vägar.|
|InternalError|Ett fel uppstod när ett meddelande levererades till en slut punkt.|Det här är ett internt undantag, men Observera även [loggar för diagnostikloggar](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health#routes).|
|Behörighet saknas|IoT Hub har inte behörighet att skicka meddelanden till den angivna slut punkten.|Kontrol lera att anslutnings strängen är aktuell för slut punkten. Om den har ändrats bör du överväga att uppdatera IoT Hub. Om den hanterade identiteten används för slut punkten kontrollerar du att IoT Hub-huvudobjektet har de behörigheter som krävs för målet.|
|Begränsas|IoT Hub begränsas när meddelanden skrivs till slut punkten.|Granska begränsningarna för den påverkade slut punkten. Ändra konfigurationerna för slut punkten för att skala upp vid behov.|
|Tidsgräns|Åtgärds-timeout.|Försök att utföra åtgärden igen.|
|Hittades inte|Mål resursen finns inte.|Se till att mål resursen finns.|
|Behållaren hittades inte|Lagrings containern finns inte.|Se till att lagrings containern finns.|
|Container inaktive rad|Lagrings containern är inaktive rad.|Se till att lagrings behållaren är aktive rad.|
|MaxMessageSizeExceeded|Meddelanderoutning har en storleks gräns på 256 kB. den meddelande storlek som dirigeras överskrider den här gränsen.|Kontrol lera om meddelande storleken kan minskas med färre program egenskaper eller färre meddelande.|
|PartitioningAndDuplicateDetectionNotSupported|Ingen dubblettidentifiering har Aktiver ATS för Service Bus.|Inaktivera dubblettidentifiering från Service Bus eller Överväg att använda en entitet utan dubblettidentifiering.|
|SessionfulEntityNotSupported|Det är inte säkert att sessioner har Aktiver ATS för Service Bus.|Inaktivera sessionen från Service Bus eller Överväg att använda en entitet utan sessioner.|
|NoMatchingSubscriptionsForMessage|Det finns ingen prenumeration på att skriva meddelande i Service Bus-ämnet.|Skapa en prenumeration för IoT Hub meddelanden som ska vidarebefordras till.|
|EndpointExternallyDisabled|Slut punkten är inte i ett aktivt tillstånd, så IoT Hub kan skicka meddelanden till den.|Aktivera slut punkten för att återställa till aktivt tillstånd.|
|DeviceMaximumQueueDepthExceeded|Gränsen för Service Bus-storlek har nåtts.|Överväg att ta bort meddelanden från mål Event Hubs så att nya meddelanden kan matas in i Event Hubs.|