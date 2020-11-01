---
title: Mata in telemetri från IoT Hub
titleSuffix: Azure Digital Twins
description: Se hur du matar in meddelanden för telemetri från IoT Hub.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d2606f793c7ab2e3ac29b1eb869e60a2c8e634ad
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145930"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Mata in IoT Hub telemetri i Azure Digitals, dubbla

Digitala Azure-enheter drivs med data från IoT-enheter och andra källor. En gemensam källa för enhets data som ska användas i Azure Digitals dubbla är [IoT Hub](../iot-hub/about-iot-hub.md).

Processen för att mata in data i Azure Digitals, är att konfigurera en extern beräknings resurs, till exempel en [Azure-funktion](../azure-functions/functions-overview.md), som tar emot data och använder [DigitalTwins-API: er](/rest/api/digital-twins/dataplane/twins) för att ange egenskaper eller brand släcknings händelser på [digitala dubbla](concepts-twins-graph.md) . 

Det här dokumentet vägleder dig genom processen för att skriva en Azure-funktion som kan mata in telemetri från IoT Hub.

## <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter med det här exemplet måste du konfigurera följande resurser som krav:
* **En IoT-hubb** . Anvisningar finns i avsnittet *skapa en IoT Hub* i [den här IoT Hub snabb](../iot-hub/quickstart-send-telemetry-cli.md)starten.
* **En Azure-funktion** med rätt behörighet för att anropa den digitala dubbla instansen. Instruktioner finns i [*instruktion: Konfigurera en Azure-funktion för bearbetning av data*](how-to-create-azure-function.md). 
* **En digital Azure-instans** som tar emot din enhets telemetri. Anvisningar finns i anvisningar [*: Konfigurera en digital Azure-instans och autentisering*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Exempel scenario för telemetri

Den här instruktionen beskriver hur du skickar meddelanden från IoT Hub till Azures digitala dubbla, med hjälp av en Azure-funktion. Det finns många möjliga konfigurationer och matchnings strategier som du kan använda för att skicka meddelanden, men exemplet för den här artikeln innehåller följande delar:
* En termometer-enhet i IoT Hub, med ett känt enhets-ID
* En digital enhet som representerar enheten, med ett matchande ID

> [!NOTE]
> I det här exemplet används en enkel ID-matchning mellan enhets-ID: t och motsvarande digitala dubbla ID, men det är möjligt att ge mer sofistikerade mappningar från enheten till dess dubbla (till exempel med en mappnings tabell).

När en händelse för temperatur telemetri skickas av termostat-enheten, bearbetar en Azure-funktion Telemetrin och egenskapen *temperatur* för den digitala enheten bör uppdateras. Det här scenariot beskrivs i ett diagram nedan:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Ett diagram som visar ett flödes diagram. I diagrammet skickar en IoT Hub enhet temperatur telemetri via IoT Hub till en Azure-funktion som uppdaterar en temperatur egenskap på en enhet i en digital i Azure Digitals." border="false":::

## <a name="add-a-model-and-twin"></a>Lägg till en modell och dubbla

Du kan lägga till/Ladda upp en modell med kommandot CLI nedan och sedan skapa en dubbel med den här modellen som kommer att uppdateras med information från IoT Hub.

Modellen ser ut så här:
```JSON
{
  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "contents": [
    {
      "@type": "Property",
      "name": "Temperature",
      "schema": "double"
    }
  ]
}
```

Om du vill **överföra den här modellen till din dubbla instansen** öppnar du Azure CLI och kör följande kommando:

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

Sedan måste du **skapa en dubbel med den här modellen** . Använd följande kommando för att skapa en dubbla och ange 0,0 som första temperatur värde.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

Utdata från ett lyckat dubbelt skapande-kommando bör se ut så här:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

I det här avsnittet används samma start steg i Visual Studio och Azure Function Skeleton från [*anvisningar: Konfigurera en Azure-funktion för bearbetning av data*](how-to-create-azure-function.md). Skeleton hanterar autentisering och skapar en tjänst klient som är redo att bearbeta data och anropa Azure Digitals dubbla API: er som svar. 

I de steg som följer ska du lägga till en speciell kod i den för att bearbeta IoT telemetri-händelser från IoT Hub.  

### <a name="add-telemetry-processing"></a>Lägg till telemetri-bearbetning
    
Telemetri händelser levereras i form av meddelanden från enheten. Det första steget vid tillägg av telemetri – bearbetnings kod extraherar relevant del av enhets meddelandet från Event Grid-händelsen. 

Olika enheter kan strukturera sina meddelanden på olika sätt, så koden för **det här steget beror på den anslutna enheten.** 

Följande kod visar ett exempel på en enkel enhet som skickar telemetri som JSON. Det här exemplet är helt utforskat i [*Självstudier: Anslut en lösning från slut punkt till slut punkt*](./tutorial-end-to-end.md). Följande kod hittar enhets-ID: t för enheten som skickade meddelandet, samt temperatur svärdet.

```csharp
JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
var temperature = deviceMessage["body"]["Temperature"];
```

Nästa kod exempel tar med ID och temperatur värde och använder dem i "patch" (gör uppdateringar till) som är dubbla.

```csharp
//Update twin using device temperature
var updateTwinData = new JsonPatchDocument();
updateTwinData.AppendReplace("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(deviceId, updateTwinData);
...
```

### <a name="update-your-azure-function-code"></a>Uppdatera din Azure Function-kod

Nu när du förstår koden från de tidigare exemplen öppnar du Azure-funktionen från avsnittet [*krav*](#prerequisites) i Visual Studio. (Om du inte har en Azure-funktion kan du gå till länken i förutsättningarna för att skapa en nu).

Ersätt din Azure Function-kod med den här exempel koden.

```csharp
using System;
using System.Net.Http;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace IotHubtoTwins
{
    public class IoTHubtoTwins
    {
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("IoTHubtoTwins")]
        public async void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");

            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(
                    new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions 
                    { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
            
                if (eventGridEvent != null && eventGridEvent.Data != null)
                {
                    log.LogInformation(eventGridEvent.Data.ToString());

                    // Reading deviceId and temperature for IoT Hub JSON
                    JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                    string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
                    var temperature = deviceMessage["body"]["Temperature"];
                    
                    log.LogInformation($"Device:{deviceId} Temperature is:{temperature}");

                    //Update twin using device temperature
                    var updateTwinData = new JsonPatchDocument();
                    updateTwinData.AppendReplace("/Temperature", temperature.Value<double>());
                    await client.UpdateDigitalTwinAsync(deviceId, updateTwinData);
                }
            }
            catch (Exception e)
            {
                log.LogError($"Error in ingest function: {e.Message}");
            }
        }
    }
}
```
Spara funktions koden och publicera Function-appen till Azure. Du kan göra detta genom att läsa avsnittet om att [*publicera Funktionsapp*](./how-to-create-azure-function.md#publish-the-function-app-to-azure) avsnittet i [*How-to: Konfigurera en Azure-funktion för bearbetning av data*](how-to-create-azure-function.md).

Efter en lyckad publicering visas utdata i Visual Studio-kommando fönstret som visas nedan:

```cmd
1>------ Build started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
1>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>------ Publish started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\Out\
2>Publishing C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\adtIngestFunctionSample - 20200911112545669.zip to https://adtingestfunctionsample20200818134346.scm.azurewebsites.net/api/zipdeploy...
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
========== Publish: 1 succeeded, 0 failed, 0 skipped ==========
```
Du kan också kontrol lera status för publicerings processen i [Azure Portal](https://portal.azure.com/). Sök efter din _resurs grupp_ och navigera till _aktivitets loggen_ och leta efter _Hämta publicerings profil för webbapp_ i listan och kontrol lera att statusen har slutförts.

:::image type="content" source="media/how-to-ingest-iot-hub-data/azure-function-publish-activity-log.png" alt-text="Ett diagram som visar ett flödes diagram. I diagrammet skickar en IoT Hub enhet temperatur telemetri via IoT Hub till en Azure-funktion som uppdaterar en temperatur egenskap på en enhet i en digital i Azure Digitals.":::

## <a name="connect-your-function-to-iot-hub"></a>Anslut din funktion till IoT Hub

Konfigurera ett händelse mål för Hub-data.
I [Azure Portal](https://portal.azure.com/)navigerar du till IoT Hub-instansen som du skapade i avsnittet [*krav*](#prerequisites) . Under **händelser** skapar du en prenumeration för Azure-funktionen.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Ett diagram som visar ett flödes diagram. I diagrammet skickar en IoT Hub enhet temperatur telemetri via IoT Hub till en Azure-funktion som uppdaterar en temperatur egenskap på en enhet i en digital i Azure Digitals.":::

På sidan **Skapa händelse prenumeration** fyller du i fälten enligt följande:
  1. Namn på den prenumeration du vill ha under **namn** .
  2. Under **händelse schema** väljer du _Event Grid schema_ .
  3. Under **händelse typer** väljer du kryss rutan _telemetri för enheter_ och avmarkerar andra händelse typer.
  4. Välj _Azure Function_ under **typ av slut punkt** .
  5. Under **slut punkt** väljer du _Välj en slut punkts_ länk för att skapa en slut punkt.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Ett diagram som visar ett flödes diagram. I diagrammet skickar en IoT Hub enhet temperatur telemetri via IoT Hub till en Azure-funktion som uppdaterar en temperatur egenskap på en enhet i en digital i Azure Digitals.":::

Verifiera nedanstående information på sidan _Välj Azure-funktion_ som öppnas.
 1. **Prenumeration** : Din Azure-prenumeration
 2. **Resurs grupp** : din resurs grupp
 3. **Function-app** : namnet på din Function-app
 4. **Fack** : _produktion_
 5. **Funktion** : Välj din Azure-funktion i list rutan.

Spara informationen genom att välja knappen _Bekräfta markering_ .            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="Ett diagram som visar ett flödes diagram. I diagrammet skickar en IoT Hub enhet temperatur telemetri via IoT Hub till en Azure-funktion som uppdaterar en temperatur egenskap på en enhet i en digital i Azure Digitals.":::

Klicka på _skapa_ om du vill skapa en händelse prenumeration.

## <a name="send-simulated-iot-data"></a>Skicka simulerade IoT-data

Testa din nya ingångs funktion genom att använda enhets simulatorn från [*självstudien: Anslut en lösning från slut punkt till slut punkt*](./tutorial-end-to-end.md). Den själv studie kursen drivs av ett exempel projekt som skrivits i C#. Exempel koden finns här: [Azure Digitals dubblare-exempel från slut punkt till slut punkt](/samples/azure-samples/digital-twins-samples/digital-twins-samples). Du kommer att använda **DeviceSimulator** -projektet på den lagrings platsen.

I självstudierna från slut punkt till slut punkt utför du följande steg:
1. [*Registrera den simulerade enheten med IoT Hub*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Konfigurera och kör simuleringen*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Verifiera dina resultat

När du kör enhets simulatorn ovan ändras temperatur värdet för din digitala garn. I Azure CLI kör du följande kommando för att visa temperatur svärdet.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

Dina utdata bör innehålla ett temperatur värde som detta:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Om du vill se värde ändringen kör du kommandot query upprepade gånger.

## <a name="next-steps"></a>Nästa steg

Läs om data ingress och utgånget med Azure Digitals:
* [*Koncept: integrering med andra tjänster*](concepts-integration.md)