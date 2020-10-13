---
title: Kopiera data från en REST-källa med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från en moln-eller lokal REST-källa till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: jingwang
ms.openlocfilehash: 7b6fa2395e81089e8b4523929a4a7a583b0788a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91360777"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Kopiera data från en REST-slutpunkt genom att använda Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från en REST-slutpunkt. Artikeln bygger på [kopierings aktivitet i Azure Data Factory](copy-activity-overview.md), som visar en översikt över kopierings aktiviteten.

Skillnaden mellan den här REST-anslutningen, [http-kopplingen](connector-http.md)och [webb tabell anslutningen](connector-web-table.md) är:

- **Rest Connector** stöder särskilt kopiering av data från RESTful-API: er; 
- **Http-anslutningen** är generisk för att hämta data från alla http-slutpunkter, till exempel för att ladda ned filen. Innan den här REST-anslutningen blir tillgänglig kan du välja att använda HTTP-anslutning för att kopiera data från RESTful-API, som stöds men mindre funktions jämförelser till REST Connector.
- **Webb tabells koppling** extraherar tabell innehåll från en HTML-webbsida.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från en REST-källa till alla mottagar data lager som stöds. En lista över data lager som kopierings aktiviteten stöder som källor och mottagare finns i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Mer specifikt stöder den här generiska REST-anslutningen:

- Hämta data från en REST-slutpunkt med metoderna **Get** eller **post** .
- Hämta data genom att använda någon av följande autentiseringar: **Anonym**, **grundläggande**, **AAD-tjänstens huvud namn**och **hanterade identiteter för Azure-resurser**.
- **[Sid brytning](#pagination-support)** i REST-API: erna.
- Kopiera REST JSON-svaret [som det är](#export-json-response-as-is) eller parsa det med hjälp av [schema mappning](copy-activity-schema-and-type-mapping.md#schema-mapping). Endast svars nytto Last i **JSON** stöds.

> [!TIP]
> Om du vill testa en begäran om data hämtning innan du konfigurerar REST-anslutningen i Data Factory kan du läsa om API-specifikationen för sidhuvuds-och text krav. Du kan använda verktyg som Postman eller webbläsare för att validera.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory entiteter som är speciella för REST-anslutningen.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den REST-länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **RestService**. | Ja |
| url | Bas-URL: en för REST-tjänsten. | Ja |
| enableServerCertificateValidation | Huruvida TLS/SSL-certifikat på Server sidan ska verifieras vid anslutning till slut punkten. | Inga<br /> (Standardvärdet är **Sant**) |
| authenticationType | Typ av autentisering som används för att ansluta till REST-tjänsten. Tillåtna värden är **Anonymous**, **Basic**, **AadServicePrincipal**och **ManagedServiceIdentity**. Se motsvarande avsnitt nedan om du vill ha fler egenskaper respektive exempel. | Ja |
| connectVia | [Integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om detta inte anges använder den här egenskapen standard Azure Integration Runtime. |Inga |

### <a name="use-basic-authentication"></a>Använd grundläggande autentisering

Ange egenskapen **authenticationType** som **Basic**. Förutom de allmänna egenskaper som beskrivs i föregående avsnitt anger du följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| userName | Användar namnet som används för att få åtkomst till REST-slutpunkten. | Ja |
| password | Användarens lösen ord (värdet **username** ). Markera det här fältet som en **SecureString** -typ för att lagra det på ett säkert sätt i Data Factory. Du kan också [referera till en hemlighet som lagrats i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |

**Exempel**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
            "userName": "<user name>",
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

### <a name="use-aad-service-principal-authentication"></a>Använd AAD-tjänstens huvud namns autentisering

Ange egenskapen **authenticationType** till **AadServicePrincipal**. Förutom de allmänna egenskaper som beskrivs i föregående avsnitt anger du följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| servicePrincipalId | Ange det Azure Active Directory programmets klient-ID. | Ja |
| servicePrincipalKey | Ange Azure Active Directory programmets nyckel. Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| tenant | Ange den klient information (domän namn eller klient-ID) som programmet finns under. Hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Ja |
| aadResourceId | Ange den AAD-resurs som du begär auktorisering för, till exempel `https://management.core.windows.net` .| Ja |
| azureCloudType | För tjänstens huvud namns autentisering anger du vilken typ av Azure-moln miljö som ditt AAD-program är registrerat på. <br/> Tillåtna värden är **AzurePublic**, **AzureChina**, **azureusgovernment eller**och **AzureGermany**. Som standard används data fabrikens moln miljö. | Inga |

**Exempel**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Använda hanterade identiteter för Azure-resurser-autentisering

Ange egenskapen **authenticationType** till **ManagedServiceIdentity**. Förutom de allmänna egenskaper som beskrivs i föregående avsnitt anger du följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| aadResourceId | Ange den AAD-resurs som du begär auktorisering för, till exempel `https://management.core.windows.net` .| Ja |

**Exempel**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

Det här avsnittet innehåller en lista över egenskaper som stöds av REST-datauppsättningen. 

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar och länkade tjänster](concepts-datasets-linked-services.md). 

Följande egenskaper stöds för att kopiera data från REST:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens **typ** -egenskap måste anges till **RestResource**. | Ja |
| relativeUrl | En relativ URL till den resurs som innehåller data. När den här egenskapen inte anges används endast den URL som anges i den länkade tjänst definitionen. HTTP-anslutningen kopierar data från den kombinerade URL: en: `[URL specified in linked service]/[relative URL specified in dataset]` . | Inga |

Om du har angett `requestMethod` , `additionalHeaders` `requestBody` och `paginationRules` i data uppsättning, stöds den fortfarande som den är, men du rekommenderas att använda den nya modellen i aktivitets källan.

**Exempel:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera aktivitets egenskaper

Det här avsnittet innehåller en lista över egenskaper som REST-källan stöder.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen för kopierings aktivitets källan måste anges till **RestSource**. | Ja |
| requestMethod | HTTP-metoden. Tillåtna värden är **Get** (standard) och **post**. | Inga |
| additionalHeaders | Ytterligare rubriker för HTTP-begäran. | Inga |
| requestBody | Bröd texten för HTTP-begäran. | Inga |
| paginationRules | Sid brytnings regler för att skapa nästa sida begär Anden. Mer information finns i avsnittet om [sid brytnings stöd](#pagination-support) . | Inga |
| httpRequestTimeout | Timeout ( **TimeSpan** -värdet) för http-begäran för att få ett svar. Det här värdet är tids gränsen för att få ett svar, inte tids gränsen för att läsa svars data. Standardvärdet är **00:01:40**.  | Inga |
| requestInterval | Vänte tiden innan begäran skickas för nästa sida. Standardvärdet är **00:00:01** |  Inga |

>[!NOTE]
>REST Connector ignorerar alla "Accept"-huvud som anges i `additionalHeaders` . Eftersom REST Connector endast stöder svar i JSON genererar den automatiskt ett huvud av `Accept: application/json` .

**Exempel 1: använda Get-metoden med sid brytning**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exempel 2: använda post-metoden**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Stöd för sid brytning

Normalt begränsar REST API dess svars nytto Last storlek för en enskild begäran under ett rimligt antal. När stora mängder data skulle returneras delas resultatet in på flera sidor och det krävs att anropare skickar efterföljande begär Anden för att hämta nästa sida i resultatet. Vanligt vis är begäran för en sida dynamisk och består av den information som returneras från svars sidan föregående.

Denna generiska REST-anslutning har stöd för följande sid brytnings mönster: 

* Nästa begär ande absoluta eller relativa URL = egenskap svärdet i den aktuella svars texten
* Nästa begär ande absoluta eller relativa URL = huvud värde i aktuella svarshuvuden
* Nästa förfrågans frågeparameter = egenskaps värde i den aktuella svars texten
* Nästa förfrågans frågeparameter = header-värde i aktuella svarshuvuden
* Nästa begär ande rubrik = egenskaps värde i den aktuella svars texten
* Nästa begär ande rubrik = huvud värde i aktuella svarshuvuden

**Sid brytnings regler** definieras som en ord lista i data uppsättningen, som innehåller ett eller flera Skift läges känsliga nyckel/värde-par. Konfigurationen kommer att användas för att generera begäran från den andra sidan. Kopplingen slutar att gå igenom när den får HTTP-statuskod 204 (inget innehåll) eller något av JSONPath-uttrycken i "paginationRules" returnerar null.

**Nycklar som stöds** i sid brytnings regler:

| Tangent | Beskrivning |
|:--- |:--- |
| AbsoluteUrl | Anger den URL som utfärdar nästa begäran. Det kan **antingen vara en absolut URL eller en relativ URL**. |
| QueryParameters. *request_query_parameter* ELLER QueryParameters ["request_query_parameter"] | "request_query_parameter" är användardefinierad, som refererar till ett parameter namn för en fråga i nästa HTTP-begärande-URL. |
| Sidhuvud. *request_header* ELLER huvuden ["request_header"] | "request_header" är användardefinierad, som refererar till ett huvud namn i nästa HTTP-begäran. |

**Värden som stöds** i sid brytnings regler:

| Värde | Beskrivning |
|:--- |:--- |
| Sidhuvud. *response_header* ELLER huvuden ["response_header"] | "response_header" är användardefinierad, som refererar till ett rubrik namn i det aktuella HTTP-svaret och värdet som ska användas för nästa begäran. |
| Ett JSONPath-uttryck som börjar med "$" (som representerar roten i svars texten) | Svars texten får bara innehålla ett JSON-objekt. JSONPath-uttrycket ska returnera ett enda primitivt värde som ska användas för nästa begäran. |

**Exempel:**

Facebook Graph API returnerar svar i följande struktur, i vilket fall visas nästa sidas URL i ***sid indelning. nästa***:

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

Motsvarande käll konfiguration för REST kopierings aktivitet är i synnerhet följande `paginationRules` :

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="use-oauth"></a>Använda OAuth
I det här avsnittet beskrivs hur du använder en lösnings mall för att kopiera data från REST Connector till Azure Data Lake Storage i JSON-format med OAuth. 

### <a name="about-the-solution-template"></a>Om lösnings mal len

Mallen innehåller två aktiviteter:
- **Webb** aktivitet hämtar Bearer-token och skickar den sedan till efterföljande kopierings aktivitet som auktorisering.
- **Kopierings** aktiviteten kopierar data från REST till Azure Data Lake Storage.

Mallen definierar två parametrar:
- **SinkContainer** är sökvägen till rot katalogen där data kopieras till i din Azure Data Lake Storage. 
- **SinkDirectory** är katalog Sök vägen under roten där data kopieras till i din Azure Data Lake Storage. 

### <a name="how-to-use-this-solution-template"></a>Så här använder du den här lösnings mal len

1. Gå till **kopian från rest eller http med OAuth-** mall. Skapa en ny anslutning för käll anslutning. 
    ![Skapa nya anslutningar](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    Nedan visas viktiga steg för inställningar för ny länkad tjänst (REST):
    
     1. Under **bas-URL**anger du URL-parametern för din egen käll REST-tjänst. 
     2. Som **Autentiseringstyp**väljer du *Anonym*.
        ![Ny REST-anslutning](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. Skapa en ny anslutning för mål anslutning.  
    ![Ny Gen2-anslutning](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. Välj **Använd den här mallen**.
    ![Använd den här mallen](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. Du ser pipelinen som skapades på det sätt som visas i följande exempel:  ![ skärm bilden visar pipelinen som skapats från mallen.](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. Välj **webb** aktivitet. I **Inställningar**anger du motsvarande **URL**, **metod**, **rubriker**och **brödtext** för att hämta OAuth Bearer-token från inloggnings-API: t för den tjänst som du vill kopiera data från. Plats hållaren i mallen visar ett exempel på Azure Active Directory (AAD) OAuth. Obs!-autentisering med AAD stöds internt av REST Connector. här är bara ett exempel på ett OAuth-flöde. 

    | Egenskap | Beskrivning |
    |:--- |:--- |:--- |
    | URL |Ange URL: en som OAuth Bearer-token ska hämtas från. i exemplet här är det t. ex. https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | Metod | HTTP-metoden. Tillåtna värden är **post** och **Get**. | 
    | Rubriker | Rubriken är användardefinierad, som refererar till ett rubrik namn i HTTP-begäran. | 
    | Brödtext | Bröd texten för HTTP-begäran. | 

    ![Pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. I **Kopiera data** aktivitet väljer du fliken *källa* . du kan se att Bearer-token (access_token) som hämtades från föregående steg skulle skickas till aktiviteten Kopiera data som **auktorisering** under ytterligare huvuden. Bekräfta inställningarna för följande egenskaper innan du startar en pipeline-körning.

    | Egenskap | Beskrivning |
    |:--- |:--- |:--- | 
    | Metod för begäran | HTTP-metoden. Tillåtna värden är **Get** (standard) och **post**. | 
    | Ytterligare rubriker | Ytterligare rubriker för HTTP-begäran.| 

   ![Kopiera käll autentisering](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. Välj **Felsök**, ange **parametrarna**och välj sedan **Slutför**.
   ![Pipeline-körning](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. När pipeline-körningen har slutförts ser du resultatet som liknar följande exempel: ![ pipeline-körnings resultat](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. Klicka på ikonen "utdata" för webactivity i kolumnen **åtgärder** , så visas access_token som returnerades av tjänsten.

   ![Utdata för token](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. Klicka på ikonen "indataport" för CopyActivity i kolumnen **åtgärder** så ser du att access_token som hämtats av webactivity skickas till CopyActivity för autentisering. 

    ![Token-indatamängd](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >Om du vill undvika att token loggas som oformaterad text aktiverar du "säker utdata" i webb aktivitet och "säker indata" i kopierings aktiviteten.


## <a name="export-json-response-as-is"></a>Exportera JSON-svar som-är

Du kan använda den här REST-anslutningen för att exportera REST API JSON-svaret som är till olika filbaserade butiker. För att få en sådan oberoende kopia kan du hoppa över avsnittet "struktur" (kallas även *schema*) i data uppsättning och schema mappning i kopierings aktiviteten.

## <a name="schema-mapping"></a>Schemamappning

Information om hur du kopierar data från REST-slutpunkt till tabell mottagare finns i [schema mappning](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Nästa steg

En lista över data lager som kopierings aktiviteten stöder som källor och handfat i Azure Data Factory finns i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
