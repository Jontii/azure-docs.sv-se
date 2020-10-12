---
title: Kopiera data från/till SAP-molnet för kunden
description: Lär dig hur du kopierar data från SAP Cloud för kunder till mottagar data lager (eller) från käll data lager som stöds till SAP-molnet för kunder med hjälp av Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/12/2020
ms.openlocfilehash: 3874d3b2b0938b6fd0f763b42ef15f8250b42f1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87529627"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Kopiera data från SAP-molnet för kunden (C4C) med hjälp av Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från/till SAP-moln för kunder (C4C). Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

>[!TIP]
>Information om hur du hanterar ADF: s övergripande support på SAP data integrations scenario finns i [SAP-dataintegrering med Azure Data Factory whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) med detaljerad introduktion till varje SAP-koppling, comparsion och vägledning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Det här SAP-molnet för kund Connector stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från SAP Cloud för kunden till alla mottagar data lager som stöds, eller kopiera data från alla käll data lager som stöds till SAP-molnet för kunden. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt gör den här anslutningen att Azure Data Factory kopiera data från/till SAP-molnet för kunder, inklusive SAP-molnet för försäljning, SAP Cloud for service och SAP Cloud för sociala engagemang-lösningar.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är specifika för SAP Cloud för kund Connector.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för SAP Cloud för kund länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **SapCloudForCustomer**. | Ja |
| url | URL: en för SAP C4C OData-tjänsten. | Ja |
| användarnamn | Ange användar namnet för att ansluta till SAP-C4C. | Ja |
| password | Ange lösen ordet för det användar konto som du har angett för användar namnet. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Om inget värde anges används standard Azure Integration Runtime. | Nej för källa, Ja för mottagare |

>[!IMPORTANT]
>Om du vill kopiera data till SAP-molnet för kunden kan du uttryckligen [skapa en Azure IR](create-azure-integration-runtime.md#create-azure-ir) med en plats nära ditt SAP-moln för kunden och associera i den länkade tjänsten som i följande exempel:

**Exempel:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av SAP Cloud för kund data uppsättning.

Om du vill kopiera data från SAP-molnet för kunden anger du egenskapen type för data uppsättningen till **SapCloudForCustomerResource**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **SapCloudForCustomerResource** |Ja |
| path | Ange sökvägen till SAP C4C OData-entiteten. |Ja |

**Exempel:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av SAP Cloud för kund källa.

### <a name="sap-c4c-as-source"></a>SAP-C4C som källa

Om du vill kopiera data från SAP-molnet för kunden anger du käll typen i kopierings aktiviteten till **SapCloudForCustomerSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **SapCloudForCustomerSource**  | Ja |
| DocumentDB | Ange anpassad OData-fråga för att läsa data. | Inga |
| httpRequestTimeout | Timeout ( **TimeSpan** -värdet) för http-begäran för att få ett svar. Det här värdet är tids gränsen för att få ett svar, inte tids gränsen för att läsa svars data. Om inget värde anges är standardvärdet **00:30:00** (30 minuter). | Inga |

Exempel fråga för att hämta data för en angiven dag: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP-C4C som mottagare

Om du vill kopiera data till SAP-molnet för kunden ställer du in mottagar typen i kopierings aktiviteten till **SapCloudForCustomerSink**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **mottagare** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **SapCloudForCustomerSink**  | Ja |
| writeBehavior | Åtgärdens Skriv funktion. Kan vara "Infoga", "uppdatera". | Nej. Standard "Infoga". |
| writeBatchSize | Batch-storlek för Skriv åtgärd. Batchstorleken för att få bästa prestanda kan vara olika för olika tabeller och servrar. | Nej. Standard 10. |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Data typs mappning för SAP-moln för kund

När du kopierar data från SAP-molnet för kunden används följande mappningar från SAP Cloud för kund data typer för att Azure Data Factory interimistiska data typer. Se [mappningar av schema och data typer](copy-activity-schema-and-type-mapping.md) för att lära dig mer om hur kopierings aktiviteten mappar käll schema och datatyp till mottagaren.

| Data typen SAP C4C OData | Data fabrikens interimistiska datatyp |
|:--- |:--- |
| EDM. Binary | Byte [] |
| Edm.Boolean | Bool |
| EDM. byte | Byte [] |
| EDM. DateTime | DateTime |
| EDM. decimal | Decimal |
| Edm.Double | Double |
| EDM. Single | Enkel |
| EDM. GUID | GUID |
| EDM. Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| EDM. SByte | Int16 |
| Edm.String | Sträng |
| EDM. Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
