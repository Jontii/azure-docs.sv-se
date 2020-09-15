---
title: Använd olika mekanismer för attestering med Azure IoT Hub Device Provisioning Service client SDK
description: Azure så här använder du olika attesterings metoder med klient-SDK för enhets etablerings tjänsten (DPS) i Azure
author: wesmc7777
ms.author: wesmc
ms.date: 03/30/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom:
- mvc
- amqp
ms.openlocfilehash: 0a32e2f055b2914fa0008e043e80092ac2da0814
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531516"
---
# <a name="how-to-use-different-attestation-mechanisms-with-device-provisioning-service-client-sdk-for-c"></a>Använda olika attesteringsmetoder för maskinvara med klient-SDK för enhetsetableringstjänsten för C

I den här artikeln beskrivs hur du använder olika [attesteringsmetoder](concepts-service.md#attestation-mechanism) med klient-SDK för enhetsetableringstjänsten för C. Du kan antingen använda en fysisk enhet eller en simulator. Etableringstjänsten stöder autentisering för två typer av attesteringsmetoder: X.509 och TPM (Trusted Platform Module).

## <a name="prerequisites"></a>Förutsättningar

Förbered din utvecklingsmiljö enligt avsnittet "Prepare the development environment" (förbereda utvecklingsmiljön) i guiden [Create and provision simulated device](./quick-create-simulated-device.md) (Skapa och etablera en simulerad enhet).

### <a name="choose-an-attestation-mechanism"></a>Välj en attesteringsmetod

Som enhetens tillverkare måste du först välja attesteringsmetod (eller HSM:er) som baseras på någon av typerna som stöds. För närvarande erbjuder [klient-SDK för enhetsetableringstjänsten för C](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) stöd för följande HSM:er: 

- [Trusted Platform Module (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module): TPM är en etablerad standard för de flesta plattformar för Windows-baserade enheter, samt några Linux-/Ubuntu-baserade enheter. Som enhets tillverkare kan du välja den här mekanismen för attestering om du har något av dessa operativ system som körs på dina enheter och du letar efter en etablerad standard. Med TPM-kretsar kan du endast registrera varje enhet individuellt till enhetsetableringstjänsten. I utvecklingssyfte kan du använda TPM-simulatorn på din Windows- eller Linux-utvecklingsdator.

- [X.509](https://cryptography.io/en/latest/x509/): X.509-certifikat kan lagras i relativt nya HSM-kretsar, så kallade [maskinvarubaserade säkerhetsmoduler](concepts-service.md#hardware-security-module). Arbetet fortlöper också inom Microsoft, på RIoT- eller DICE-kretsar, som implementerar X.509-certifikaten. Med X.509-kretsar kan du göra massregistrering av enheter i portalen. De stöder även vissa icke-Windows-operativsystem som embedOS. I utvecklingssyfte stöder klient-SDK:er för enhetsetableringstjänster en X.509-enhetssimulator. 

Mer information finns i IoT Hub Device Provisioning Service [attesterings metoder](concepts-service.md#attestation-mechanism).

## <a name="enable-authentication-for-supported-attestation-mechanisms"></a>Aktivera autentisering för attesteringsmetoder som stöds

Autentiseringsläge (X.509 eller TPM) för SDK måste aktiveras för den fysiska enheten eller simulatorn innan den kan registreras i Azure Portal. Navigera först till rotmappen för azure-iot-sdk-c. Kör sedan det angivna kommandot beroende på vilket autentiseringsläge du väljer:

### <a name="use-x509-with-simulator"></a>Använd X.509 med simulator

Etablerings tjänsten levereras med en tärning-emulator (Device Identity komposition Engine) som genererar ett **X. 509** -certifikat för autentisering av enheten. Kör följande kommando för att aktivera **X. 509** -autentisering: 

```
cmake -Ddps_auth_type=x509 ..
```

Information om maskinvara med DICE finns [här](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/).

### <a name="use-x509-with-hardware"></a>Använd X.509 med maskinvara

Etablerings tjänsten kan användas med **X. 509** på annan maskin vara. Det behövs ett gränssnitt mellan maskinvara och SDK:t för att upprätta anslutningen. Prata med tillverkaren av din maskinvarusäkerhetsmodul för mer information om gränssnittet.

### <a name="use-tpm"></a>Använd TPM

Etableringstjänsten kan ansluta till TPM-kretsar i Windows- och Linux-maskinvara med SAS-Token. Kör följande kommando för att aktivera TPM-autentisering:

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>Använd TPM med simulator

Om du inte har en enhet med TPM-krets, kan du använda en simulator för utveckling i Windows-operativsystemet. Kör följande kommando för att aktivera TPM-autentisering och köra TPM-simulatorn:

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>Bygga SDK:t
Bygga SDK:t innan du skapar enhetsregistreringen.

### <a name="linux"></a>Linux
- Bygga SDK:t i Linux:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- Om du vill bygga binärfiler för felsökning lägger du till motsvarande CMake-alternativ i kommandot för projektgenerering, till exempel:
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- Det finns många [CMake-konfigurationsalternativ](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html) för att bygga SDK:t. Du kan till exempel inaktivera en av de tillgängliga protokollstackarna genom att lägga till ett argument i CMake-kommandot för projektgenerering:
    ```
    cmake -Duse_amqp=OFF ..
    ```
- Du kan också bygga och köra enhetstest:
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- Vidta följande steg för generering av projektfiler för att bygga SDK:t i Windows:
  - Öppna kommandotolken för utvecklare för VS2015
  - Kör följande CMake-kommandon från rotkatalogen för källkoden:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake -G "Visual Studio 14 2015" ..
    ```
    Det här kommandot bygger x86-bibliotek. Om du vill bygga för x64 ändrar du cmake generator-argumentet: 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- Om projektgenereringen slutförs utan fel, kan du se en Visual Studio-lösningsfil (.sln) under `cmake`-mappen. Så här bygger du SDK:t:
    - Öppna **cmake\azure_iot_sdks.sln** i Visual Studio och bygg den, **ELLER**
    - Kör följande kommando på kommandotolken du använde för att generera projektfilerna:
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- Använd motsvarande MSBuild-argument för att bygga binära filer för felsökning: 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- Det finns många CMake-konfigurationsalternativ för att bygga SDK:t. Du kan till exempel inaktivera en av de tillgängliga protokollstackarna genom att lägga till ett argument i CMake-kommandot för projektgenerering:
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- Du kan också bygga och köra enhetstester:
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>Bibliotek att inkludera
- Dessa bibliotek ska ingå i SDK:t:
    - Etableringstjänsten: dps_http_transport dps_client, dps_security_client
    - IoTHub-säkerhet: iothub_security_client

## <a name="create-a-device-enrollment-entry-in-device-provisioning-services"></a>Skapa en post för enhetsregistrering i Device Provisioning-tjänsten

### <a name="tpm"></a>TPM
Om du använder TPM, följer du anvisningarna i ["Create and provision a simulated device using IoT Hub Device Provisioning Service"](./quick-create-simulated-device.md) (skapa och etablera en simulerad enhet med enhetsetableringstjänsten för IoT-hubb) för att skapa en post för registrering av enheten i din enhetsetableringstjänst och simulering av den första starten.

### <a name="x509"></a>X.509

1. Om du vill registrera en enhet i etableringstjänsten behöver du anteckna bekräftelsenyckel och registrerings-ID för varje enhet. Dessa visas i etableringsverktyget som tillhandahålls av klient-SDK:t. Kör följande kommando för att skriva ut rotcertifikatutfärdarens certifikat (för gruppregistrering) och lövcertifikatet (för enskild registrering):
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Logga in på Azure-portalen, klicka på knappen **Alla resurser** i den vänstra menyn och öppna Device Provisioning-tjänsten.
   - **X. 509-registrering**: i sammanfattnings bladet för etablerings tjänsten väljer du **Hantera registreringar**. Välj fliken **Individual Enrollments** (Enskilda registreringar) och klicka på knappen **Lägg till** längst upp. Välj **X. 509** som *mekanism*för identitets attestering, ladda upp löv certifikatet som krävs av bladet. Klicka på knappen **Spara** när det är klart. 
   - **Grupp registrering för X. 509**: Välj **Hantera registreringar**på bladet Sammanfattning för etablerings tjänsten. Välj fliken **Group Enrollments** (gruppregistreringar) och klicka på knappen **Lägg till** längst upp. Välj **X. 509** som *mekanism*för identitets attestering, ange ett grupp namn och certifikat namn, ladda upp ca/mellanliggande certifikat som krävs av bladet. Klicka på knappen **Spara** när det är klart. 

## <a name="enable-authentication-for-devices-using-a-custom-attestation-mechanism-optional"></a>Aktivera autentisering för enheter med hjälp av en anpassad attesteringsmetod (valfri)

> [!NOTE]
> Det här avsnittet gäller endast enheter som kräver stöd för en anpassad plattform eller attesteringsmetoder som för närvarande inte stöds av klient-SDK för enhetsetableringstjänsten för C. Observera även att SDK ofta använder termen ”HSM” som en generisk ersättning för ”attesteringsmetod”.

Du måste först att utveckla en lagringsplats och ett bibliotek för din anpassade attesteringsmetod:

1. Utveckla ett bibliotek som ska få åtkomst din attesteringsmetod. Det här projektet måste skapa ett statiskt bibliotek som ska användas av enhetsetableringens SDK.

2. Implementera funktionerna som definieras i följande huvudfil i ditt bibliotek: 

    - För en anpassad TPM: implementera funktionerna under [HSM TPM API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api).  
    - För en anpassad X.509: implementera funktionerna under [HSM X509 API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api). 

När biblioteket skapas på egen hand måste du integrera det med klient-SDK för enhetsetableringstjänster genom att länka till ditt bibliotek. :

1. Ange den anpassade GitHub-lagringsplatsen och bibliotekets sökväg och dess namn i följande `cmake`-kommando:
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
2. Öppna Visual Studio-lösningsfilen som skapats av CMake (`\azure-iot-sdk-c\cmake\azure_iot_sdks.sln`) och skapa den. 

    - Skapandeprocessen kompilerar SDK-biblioteket.
    - SDK:n försöker länka mot det anpassade biblioteket enligt definitionen i kommandot `cmake`.

3. Kör exempelappen ”prov_dev_client_ll_sample” under ”Provision_Samples” (under `\azure-iot-sdk-c\cmake\provisioning_client\samples\prov_dev_client_ll_sample`) för att verifiera att din attesteringsmetod har implementerats korrekt.

## <a name="connecting-to-iot-hub-after-provisioning"></a>Ansluta till IoT Hub efter etablering

När enheten har etablerats med etablerings tjänsten använder det här API: t det angivna autentiseringsläget (**X. 509** eller TPM) för att ansluta till IoT Hub: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```
