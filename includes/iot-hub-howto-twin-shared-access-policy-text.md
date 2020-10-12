---
title: inkludera fil
description: inkludera fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "70050436"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

I den här artikeln skapar du en backend-tjänst som lägger till önskade egenskaper till en enhet och skickar sedan en fråga till identitets registret för att hitta alla enheter med rapporterade egenskaper som har uppdaterats därefter. Tjänsten behöver **tjänstens Connect-** behörighet för att ändra önskade egenskaper för en enhet, och den måste ha **Läs** behörighet för registret för att fråga identitets registret. Det finns ingen standard princip för delad åtkomst som bara innehåller dessa två behörigheter, så du måste skapa en.
