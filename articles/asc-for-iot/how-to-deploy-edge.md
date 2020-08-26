---
title: Distribuera IoT Edge säkerhetsmodul
description: Lär dig hur du distribuerar en Azure Security Center för IoT-Säkerhetsagenten på IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 1c646c750cb54228211fadb0a4f6733d495e9219
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892073"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Distribuera en säkerhetsmodul på din IoT Edge-enhet

**Azure Security Center för IoT** -modulen tillhandahåller en omfattande säkerhetslösning för dina IoT Edge-enheter.
Säkerhetsmodulen samlar in, samlar in och analyserar rå säkerhets data från operativ systemet och behållar systemet till åtgärds bara säkerhets rekommendationer och aviseringar.
Mer information finns i [säkerhetsmodulen för IoT Edge](security-edge-architecture.md).

I den här artikeln får du lära dig hur du distribuerar en säkerhetsmodul på din IoT Edge-enhet.

## <a name="deploy-security-module"></a>Distribuera säkerhetsmodul

Använd följande steg för att distribuera en Azure Security Center för IoT-säkerhetsmodulen för IoT Edge.

### <a name="prerequisites"></a>Förutsättningar

1. Kontrol lera att enheten är [registrerad som en IoT Edge enhet](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)i IoT Hub.

1. För att Azure Security Center för IoT Edge modul krävs att det [granskade ramverket](https://linux.die.net/man/8/auditd) är installerat på IoT Edge enheten.

    - Installera ramverket genom att köra följande kommando på din IoT Edge enhet:

    `sudo apt-get install auditd audispd-plugins`

    - Kontrol lera att granskningen är aktiv genom att köra följande kommando:

    `sudo systemctl status auditd`<br>
    - Förväntat svar är: `active (running)`

### <a name="deployment-using-azure-portal"></a>Distribution med hjälp av Azure Portal

1. Öppna **Marketplace**från Azure Portal.

1. Välj **Sakernas Internet**och sök efter **Azure Security Center för IoT** och välj den.

   ![Välj Azure Security Center för IoT](media/howto/edge-onboarding-8.png)

1. Konfigurera distributionen genom att klicka på **skapa** .

1. Välj Azure- **prenumerationen** för din IoT Hub och välj sedan **IoT Hub**.<br>Välj **distribuera till en enhet** för att rikta in dig på en enskild enhet eller Välj **distribuera i skala** för att rikta flera enheter och klicka på **skapa**. Mer information om [hur du](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)distribuerar i skala finns i distribuera.

    >[!Note]
    >Om du har valt **distribuera i skala**lägger du till enhets namnet och informationen innan du fortsätter till fliken **Lägg till moduler** i följande instruktioner.

Slutför varje steg för att slutföra din IoT Edge-distribution för Azure Security Center för IoT.

#### <a name="step-1-modules"></a>Steg 1: moduler

1. Välj modulen **AzureSecurityCenterforIoT** .
1. På fliken **Modulnamn** ändrar du **namnet** till **azureiotsecurity**.
1. På fliken **miljön variabler** lägger du till en variabel vid behov (till exempel fel söknings nivå).
1. På fliken **behållare skapa alternativ** lägger du till följande konfiguration:

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }
    ```

1. På fliken **dubbla inställningar för modul** lägger du till följande konfiguration:

   Modul, delad egenskap:
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   Modul, dubbla egenskaps innehåll: 

   ```json
     {

     }
   ```
    
   Mer information om hur du konfigurerar agenten finns i [Konfigurera säkerhets agenter](https://docs.microsoft.com/azure/asc-for-iot/how-to-agent-configuration).

1. Välj **Uppdatera**.

#### <a name="step-2-runtime-settings"></a>Steg 2: körnings inställningar

1. Välj **körnings inställningar**.
1. Ändra **bilden** till **MCR.Microsoft.com/azureiotedge-Hub:1.0.8.3**under **Edge Hub**.
1. Kontrol lera att **skapa-alternativ** har angetts till följande konfiguration:

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

1. Välj **Spara**.

1. Välj **Nästa**.

#### <a name="step-3-specify-routes"></a>Steg 3: Ange vägar

1. På fliken **Ange vägar** kontrollerar du att du har en väg (explicit eller implicit) som vidarebefordrar meddelanden från **azureiotsecurity** -modulen till **$upstream** enligt följande exempel. Välj **Nästa**när vägen är på plats.

   Exempel vägar:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. Välj **Nästa**.

#### <a name="step-4-review-deployment"></a>Steg 4: granska distribution

- På fliken **Granska distribution** granskar du distributions informationen och väljer sedan **skapa** för att slutföra distributionen.

## <a name="diagnostic-steps"></a>Diagnostiska steg

Om du stöter på problem är behållar loggarna det bästa sättet att lära sig om status för en IoT Edge säkerhetsmodulen het. Använd kommandona och verktygen i det här avsnittet för att samla in information.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Kontrol lera att de obligatoriska behållarna är installerade och fungerar som förväntat

1. Kör följande kommando på din IoT Edge enhet:

    `sudo docker ps`

1. Kontrol lera att följande behållare körs:

   | Namn | BILD |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   Om minsta antalet obligatoriska behållare inte finns kontrollerar du om ditt IoT Edge distributions manifest är justerat med de rekommenderade inställningarna. Mer information finns i [distribuera IoT Edge-modulen](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Granska modulens loggar för fel

1. Kör följande kommando på din IoT Edge enhet:

   `sudo docker logs azureiotsecurity`

1. För mer utförliga loggar lägger du till följande miljö variabel i **azureiotsecurity** -modulen distribution: `logLevel=Debug` .

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om konfigurations alternativ fortsätter du till instruktions guiden för konfiguration av modulen.
> [!div class="nextstepaction"]
> [Guide för konfiguration av moduler](./how-to-agent-configuration.md)
