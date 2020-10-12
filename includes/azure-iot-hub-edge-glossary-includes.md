---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: 0b478775aa572ce89f3dcdc6f28ee0418e818d99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91376625"
---
## <a name="automatic-device-management-in-azure-iot-hub"></a>Automatisk enhets hantering i Azure IoT Hub
Automatisk enhets hantering i Azure IoT Hub automatiserar många av de repetitiva och komplexa uppgifterna i hanteringen av stora enhets flottor över hela livs cykeln. Med automatisk enhets hantering kan du rikta en uppsättning enheter baserat på deras egenskaper, definiera en önskad konfiguration och låta IoT Hub uppdatera enheter när de kommer till omfånget.  Består av [Automatisk enhets konfiguration](../articles/iot-hub/iot-hub-auto-device-config.md) och [IoT Edge automatiska distributioner](../articles/iot-edge/how-to-deploy-at-scale.md).

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge möjliggör moln driven distribution av Azure-tjänster och lösnings kod till lokala enheter. IoT Edge enheter kan samla in data från andra enheter för att utföra data behandling och analys innan data skickas till molnet. Mer information finns i [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>IoT Edge agent
Den del av IoT Edge runtime som ansvarar för att distribuera och övervaka moduler.

## <a name="iot-edge-device"></a>IoT Edge-enhet
IoT Edge runtime är installerat på IoT Edge enheter och flaggas som **IoT Edge enhet** i enhets informationen. Lär dig hur du [distribuerar Azure IoT Edge på en simulerad enhet i Linux – för hands version](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>IoT Edge automatisk distribution
En IoT Edge automatisk distribution konfigurerar en uppsättning IoT Edge moduler som ska köras på en mål uppsättning IoT Edge enheter. Varje distribution säkerställer kontinuerligt att alla enheter som matchar dess mål villkor kör den angivna uppsättningen moduler, även när nya enheter skapas eller ändras för att matcha mål villkoret. Varje IoT Edge enhet tar bara emot den högsta prioritets distributionen vars mål villkor är uppfyllda. Läs mer om [IoT Edge automatisk distribution](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Distributions manifest för IoT Edge
Ett JSON-dokument som innehåller den information som ska kopieras i en eller flera IoT Edge enheters modul dubbla (s) för att distribuera en uppsättning moduler, vägar och önskade egenskaper för associerade moduler.

## <a name="iot-edge-gateway-device"></a>IoT Edge gateway-enhet
En IoT Edge enhet med underordnad enhet. Den underordnade enheten kan vara antingen IoT Edge eller inte IoT Edge enhet.

## <a name="iot-edge-hub"></a>IoT Edge hubb
Den del av IoT Edge runtime som är ansvarig för att modulerna ska kunna kommunicera, strömmas (mot IoT Hub) och underordnad (från IoT Hub) kommunikation. 

## <a name="iot-edge-leaf-device"></a>IoT Edge löv enhet
En IoT Edge enhet utan någon underordnad enhet. 

## <a name="iot-edge-module"></a>IoT Edge modul
En IoT Edge modul är en Docker-behållare som du kan distribuera till IoT Edge enheter. Den utför en speciell uppgift, t. ex. inmatning av ett meddelande från en enhet, omvandling av ett meddelande eller att skicka ett meddelande till en IoT-hubb. Den kommunicerar med andra moduler och skickar data till IoT Edge Runtime. [Lär dig mer om krav och verktyg för att utveckla IoT Edge moduler](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>IoT Edge modulens identitet
En post i IoT Hub modul identitets registret som innehåller information om de existenss-och säkerhets referenser som ska användas av en modul för att autentisera med en Edge Hub eller IoT Hub.

## <a name="iot-edge-module-image"></a>Avbildning av IoT Edge modul
Docker-avbildningen som används av IoT Edge runtime för att instansiera modul instanser.

## <a name="iot-edge-module-twin"></a>IoT Edge modul, dubbla
Ett JSON-dokument sparat i IoT Hub som lagrar Tillståndsinformation för en modul-instans.

## <a name="iot-edge-priority"></a>IoT Edge prioritet
När två IoT Edge distributioner riktar sig till samma enhet tillämpas distributionen med högre prioritet. Om två distributioner har samma prioritet tillämpas distributionen med det senare skapande datumet. Läs mer om [prioritet](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>IoT Edge-körning
IoT Edge runtime innehåller allt som Microsoft distribuerar för att installeras på en IoT Edge enhet. Den innehåller Edge-agent, Edge Hub och IoT Edge Security daemon.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge att ange moduler till en enda enhet
En åtgärd som kopierar innehållet i ett IoT Edge-manifest på en enhets modul, dubbel. Det underliggande API: t är en allmän "tillämpa konfiguration" som bara tar ett IoT Edge manifest som inmatade.

## <a name="iot-edge-target-condition"></a>IoT Edge mål villkor
I en IoT Edge distribution är mål villkor alla booleska villkor på enhetens dubbla taggar för att välja mål enheter för distributionen, till exempel **tagg. Environment = Prod**. Mål villkoret utvärderas kontinuerligt för att omfatta alla nya enheter som uppfyller kraven eller tar bort enheter som inte längre fungerar. Läs mer om [mål villkor](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)