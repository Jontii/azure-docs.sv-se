---
title: Snabbstart för att skicka telemetri till Azure IoT Hub (Android) | Microsoft Docs
description: I den här snabbstarten kör du ett Android-exempelprogram som skickar simulerad telemetri till en IoT-hubb och läser telemetrin från IoT-hubben för bearbetning i molnet.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-java
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: 5a077a325f29e38ce3647b2662c72aaad5adfc2b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150675"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Snabbstart: Skicka IoT-telemetri från en Android-enhet

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

I den här snabb starten skickar du telemetri till en Azure-IoT Hub från ett Android-program som körs på en fysisk eller simulerad enhet. IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. Den här snabb starten använder ett fördefinierat Android-program för att skicka Telemetrin. Telemetrin läses från IoT-hubben med hjälp av Azure Cloud Shell. Innan du kör programmet skapar du en IoT-hubb och registrera ren enhet med hubben.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Android Studio med Android SDK 27](https://developer.android.com/studio/). Mer information finns i [Android-installation](https://developer.android.com/studio/install). Android SDK-27 används av exemplet i den här artikeln.

* [Ett Android-exempelprogram](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample). Det här exemplet är en del av [Azure-IoT-samples-Java-](https://github.com/Azure-Samples/azure-iot-samples-java) lagringsplatsen.

* Port 8883 öppna i brand väggen. Enhets exemplet i den här snabb starten använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Lägg till Azure IoT-tillägg

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI till Cloud Shell-instansen. IOT-tillägget lägger till IoT Hub-, IoT Edge-och IoT Device Provisioning-tjänst (DPS)-kommandon i Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyAndroidDevice**: det här är namnet på enheten som du registrerar. Vi rekommenderar att du använder **MyAndroidDevice** som det visas. Om du väljer ett annat namn på din enhet måste du också använda det namnet i den här artikeln och uppdatera enhets namnet i exempel programmen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Kör följande kommando i Azure Cloud Shell för att hämta _enhets anslutnings strängen_ för enheten som du nyss registrerade:

    **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Anteckna enhetens anslutningssträng, som ser ut så här:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Du använder det här värdet senare i den här snabb starten för att skicka telemetri.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

1. Öppna Android-exempelprojektet från GitHub i Android Studio. Projektet finns i följande katalog i din klonade eller hämtade kopia av [Azure-IoT-Sample-Java](https://github.com/Azure-Samples/azure-iot-samples-java) -lagringsplatsen: *\azure-IoT-samples-java\iot-hub\Samples\device\AndroidSample*.

2. I Android Studio öppnar du *gradle. Properties* för exempelprojektet och ersätter plats hållaren **Device_Connection_String** med enhets anslutnings strängen som du antecknade tidigare.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. I Android Studio klickar du **på**  >  **Synkronisera projekt med Gradle-filer**. Kontrollera att bygget slutförs.

   > [!NOTE]
   > Om Project-synkroniseringen Miss lyckas kan det bero på någon av följande orsaker:
   >
   > * De versioner av Android Gradle-plugin-programmet och Gradle som refereras i projektet är inaktuella för din version av Android Studio. Följ [dessa instruktioner](https://developer.android.com/studio/releases/gradle-plugin) om du vill referera till och installera rätt versioner av plugin-och Gradle för din installation.
   > * Licens avtalet för Android SDK har inte signerats. Följ instruktionerna i build-utdata för att signera licens avtalet och Hämta SDK: n.

4. När versionen har slutförts klickar du på **Kör**  >  **Kör "app"**. Konfigurera appen att köras på en fysisk Android-enhet eller en Android-emulator. Mer information om att köra en Android-app på en fysisk enhet eller emulator finns i [Köra appen](https://developer.android.com/training/basics/firstapp/running-app).

5. När appen har lästs in klickar du på knappen **Starta** för att börja skicka telemetri till din IoT-hubb:

    ![Program](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Läs telemetrin från din hubb

I det här avsnittet använder du Azure Cloud Shell med [IoT-tillägget](/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) för att övervaka de enhetsmeddelanden som skickas av Android-enheten.

1. Med Azure Cloud Shell kör du följande kommando för att ansluta och läsa meddelanden från IoT-hubben:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Följande skärmbild visar utdata när IoT-hubben tar emot telemetri som skickas av Android-enheten:

      ![Läsa enhetsmeddelanden med hjälp av Azure CLI](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapar du en IoT-hubb, registrerat en enhet, skickade simulerad telemetri till hubben med ett Android-program och läser telemetri från hubben med hjälp av Azure Cloud Shell.

Om du vill veta hur du kan styra den simulerade enheten från ett serverdelsprogram fortsätter du till nästa snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb](quickstart-control-device-android.md)