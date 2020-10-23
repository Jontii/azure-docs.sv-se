---
title: Telemetri, egenskaper och kommando nytto laster i Azure IoT Central | Microsoft Docs
description: Med Azure IoT Central Device templates kan du ange telemetri, egenskaper och kommandon för en enhet måste implementera. Förstå formatet på de data som en enhet kan utbyta med IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 9e5288bb177d5827f05003e4561bc79240a71b59
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427872"
---
# <a name="telemetry-property-and-command-payloads"></a>Telemetri, egenskaper och kommandonyttolaster

_Den här artikeln gäller för enhets utvecklare._

En enhets mall i Azure IoT Central är en skiss som definierar:

* Telemetri en enhet skickar till IoT Central.
* Egenskaper som en enhet synkroniseras med IoT Central.
* Kommandon som IoT Central anrop på en enhet.

I den här artikeln beskrivs, för enhets utvecklare, de JSON-nyttolaster som enheterna skickar och tar emot för telemetri, egenskaper och kommandon som definierats i en enhets mall.

Artikeln beskriver inte varje möjlig typ av telemetri, egenskaper och kommando nytto Last, men exemplen illustrerar alla nyckel typer.

I varje exempel visas ett kodfragment från den enhets kapacitets modell (DCM) som definierar typ och exempel på JSON-nyttolaster för att illustrera hur enheten ska interagera med IoT Central-programmet.

> [!NOTE]
> IoT Central accepterar en giltig JSON-uppsättning, men den kan bara användas för visualiseringar om den matchar en definition i DCM. Du kan exportera data som inte matchar en definition, se [Exportera IoT-data till mål i Azure](howto-export-data.md).

JSON-filen som definierar DCM använder sig av [DTDL (Digital Definition Language) v1](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/dtdlv1.md). I den här specifikationen ingår definitionen av `@id` egenskaps formatet.

För exempel enhets kod som visar några av dessa nytto laster som används, se avsnittet [skapa och ansluta ett klient program till ditt azure IoT Central-program (Node.js)](tutorial-connect-device-nodejs.md) och [skapa och Anslut ett klient program till dina Azure IoT Central-program (python)](tutorial-connect-device-python.md) .

## <a name="view-raw-data"></a>Visa rå data

Med IoT Central kan du Visa rå data som en enhet skickar till ett program. Den här vyn är användbar för fel sökning av problem med nytto lasten som skickas från en enhet. Så här visar du de rå data som en enhet skickar:

1. Navigera till enheten från sidan **enheter** .

1. Välj fliken **rå data** :

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="Vyn rå data":::

    I den här vyn kan du välja vilka kolumner som ska visas och ange ett tidsintervall som ska visas. I kolumnen för staplade **data** visas data från enheten som inte matchar några definitioner för egenskaper eller telemetri i enhets mal len.

## <a name="telemetry"></a>Telemetri

### <a name="primitive-types"></a>Primitiva typer

I det här avsnittet visas exempel på typer av primitiva telemetri som en enhet strömmar till ett IoT Central-program.

Följande kodfragment från ett DCM visar definitionen av en typ av `boolean` telemetri:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel:

```json
{ "BooleanTelemetry": true }
```

Följande kodfragment från ett DCM visar definitionen av en typ av `string` telemetri:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel:

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

Följande kodfragment från ett DCM visar definitionen av en typ av `integer` telemetri:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel:

```json
{ "IntegerTelemetry": 23 }
```

Följande kodfragment från ett DCM visar definitionen av en typ av `double` telemetri:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel:

```json
{ "DoubleTelemetry": 56.78 }
```

Följande kodfragment från ett DCM visar definitionen av en typ av `dateTime` telemetri:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel – `DateTime` typer måste vara ISO 8061-kompatibel:

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

Följande kodfragment från ett DCM visar definitionen av en typ av `duration` telemetri:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel – varaktigheten måste vara ISO 8601 duration-kompatibel:

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>Komplexa typer

Det här avsnittet innehåller exempel på komplexa typer av telemetri som en enhet strömmar till ett IoT Central-program.

Följande kodfragment från ett DCM visar definitionen av en typ av `geopoint` telemetri:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel. IoT Central visar värdet som en PIN-kod på en karta:

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Följande kodfragment från ett DCM visar definitionen av en typ av `Enum` telemetri:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel. Möjliga värden är `0` , `1` och `2` som visas i IoT central som `Item1` , `Item2` och `Item3` :

```json
{ "EnumTelemetry": 1 }
```

Följande kodfragment från ett DCM visar definitionen av en typ av `Object` telemetri. Det här objektet har tre fält med typerna `dateTime` , `integer` och `Enum` :

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@id": "<element id>",
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel. `DateTime` typerna måste vara ISO 8061-kompatibla. Möjliga värden för `Property3` är `0` , `1` och som visas i IoT central som `Item1` , `Item2` och `Item3` :

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

Följande kodfragment från ett DCM visar definitionen av en typ av `vector` telemetri:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel:

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>Händelse-och tillstånds typer

Det här avsnittet innehåller exempel på telemetri-händelser och tillstånd som en enhet skickar till ett IoT Central-program.

Följande kodfragment från ett DCM visar definitionen av en `integer` händelse typ:

```json
{
  "@id": "<element id>",
  "@type": [
    "Telemetry",
    "SemanticType/Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

En enhets klient ska skicka händelse data som JSON som ser ut som i följande exempel:

```json
{ "IntegerEvent": 74 }
```

Följande kodfragment från ett DCM visar definitionen av en `integer` tillstånds typ:

```json
{
  "@id": "<element id>",
  "@type": [
    "Telemetry",
    "SemanticType/State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

En enhets klient ska skicka tillstånd som JSON som ser ut som i följande exempel. Möjliga heltals status värden är `1` , `2` eller `3` :

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>Egenskaper

> [!NOTE]
> Nytto Last formaten för egenskaper gäller för program som skapats på eller efter 07/14/2020.

### <a name="primitive-types"></a>Primitiva typer

Det här avsnittet innehåller exempel på primitiva egenskaps typer som en enhet skickar till ett IoT Central-program.

Följande kodfragment från ett DCM visar definitionen av en `boolean` egenskaps typ:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean"
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten:

```json
{ "BooleanProperty": false }
```

Följande kodfragment från ett DCM visar definitionen av en `boolean` egenskaps typ:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long"
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten:

```json
{ "LongProperty": 439 }
```

Följande kodfragment från ett DCM visar definitionen av en `date` egenskaps typ:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date"
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten. `Date` typerna måste vara ISO 8061-kompatibla:

```json
{ "DateProperty": "2020-05-17" }
```

Följande kodfragment från ett DCM visar definitionen av en `duration` egenskaps typ:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration"
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enhetens dubbla varaktigheter måste vara ISO 8601 duration-kompatibel:

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

Följande kodfragment från ett DCM visar definitionen av en `float` egenskaps typ:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float"
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten:

```json
{ "FloatProperty": 1.9 }
```

Följande kodfragment från ett DCM visar definitionen av en `string` egenskaps typ:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string"
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten:

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>Komplexa typer

Det här avsnittet innehåller exempel på komplexa egenskaps typer som en enhet skickar till ett IoT Central-program.

Följande kodfragment från ett DCM visar definitionen av en `geopoint` egenskaps typ:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint"
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten:

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Följande kodfragment från ett DCM visar definitionen av en `Enum` egenskaps typ:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten. Möjliga värden är `0` , `1` och som visas i IoT central som `Item1` , `Item2` och `Item3` :

```json
{ "EnumProperty": 1 }
```

Följande kodfragment från ett DCM visar definitionen av en `Object` egenskaps typ. Det här objektet har två fält med typer `string` och `integer` :

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten:

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

Följande kodfragment från ett DCM visar definitionen av en `vector` egenskaps typ:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector"
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten:

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writeable-property-types"></a>Skrivbara egenskaps typer

Det här avsnittet innehåller exempel på skrivbara egenskaps typer som en enhet tar emot från ett IoT Central-program.

IoT Central förväntar sig ett svar från enheten till skrivbara egenskaps uppdateringar. Svars meddelandet ska innehålla `ac` fälten och `av` . Fältet `ad` är valfritt. Se följande kodfragment för exempel.

`ac` är ett numeriskt fält som använder värdena i följande tabell:

| Värde | Etikett | Description |
| ----- | ----- | ----------- |
| `'ac': 200` | Slutförd | Egenskaps ändrings åtgärden har slutförts. |
| `'ac': 202`  eller `'ac': 201` | Väntar | Egenskaps ändrings åtgärden väntar eller pågår |
| `'ac': 4xx` | Fel | Den begärda egenskaps ändringen var inte giltig eller innehöll ett fel |
| `'ac': 5xx` | Fel | Ett oväntat fel uppstod i enheten vid bearbetning av den begärda ändringen. |

`av` är versions numret som skickas till enheten.

`ad` är en beskrivning av alternativ strängen.

Följande kodfragment från ett DCM visar definitionen av en skrivbar `string` egenskaps typ:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

Enheten får följande nytto Last från IoT Central:

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

Enheten ska skicka följande JSON-nyttolast till IoT Central när uppdateringen har bearbetats. Det här meddelandet innehåller versions numret för den ursprungliga uppdateringen som togs emot från IoT Central. När IoT Central tar emot det här meddelandet markeras egenskapen som **synkroniserad** i användar gränssnittet:

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

Följande kodfragment från ett DCM visar definitionen av en skrivbar `Enum` egenskaps typ:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Enheten får följande nytto Last från IoT Central:

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

Enheten ska skicka följande JSON-nyttolast till IoT Central när uppdateringen har bearbetats. Det här meddelandet innehåller versions numret för den ursprungliga uppdateringen som togs emot från IoT Central. När IoT Central tar emot det här meddelandet markeras egenskapen som **synkroniserad** i användar gränssnittet:

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>Kommandon

### <a name="synchronous-command-types"></a>Synkrona kommando typer

Följande kodfragment från ett DCM visar definitionen av ett synkront kommando som inte har några parametrar och som inte förväntar sig att enheten ska returnera något:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "displayName": {
    "en": "SynchronousCommandBasic"
  },
  "name": "SynchronousCommandBasic"
}
```

Enheten får en tom nytto Last i begäran och ska returnera en tom nytto Last i svaret med en `200` http-svarskod för att indikera att det lyckades.

Följande kodfragment från ett DCM visar definitionen av ett synkront kommando som har en heltals parameter och som förväntar sig att enheten returnerar ett heltals värde:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "SynchronousCommandSimple"
  },
  "name": "SynchronousCommandSimple"
}
```

Enheten får ett heltals värde som begär ande nytto Last. Enheten ska returnera ett heltals värde som svars nytto last med en `200` http-svarskod för att indikera att det lyckades.

Följande kodfragment från ett DCM visar definitionen av ett synkront kommando som har en objekt parameter och som förväntar sig att enheten returnerar ett objekt. I det här exemplet har båda objekten heltals-och sträng fält:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@id": "<element id>",
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@id": "<element id>",
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "displayName": {
    "en": "SynchronousCommandComplex"
  },
  "name": "SynchronousCommandComplex"
}
```

Följande fragment visar ett exempel på begäran om nytto last som skickats till enheten:

```json
{ "Field1": 56, "Field2": "A string value" }
```

I följande kodfragment visas ett exempel på en nytto last för svar som skickas från enheten. Använd en `200` http-svarskod för att indikera att det lyckades:

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="asynchronous-command-types"></a>Asynkrona kommando typer

Följande kodfragment från ett DCM visar definitionen av ett asynkront kommando. Kommandot har en heltals parameter och förväntar sig att enheten returnerar ett heltals värde:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "asynchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "AsynchronousCommandSimple"
  },
  "name": "AsynchronousCommandSimple"
}
```

Enheten får ett heltals värde som begär ande nytto Last. Enheten ska returnera en tom svars nytto last med en `202` http-svarskod för att indikera att enheten har accepterat begäran om asynkron bearbetning.

När enheten har bearbetat bearbetningen av begäran ska den skicka en egenskap till IoT Central som ser ut som i följande exempel. Egenskaps namnet måste vara detsamma som kommando namnet:

```json
{
  "AsynchronousCommandSimple": {
    "value": 87
  }
}
```

## <a name="next-steps"></a>Nästa steg

Som enhets utvecklare, nu när du har lärt dig mer om enhets mallar, kan du [IoT Central](./concepts-get-connected.md) läsa mer om hur du registrerar enheter med IoT Central och hur IoT Central skyddar enhets anslutningar.
