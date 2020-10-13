---
title: Reetablera enheter i Azure IoT Hub Device Provisioning Service
description: Lär dig hur du reetablerar enheter med din DPS-instans (Device Provisioning service) och varför du kan behöva göra detta.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e5cc5b557aa4dff793f7e87093eeb65028da4f8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839800"
---
# <a name="how-to-reprovision-devices"></a>Så här etablerar du om enheter

Under livs cykeln för en IoT-lösning är det vanligt att flytta enheter mellan IoT-hubbar. Orsakerna till den här flytten kan innefatta följande scenarier:

* **Geolokalisering**: när en enhet flyttas mellan platser förbättras nätverks fördröjningen genom att enheten migreras till en IoT-hubb närmare varje plats.

* **Flera innehavare**: en enhet kan användas inom samma IoT-lösning, men omtilldelas eller lånas ut till en ny kund eller kunds webbplats. Den nya kunden kan servas med hjälp av en annan IoT-hubb.

* **Lösnings ändring**: en enhet kan flyttas till en ny eller uppdaterad IoT-lösning. Den här omtilldelningen kan kräva att enheten kommunicerar med en ny IoT-hubb som är ansluten till andra server dels komponenter. 

* **Karantän**: liknar en lösnings förändring. En enhet som inte fungerar, komprometteras eller är inaktuell kan omtilldelas till en IoT-hubb där allt det kan göra är att uppdatera och se till att kompatibiliteten fungerar igen. När enheten fungerar korrekt migreras den tillbaka till huvud navet.

Mer detaljerad information om hur du reetablering finns i [IoT Hub metoder för att etablera enheter](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Konfigurera principen för registrerings tilldelning

Allokeringsregeln avgör hur enheter som är kopplade till registreringen ska allokeras eller tilldelas till en IoT-hubb när den har etablerats.

Följande steg konfigurerar principen för en enhets registrering:

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din enhets etablerings tjänst instans.

2. Klicka på **Hantera registreringar**och klicka på den registrerings grupp eller enskild registrering som du vill konfigurera för att etablera om. 

3. Under **Välj hur du vill tilldela enheter till hubbar väljer du**någon av följande allokeringsregler:

    * **Lägsta latens**: den här principen tilldelar enheter till den länkade IoT Hub som leder till den lägsta latens kommunikationen mellan enhet och IoT Hub. Med det här alternativet kan enheten kommunicera med den närmaste IoT-hubben baserat på plats. 
    
    * **Jämt viktad distribution**: den här principen distribuerar enheter över de länkade IoT-hubbarna baserat på den tilldelnings vikt som tilldelats varje länkad IoT-hubb. Med den här principen kan du belastningsutjämna enheter i en grupp med länkade hubbar baserat på de tilldelnings vikter som anges för dessa nav. Om du bara konfigurerar enheter till en IoT Hub rekommenderar vi den här inställningen. Den här inställningen är standardinställningen. 
    
    * **Statisk konfiguration**: den här principen kräver att en önskad IoT Hub anges i registrerings posten för att en enhet ska kunna tillhandahållas. Med den här principen kan du ange en enda viss IoT-hubb som du vill tilldela enheter till.

4. Under **Välj de IoT-hubbar som gruppen kan tilldelas till**väljer du de länkade IoT-hubbar som du vill ska ingå i din resursallokeringsprincip. Du kan också lägga till en ny länkad IoT-hubb med knappen **Länka en ny IoT Hub** .

    Med den **lägsta** tilldelnings principen för fördröjning kommer de nav du väljer att inkluderas i utvärderingen av fördröjningen för att fastställa den närmaste hubben för enhets tilldelning.

    Med en **jämt viktad** resursallokeringsprincip kommer enheterna att läsas in över de nav du väljer baserat på deras konfigurerade fördelnings vikter och deras aktuella enhets belastning.

    Med principen för **statisk konfiguration** av principen väljer du den IoT-hubb som du vill att enheter ska tilldelas till.

4. Klicka på **Spara**, eller Fortsätt till nästa avsnitt för att ange den ometablerings principen.

    ![Välj princip för registrerings tilldelning](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Ange etablerings principen

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din enhets etablerings tjänst instans.

2. Klicka på **Hantera registreringar**och klicka på den registrerings grupp eller enskild registrering som du vill konfigurera för att etablera om.

3. Under **Välj hur du vill att enhets data ska hanteras vid ny etablering till en annan IoT-hubb**väljer du någon av följande principer för att etablera:

    * **Förnya och migrera data**: den här principen vidtar åtgärder när enheter som är associerade med registrerings posten skickar en ny etablerings förfrågan. Beroende på konfigurationen av registrerings posten kan enheten omtilldelas till en annan IoT-hubb. Om enheten ändrar IoT-hubbar tas enhets registreringen med den inledande IoT Hub bort. All enhets tillstånds information från den inledande IoT-hubben migreras till den nya IoT-hubben. Under migreringen rapporteras enhetens status som **tilldela**

    * **Ometablering och återställning till ursprunglig konfiguration**: den här principen vidtar åtgärder när enheter som är associerade med registrerings posten skickar en ny etablerings förfrågan. Beroende på konfigurationen av registrerings posten kan enheten omtilldelas till en annan IoT-hubb. Om enheten ändrar IoT-hubbar tas enhets registreringen med den inledande IoT Hub bort. De inledande konfigurations data som etablerings tjänst instansen tog emot när enheten etablerades tillhandahålls till den nya IoT-hubben. Under migreringen rapporteras enhetens status som **tilldela**.

4. Klicka på **Spara** för att aktivera ometablering av enheten baserat på dina ändringar.

    ![Skärm bild som visar de ändringar du har gjort och knappen Spara.](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Skicka en etablerings förfrågan från enheten

För att enheter ska kunna etableras på grund av konfigurations ändringarna som gjorts i föregående avsnitt, måste dessa enheter begära reetablering. 

Hur ofta en enhet skickar en etablerings förfrågan beror på scenariot. Vi rekommenderar dock att programerar dina enheter för att skicka en etablerings förfrågan till en etablerings tjänst instans vid omstart och stödja en [metod](../iot-hub/iot-hub-devguide-direct-methods.md) för att manuellt utlösa etablering på begäran. Etableringen kan också utlösas genom att ange en [önskad egenskap](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

Den ometablerings principen på en registrerings post avgör hur enhets etablerings tjänst instansen hanterar dessa etablerings begär Anden och om enhets tillstånds data ska migreras under ometableringen. Samma principer är tillgängliga för enskilda registreringar och registrerings grupper:

Till exempel kod för att skicka etablerings begär Anden från en enhet under en startsekvens, se [Automatisk etablering av en simulerad enhet](quick-create-simulated-device.md).


## <a name="next-steps"></a>Nästa steg

- Mer information om hur du reetablerar finns i [IoT Hub metoder för att etablera enheter](concepts-device-reprovision.md) 
- Mer information om hur du avetablerar [enheter finns i så här avetablerar du enheter som tidigare var automatiskt etablerade](how-to-unprovision-devices.md) 











