---
title: inkludera fil
description: inkludera fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3a5747912a18b8406cabd03c9823e3f6fe6898a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "72808878"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Du kommer att dirigera meddelanden till olika resurser baserat på egenskaper som är anslutna till meddelandet av den simulerade enheten. Meddelanden som inte är dirigeringsanpassade skickas till standardslutpunkten (meddelanden/händelser). I nästa självstudie skickar du meddelanden till IoT Hub och kan se att de dirigeras till olika destinationer.

|Värde |Resultat|
|------|------|
|level="storage" |Skriv till Azure Storage.|
|level="critical" |Skriv till en Service Bus-kö. En logikapp hämtar meddelandet från kön och använder Office 365 för att skicka meddelandet via e-post.|
|standard |Visa dessa data med Power BI.|

Det första steget är att konfigurera slut punkten som data ska dirigeras till. Det andra steget är att konfigurera den meddelande väg som använder den slut punkten. När du har konfigurerat routningen kan du Visa slut punkterna och meddelande vägarna i portalen.