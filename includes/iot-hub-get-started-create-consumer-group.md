---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "66249049"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Lägga till en konsument grupp i IoT Hub

[Konsument grupper](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) tillhandahåller oberoende vyer i händelse strömmen som gör det möjligt för appar och Azure-tjänster att oberoende använda data från samma Event Hub-slutpunkt. I det här avsnittet lägger du till en konsument grupp i IoT Hub: s inbyggda slut punkt som används senare i den här självstudien för att hämta data från slut punkten.

Följ dessa steg om du vill lägga till en konsument grupp i IoT Hub:

1. Öppna IoT-hubben på [Azure-portalen](https://portal.azure.com/).

2. I den vänstra rutan väljer du **inbyggda slut punkter**, väljer **händelser** i den högra rutan och anger ett namn under **konsument grupper**. Välj **Spara**.

   ![Skapa en konsument grupp i din IoT Hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
