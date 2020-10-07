---
title: Definiera en ny IoT-enhets typ i Azure IoT Central | Microsoft Docs
description: Den här artikeln visar dig som ett verktyg för att skapa en ny mall för Azure IoT-enheter i ditt Azure IoT Central-program. Du definierar telemetri, tillstånd, egenskaper och kommandon för din typ.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperfq1
- device-developer
ms.openlocfilehash: d6dd1bbf853a13948f55db4ae694b28cb7549c9b
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803797"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Definiera en ny IoT-enhetstyp i Azure IoT Central-programmet

*Den här artikeln gäller lösnings byggare och enhets utvecklare.*

En enhets mall är en skiss som definierar egenskaper och beteenden för en typ av enhet som ansluter till ett [Azure IoT Central-program](concepts-app-templates.md).

Ett verktyg kan till exempel skapa en enhets mal len för en ansluten fläkt med följande egenskaper:

- Skickar temperatur telemetri
- Skickar plats egenskap
- Skickar fel händelser för fläkt motor
- Skickar fläkt drifts tillstånd
- Tillhandahåller en skrivbar fläkt hastighets egenskap
- Innehåller ett kommando för att starta om enheten
- Ger dig en övergripande vy av enheten via en instrument panel

Med den här enhets mal len kan en operatör skapa och ansluta Real fläkt enheter. Alla dessa fläktar har mått, egenskaper och kommandon som operatörer använder för att övervaka och hantera dem. Operatörer använder [enhets instrument paneler](#add-dashboards) och formulär för att interagera med fläkt enheterna. En enhets utvecklare använder mallen för att förstå hur enheten interagerar med programmet. Läs mer i [telemetri, egenskaper och kommando nytto laster](concepts-telemetry-properties-commands.md).

> [!NOTE]
> Endast konstruktörer och administratörer kan skapa, redigera och ta bort mallar för enheter. Alla användare kan skapa enheter på sidan **enheter** från befintliga enhets mallar.

I ett IoT Central-program använder en mall en enhets kapacitets modell för att beskriva funktionerna i en enhet. Som ett verktyg har du flera alternativ för att skapa enhets mallar:

- Utforma enhets mal len i IoT Central och [implementera sedan dess enhets kapacitets modell i enhets koden](concepts-telemetry-properties-commands.md).
- Importera en enhets kapacitets modell från [Azure-certifierad för IoT-katalogen](https://aka.ms/iotdevcat). Lägg sedan till eventuella moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver.
- Skapa en enhets kapacitets modell med hjälp av Visual Studio Code. Implementera din enhets kod från modellen. Importera enhetens kapacitets modell manuellt till IoT Central programmet och Lägg sedan till eventuella moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver.
- Skapa en enhets kapacitets modell med hjälp av Visual Studio Code. Implementera din enhets kod från modellen och Anslut din riktiga enhet till ditt IoT Central program genom att använda en enhets första anslutning. IoT Central hittar och importerar enhets kapacitets modellen från den offentliga lagrings platsen åt dig. Du kan sedan lägga till alla moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver för enhets mal len.

Du kan också lägga till enhets mallar i ett IoT Central program med hjälp av [REST API](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) eller [CLI](howto-manage-iot-central-from-cli.md).

Vissa [programmallar](concepts-app-templates.md) innehåller redan enhetsspecifika mallar som är användbara i det scenario som program mal len stöder. Se till exempel [in-Store Analytics-arkitektur](../retail/store-analytics-architecture.md).

## <a name="create-a-device-template-from-the-device-catalog"></a>Skapa en enhets mall från enhets katalogen

Som ett verktyg kan du snabbt börja skapa din lösning med hjälp av en IoT Plug and Play-certifierad enhet (förhands granskning). Se listan i [Azure IoT-katalogen](https://catalog.azureiotsolutions.com/alldevices). IoT Central integreras med enhets katalogen så att du kan importera en enhets kapacitets modell från någon av dessa IoT-Plug and Play (för hands version) certifierade enheter. Så här skapar du en enhets mal len från någon av dessa enheter i IoT Central:

1. Gå till sidan **Device templates** i ditt IoT Central-program.
1. Välj **+ nytt**och välj sedan någon av IoT-plug and Play (för hands version) certifierade enheter från katalogen. IoT Central skapar en enhets mal len baserat på enhetens kapacitets modell.
1. Lägg till eventuella moln egenskaper, anpassningar eller vyer i din enhets mall.
1. Välj **publicera** för att göra mallen tillgänglig för operatörer för att visa och ansluta enheter.

## <a name="create-a-device-template-from-scratch"></a>Skapa en enhets mal len från grunden

En enhets mall innehåller:

- En _modell för enhets kapacitet_ som anger telemetri, egenskaper och kommandon som enheten implementerar. Dessa funktioner är indelade i ett eller flera gränssnitt.
- _Moln egenskaper_ som definierar information som IoT Central program lagrar om dina enheter. Till exempel kan en moln egenskap registrera datumet då enheten senast servades. Den här informationen delas aldrig med enheten.
- _Anpassningar_ gör att verktyget åsidosätter vissa definitioner i enhetens kapacitets modell. Verktyget kan till exempel åsidosätta namnet på en enhets egenskap. Egenskaps namn visas i IoT Central instrument paneler och formulär.
- _Instrument paneler och formulär_ gör att verktyget kan skapa ett användar gränssnitt som gör att operatörer kan övervaka och hantera de enheter som är anslutna till ditt program.

Så här skapar du en enhets mal len i IoT Central:

1. Gå till sidan **Device templates** i ditt IoT Central-program.
1. Välj **+ ny**  >  **anpassad**.
1. Ange ett namn för mallen, t. ex. **miljö sensor**.
1.  Tryck på **Retur**. IoT Central skapar en tom enhets mal len.

## <a name="manage-a-device-template"></a>Hantera en enhets mall

Du kan byta namn på eller ta bort en mall från mallens start sida.

När du har lagt till en enhets kapacitets modell i mallen kan du publicera den. Innan du har publicerat mallen kan du inte ansluta en enhet som baseras på den här mallen för dina operatörer att se på sidan **enheter** .

## <a name="create-a-capability-model"></a>Skapa en kapacitets modell

För att skapa en enhets kapacitets modell kan du:

- Använd IoT Central för att skapa en anpassad modell från grunden.
- Importera en modell från en JSON-fil. En Device Builder kan använda Visual Studio Code för att skapa en enhets kapacitets modell för ditt program.
- Välj en av enheterna i enhets katalogen. Med det här alternativet importeras enhetens kapacitets modell som tillverkaren har publicerat för den här enheten. En enhets funktions modell som importeras som detta publiceras automatiskt.

## <a name="manage-a-capability-model"></a>Hantera en kapacitets modell

När du har skapat en enhets kapacitets modell kan du:

- Lägg till gränssnitt i modellen. En modell måste ha minst ett gränssnitt.
- Redigera modellens metadata, till exempel ID, namnrymd och namn.
- Ta bort modellen.

## <a name="create-an-interface"></a>Skapa ett gränssnitt

En enhets kapacitet måste ha minst ett gränssnitt. Ett gränssnitt är en återanvändbar samling funktioner.

Så här skapar du ett gränssnitt:

1. Gå till enhetens funktions modell och välj **+ Lägg till gränssnitt**.

1. På sidan **Välj ett gränssnitt** kan du:

    - Skapa ett anpassat gränssnitt från grunden.
    - Importera ett befintligt gränssnitt från en fil. En Device Builder kan använda Visual Studio Code för att skapa ett gränssnitt för enheten.
    - Välj ett standard gränssnitt, till exempel **enhets informations** gränssnittet. Standard gränssnitt anger de funktioner som är gemensamma för många enheter. Dessa standard gränssnitt publiceras av Azure IoT och kan inte installeras eller redige ras.

1. När du har skapat ett gränssnitt väljer du **Redigera identitet** för att ändra gränssnittets visnings namn.

1. Om du väljer att skapa ett anpassat gränssnitt från grunden kan du lägga till enhetens funktioner. Enhets funktioner är telemetri, egenskaper och kommandon.

### <a name="telemetry"></a>Telemetri

Telemetri är en data ström med värden som skickas från enheten, vanligt vis från en sensor. En sensor kan till exempel rapportera omgivnings temperatur.

I följande tabell visas konfigurations inställningarna för en telemetri-funktion:

| Field | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för telemetri-värdet som används på instrument paneler och formulär. |
| Namn | Namnet på fältet i telemetri meddelandet. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. Det här fältet måste vara alfanumeriskt. |
| Typ av kapacitet | Telemetridata. |
| Semantisk typ | Den semantiska typen av telemetri, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Data typen telemetri, till exempel Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. Schemat är inte tillgängligt för semantiska typer av händelse och tillstånd. |
| Allvarlighetsgrad | Endast tillgängligt för den semantiska händelse typen. Allvarlighets graderna är **fel**, **information**eller **Varning**. |
| Tillstånds värden | Endast tillgängligt för semantisk typ av tillstånd. Definiera möjliga tillstånds värden, som var och en har visnings namn, namn, uppräknings typ och värde. |
| Enhet | En enhet för telemetri-värdet, till exempel **mph**, **%** eller ** &deg; C**. |
| Visa enhet | En visnings enhet för användning på instrument paneler och formulär. |
| Kommentar | Eventuella kommentarer om telemetri-funktionerna. |
| Beskrivning | En beskrivning av telemetri-funktionen. |

### <a name="properties"></a>Egenskaper

Egenskaperna representerar tidpunkts värden. En enhet kan till exempel använda en egenskap för att rapportera mål temperaturen som den försöker nå. Du kan ange skrivbara egenskaper från IoT Central.

I följande tabell visas konfigurations inställningarna för en egenskaps funktion:

| Field | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för egenskap svärdet som används på instrument paneler och formulär. |
| Namn | Egenskapens namn. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. Det här fältet måste vara alfanumeriskt. |
| Typ av kapacitet | Immaterialrätt. |
| Semantisk typ | Den semantiska typen för egenskapen, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Egenskaps data typen, t. ex. Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. Schemat är inte tillgängligt för semantiska typer av händelse och tillstånd. |
| Skrivbar | Om egenskapen inte är skrivbar kan enheten rapportera egenskaps värden till IoT Central. Om egenskapen är skrivbar kan enheten rapportera egenskaps värden till IoT Central och IoT Central kan skicka egenskaps uppdateringar till enheten.
| Allvarlighetsgrad | Endast tillgängligt för den semantiska händelse typen. Allvarlighets graderna är **fel**, **information**eller **Varning**. |
| Tillstånds värden | Endast tillgängligt för semantisk typ av tillstånd. Definiera möjliga tillstånds värden, som var och en har visnings namn, namn, uppräknings typ och värde. |
| Enhet | En enhet för egenskap svärdet, till exempel **mph**, **%** eller ** &deg; C**. |
| Visa enhet | En visnings enhet för användning på instrument paneler och formulär. |
| Kommentar | Kommentarer om egenskaps funktionen. |
| Beskrivning | En beskrivning av egenskaps funktionen. |

### <a name="commands"></a>Kommandon

Du kan anropa enhets kommandon från IoT Central. Kommandon kan skicka parametrar till enheten och få ett svar från enheten. Du kan till exempel anropa ett kommando för att starta om en enhet på 10 sekunder.

I följande tabell visas konfigurations inställningarna för en kommando funktion:

| Field | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för kommandot som används på instrument paneler och formulär. |
| Namn | Kommandots namn. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. Det här fältet måste vara alfanumeriskt. |
| Typ av kapacitet | Kommandoprompt. |
| Kommando | `SynchronousExecutionType`. |
| Kommentar | Eventuella kommentarer om kommando funktionen. |
| Beskrivning | En beskrivning av kommando funktionen. |
| Förfrågan | Om aktive rad, en definition av Request-parametern, inklusive: namn, visnings namn, schema, enhet och visnings enhet. |
| Svarsåtgärder | Om aktive rad, en definition av kommando svaret, inklusive: namn, visnings namn, schema, enhet och visnings enhet. |

#### <a name="offline-commands"></a>Offline-kommandon

Du kan välja köa kommandon om en enhet för närvarande är offline genom att aktivera **kön om alternativet offline** för ett kommando i enhets mal len.

Det här alternativet använder IoT Hub meddelanden från molnet till enheten för att skicka meddelanden till enheter. Mer information finns i IoT Hub artikeln [skicka meddelanden från moln till enhet](../../iot-hub/iot-hub-devguide-messages-c2d.md).

Meddelanden från moln till enhet:

- Är enkelriktade meddelanden till enheten från din lösning.
- Garantera en meddelande leverans på minst en gång. IoT Hub sparar meddelanden från molnet till enheten i köer per enhet, vilket garanterar återhämtning mot anslutnings-och enhets problem.
- Kräv att enheten ska implementera en meddelande hanterare för att bearbeta meddelandet från molnet till enheten.

> [!NOTE]
> Det här alternativet är endast tillgängligt i IoT Central webb gränssnitt. Den här inställningen tas inte med om du exporterar en modell eller ett gränssnitt från enhets mal len.

## <a name="manage-an-interface"></a>Hantera ett gränssnitt

Om du inte har publicerat gränssnittet kan du redigera funktionerna som definierats av gränssnittet. Om du vill göra ändringar när du har publicerat gränssnittet måste du skapa en ny version av enhets mal len och versionen av gränssnittet. Du kan göra ändringar som inte kräver versions hantering, till exempel visnings namn eller enheter, i avsnittet **Anpassa** .

Du kan också exportera gränssnittet som en JSON-fil om du vill återanvända det i en annan funktions modell.

## <a name="add-cloud-properties"></a>Lägga till molnegenskaper

Använd moln egenskaper för att lagra information om enheter i IoT Central. Moln egenskaper skickas aldrig till en enhet. Du kan till exempel använda moln egenskaper för att lagra namnet på kunden som har installerat enheten, eller enhetens senaste service datum.

I följande tabell visas konfigurations inställningarna för en moln egenskap:

| Field | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för moln egenskap svärdet som används på instrument paneler och formulär. |
| Namn | Namnet på moln egenskapen. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. |
| Semantisk typ | Den semantiska typen för egenskapen, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Data typen Cloud Property, till exempel Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. |

## <a name="add-customizations"></a>Lägg till anpassningar

Använd anpassningar när du behöver ändra ett importerat gränssnitt eller lägga till IoT Central-/regionsspecifika funktioner till en funktion. Du kan bara anpassa fält som inte avbryter gränssnittets kompatibilitet. Du kan till exempel:

- Anpassa visnings namn och enheter för en funktion.
- Lägg till en standard färg som ska användas när värdet visas i ett diagram.
- Ange start-, minimi-och max värden för en egenskap.

Du kan inte anpassa funktions namnet eller funktions typen. Om det finns ändringar som du inte kan göra i avsnittet **Anpassa** , måste du konfigurera din enhets mall och ditt gränssnitt för att ändra funktionen.

### <a name="generate-default-views"></a>Generera standardvyer

Att generera standardvyer är ett snabbt sätt att visualisera viktig enhets information. Du har upp till tre standardvyer som skapats för din enhets mall:

- **Kommandona** visar en vy med enhets kommandon och låter operatören skicka dem till enheten.
- **Översikt** innehåller en vy med enhets telemetri, som visar diagram och mått.
- **Om** visar en vy med enhets information och visar enhets egenskaper.

När du har valt **generera standardvyer**ser du att de har lagts till automatiskt under avsnittet **vyer** i din enhets mall.

## <a name="add-dashboards"></a>Lägg till instrument paneler

Lägg till instrument paneler till en enhets mall för att aktivera operatörer för att visualisera en enhet med hjälp av diagram och mått. Du kan ha flera instrument paneler för en enhets mall.

Lägga till en instrument panel i en enhets mall:

1. Gå till din enhets mall och välj **vyer**.
1. Välj **visualisera enheten**.
1. Ange ett namn på instrument panelen i **instrument panelens namn**.
1. Lägg till paneler på instrument panelen från listan över statiska, egenskaper, moln egenskaper, telemetri och kommando paneler. Dra och släpp de paneler som du vill lägga till på instrument panelen.
1. Om du vill rita flera telemetri värden på en enda diagram panel väljer du telemetri-värden och väljer sedan **kombinera**.
1. Konfigurera varje panel som du lägger till för att anpassa hur data visas. Du kan göra detta genom att välja kugg hjuls ikonen eller genom att välja **ändra konfiguration** på diagram panelen.
1. Ordna och ändra storlek på panelerna på instrument panelen.
1. Spara ändringarna.

### <a name="configure-preview-device-to-view-dashboard"></a>Konfigurera förhands gransknings enheten för att visa instrument panelen

Välj **Konfigurera för hands versions enhet**om du vill visa och testa din instrument panel. På så sätt kan du se instrument panelen när din operatör ser den när den har publicerats. Använd det här alternativet för att kontrol lera att dina vyer visar rätt data. Du kan välja bland följande alternativ:

- Ingen förhands gransknings enhet.
- Den riktiga testen het som du har konfigurerat för din enhets mall.
- En befintlig enhet i programmet med hjälp av enhets-ID: t.

## <a name="add-forms"></a>Lägg till formulär

Lägg till formulär i en enhets mall för att aktivera operatörer för att hantera en enhet genom att visa och ange egenskaper. Operatörer kan bara redigera moln egenskaper och skrivbara enhets egenskaper. Du kan ha flera formulär för en enhets mall.

Så här lägger du till ett formulär i en enhets mal len:

1. Gå till din enhets mall och välj **vyer**.
1. Välj **Redigera enhets-och moln data**.
1. Ange ett namn för ditt formulär i **formulär namn**.
1. Välj hur många kolumner som ska användas för att utforma ditt formulär.
1. Lägg till egenskaper till ett befintligt avsnitt i formuläret eller välj egenskaper och sedan **Lägg till avsnitt**. Använd avsnitt för att gruppera egenskaper i formuläret. Du kan lägga till en rubrik i ett avsnitt.
1. Konfigurera varje egenskap i formuläret för att anpassa dess beteende.
1. Ordna egenskaperna i formuläret.
1. Spara ändringarna.

## <a name="publish-a-device-template"></a>Publicera en enhetsmall

Innan du kan ansluta en enhet som implementerar din enhets kapacitets modell måste du publicera din enhets mall.

När du har publicerat en enhets mall kan du bara göra begränsade ändringar i enhetens kapacitets modell. Om du vill ändra ett gränssnitt måste du [skapa och publicera en ny version](./howto-version-device-template.md).

Om du vill publicera en enhets mal len går du till din enhets mall och väljer **publicera**.

När du har publicerat en enhets mall kan en operatör gå till sidan **enheter** och lägga till antingen verkliga eller simulerade enheter som använder din enhets mall. Du kan fortsätta att ändra och spara din enhets mall när du gör ändringar. När du vill skicka ut dessa ändringar till operatören och visa dem på sidan **enheter** måste du välja **publicera** varje gång.

## <a name="next-steps"></a>Nästa steg

Om du är en enhets utvecklare är ett förslag till nästa steg att läsa om [versions hantering av enhets mallar](./howto-version-device-template.md).
