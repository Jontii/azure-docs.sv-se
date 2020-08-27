---
title: Microsoft Graph bindningar för Azure Functions
description: Förstå hur du använder Microsoft Graph utlösare och bindningar i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: d10b36047959299f5b66da5fb16beef8a591a983
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962850"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Microsoft Graph bindningar för Azure Functions

> [!IMPORTANT]
> Microsoft Graph för hands versions bindningar är nu föråldrade. Mer information om hur du använder Microsoft Graph med Azure Functions finns i självstudierna [Build Azure Functions med Microsoft Graph](https://docs.microsoft.com/graph/tutorials/azure-functions) .

Den här artikeln förklarar hur du konfigurerar och arbetar med Microsoft Graph utlösare och bindningar i Azure Functions. Med dessa kan du använda Azure Functions för att arbeta med data, insikter och händelser från [Microsoft Graph](https://developer.microsoft.com/graph).

Microsoft Graph-tillägget innehåller följande bindningar:
- Med en [autentisering med token för token-token](#token-input) kan du interagera med valfri Microsoft Graph-API.
- Med en [data bindning för Excel-tabell](#excel-input) kan du läsa data från Excel.
- Med en [Excel-tabells utgående bindning](#excel-output) kan du ändra Excel-data.
- Med en [data bindning för OneDrive-filer](#onedrive-input) kan du läsa filer från OneDrive.
- Med en [bindning för OneDrive-filutdata](#onedrive-output) kan du skriva till filer i OneDrive.
- Med en [utgående bindning för Outlook-meddelanden](#outlook-output) kan du skicka e-post via Outlook.
- En samling [Microsoft Graph webhook-utlösare och bindningar](#webhooks) gör att du kan reagera på händelser från Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Microsoft Graph-bindningar finns för närvarande i för hands version för Azure Functions version 2. x och högre. De stöds inte i functions version 1. x.

## <a name="packages"></a>Paket

Indata-bindningen för auth-token finns i paketet [Microsoft. Azure. WebJobs. Extensions. AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet. De andra Microsoft Graph-bindningarna finns i paketet [Microsoft. Azure. WebJobs. Extensions. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) . Käll koden för paketen finns i GitHub-lagringsplatsen [Azure-Functions-microsoftgraph-Extensions](https://github.com/Azure/azure-functions-microsoftgraph-extension/) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Konfigurera tilläggen

> [!IMPORTANT]
> Microsoft Graph för hands versions bindningar är nu föråldrade. Mer information om hur du använder Microsoft Graph med Azure Functions finns i självstudierna [Build Azure Functions med Microsoft Graph](https://docs.microsoft.com/graph/tutorials/azure-functions) .

Microsoft Graph bindningar är tillgängliga via _bindnings tillägg_. Bindnings tillägg är valfria komponenter till Azure Functions Runtime. I det här avsnittet visas hur du konfigurerar Microsoft Graph-och auth token-tillägg.

### <a name="enabling-functions-20"></a>Aktivera funktioner 2,0

Bindnings tillägg är bara tillgängliga för Azure Functions 2,0. 

Information om hur du ställer in en Function-app för att använda 2,0-versionen av Functions-körningen finns i [så här riktar du Azure Functions runtime-versioner](set-runtime-version.md).

### <a name="installing-the-extension"></a>Installerar tillägget

Om du vill installera ett tillägg från Azure Portal navigerar du antingen till en mall eller bindning som refererar till den. Skapa en ny funktion och välj scenariot "Microsoft Graph" i fönstret Mallval. Välj en av mallarna i det här scenariot. Du kan också navigera till fliken "integrera" i en befintlig funktion och välja en av de bindningar som beskrivs i den här artikeln.

I båda fallen visas en varning som anger vilket tillägg som ska installeras. Klicka på **Installera** för att hämta tillägget. Varje tillägg måste bara installeras en gång per Function-app. 

> [!Note] 
> Installations processen i portalen kan ta upp till 10 minuter i en förbruknings plan.

Om du använder Visual Studio kan du hämta tilläggen genom [att installera NuGet-paketen som listas tidigare i den här artikeln](#packages).

### <a name="configuring-authentication--authorization"></a>Konfigurera autentisering/auktorisering

De bindningar som beskrivs i den här artikeln kräver att en identitet används. Detta gör att Microsoft Graph kan genomdriva behörigheter och granska interaktioner. Identiteten kan vara en användare som har åtkomst till ditt program eller själva programmet. Konfigurera den här identiteten genom att konfigurera [App Service autentisering/auktorisering](../app-service/overview-authentication-authorization.md) med Azure Active Directory. Du måste också begära eventuella resurs behörigheter som dina funktioner kräver.

> [!Note] 
> Microsoft Graph-tillägget har endast stöd för Azure AD-autentisering. Användarna måste logga in med ett arbets-eller skol konto.

Om du använder Azure Portal visas en varning under prompten för att installera tillägget. Varningen gör att du kan konfigurera App Service autentisering/auktorisering och begära alla behörigheter som krävs för mallen eller bindningen. Klicka på **Konfigurera Azure AD nu** eller **Lägg till behörigheter nu** vid behov.



<a name="token-input"></a>
## <a name="auth-token"></a>Auth-token

> [!IMPORTANT]
> Microsoft Graph för hands versions bindningar är nu föråldrade. Mer information om hur du använder Microsoft Graph med Azure Functions finns i självstudierna [Build Azure Functions med Microsoft Graph](https://docs.microsoft.com/graph/tutorials/azure-functions) .

Indataporten för token för token hämtar en Azure AD-token för en specifik resurs och ger den till din kod som en sträng. Resursen kan vara valfri för vilken programmet har behörigheter. 

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#auth-token---example)
* [Attribut](#auth-token---attributes)
* [Konfiguration](#auth-token---configuration)
* [Användning](#auth-token---usage)

### <a name="auth-token---example"></a>Auth-token-exempel

Se språkspecifika exempel:

* [C#-skript (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Auth-token – skript exempel för C#

I följande exempel hämtas information om användar profiler.

*function.jsi* filen definierar en http-utlösare med en token för indata-bindning:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C#-skript koden använder token för att göra ett HTTP-anrop till Microsoft Graph och returnerar resultatet:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers;
using Microsoft.Extensions.Logging; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, ILogger log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>Auth-token – exempel på JavaScript-skript

I följande exempel hämtas information om användar profiler.

*function.jsi* filen definierar en http-utlösare med en token för indata-bindning:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript-koden använder token för att göra ett HTTP-anrop till Microsoft Graph och returnerar resultatet.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```

### <a name="auth-token---attributes"></a>Auth-token-attribut

Använd attributet [token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) i [klass bibliotek i C#](functions-dotnet-class-library.md).

### <a name="auth-token---configuration"></a>Auth-token – konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `Token` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**| saknas |Obligatoriskt – variabel namnet som används i funktions koden för auth-token. Se [använda en token för autentisering av autentiseringsuppgifter från kod](#token-input-code).|
|**bastyp**| saknas |Required-måste anges till `token` .|
|**position**| saknas |Required-måste anges till `in` .|
|**Autentiseringsidentitet**|**Identitet**|Krävs – den identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [http-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> – Använder identiteten för en tidigare inloggad användare med angivet ID. Se <code>userId</code> egenskapen.</li><li><code>userFromToken</code> – Använder den identitet som representeras av angiven token. Se <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> – Använder appens identitet.</li></ul>|
|**userId**|**UserId**  |Krävs om och endast om _identitet_ har angetts till `userFromId` . Ett huvud konto-ID som är kopplat till en tidigare inloggad användare.|
|**userToken**|**UserToken**|Krävs om och endast om _identitet_ har angetts till `userFromToken` . En giltig token för Function-appen. |
|**Resurs**|**klusterresursen**|Krävs – en Azure AD-resurs-URL för vilken token begärs.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Auth-token – användning

Själva bindningen kräver inga Azure AD-behörigheter, men beroende på hur token används kan du behöva begära ytterligare behörigheter. Kontrol lera kraven för den resurs som du tänker komma åt med token.

Token visas alltid för kod som en sträng.

> [!Note]
> När du utvecklar lokalt med något `userFromId` av `userFromToken` eller- `userFromRequest` alternativen kan obligatorisk token [hämtas manuellt](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) och anges i `X-MS-TOKEN-AAD-ID-TOKEN` begär ande huvudet från ett anropande klient program.


<a name="excel-input"></a>
## <a name="excel-input"></a>Excel-Indatatyp

> [!IMPORTANT]
> Microsoft Graph för hands versions bindningar är nu föråldrade. Mer information om hur du använder Microsoft Graph med Azure Functions finns i självstudierna [Build Azure Functions med Microsoft Graph](https://docs.microsoft.com/graph/tutorials/azure-functions) .

Excel-tabellens indataparameter läser innehållet i en Excel-tabell som är lagrad i OneDrive.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#excel-input---example)
* [Attribut](#excel-input---attributes)
* [Konfiguration](#excel-input---configuration)
* [Användning](#excel-input---usage)

### <a name="excel-input---example"></a>Excel-ingångs exempel

Se språkspecifika exempel:

* [C#-skript (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel-indatamängd – C#-skript exempel

Följande *function.jsi* filen definierar en http-utlösare med en Excel-inkommande bindning:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Följande C#-skript kod läser innehållet i den angivna tabellen och returnerar dem till användaren:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, string[][] excelTableData, ILogger log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Excel-indatamängd – JavaScript-exempel

Följande *function.jsi* filen definierar en http-utlösare med en Excel-inkommande bindning:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Följande JavaScript-kod läser innehållet i den angivna tabellen och returnerar dem till användaren.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Indataceller för Excel-attribut

Använd [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) -attributet i [klass bibliotek i C#](functions-dotnet-class-library.md).

### <a name="excel-input---configuration"></a>Excel-indatamängd – konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `Excel` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**| saknas |Obligatoriskt – variabel namnet som används i funktions koden för Excel-tabellen. Se [använda en datanings bindning för Excel-tabeller från kod](#excel-input-code).|
|**bastyp**| saknas |Required-måste anges till `excel` .|
|**position**| saknas |Required-måste anges till `in` .|
|**Autentiseringsidentitet**|**Identitet**|Krävs – den identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [http-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> – Använder identiteten för en tidigare inloggad användare med angivet ID. Se <code>userId</code> egenskapen.</li><li><code>userFromToken</code> – Använder den identitet som representeras av angiven token. Se <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> – Använder appens identitet.</li></ul>|
|**userId**|**UserId**  |Krävs om och endast om _identitet_ har angetts till `userFromId` . Ett huvud konto-ID som är kopplat till en tidigare inloggad användare.|
|**userToken**|**UserToken**|Krävs om och endast om _identitet_ har angetts till `userFromToken` . En giltig token för Function-appen. |
|**sökväg**|**Sökväg**|Obligatoriskt – sökvägen i OneDrive till Excel-arbetsboken.|
|**worksheetName**|**WorksheetName**|Det kalkyl blad som tabellen finns i.|
|**tableName**|**TableName**|Namnet på tabellen. Om inget anges används innehållet i kalkyl bladet.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel-ingångs användning

Den här bindningen kräver följande Azure AD-behörigheter:

|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Läsa användarfiler|

Bindningen visar följande typer av .NET-funktioner:
- sträng [] []
- Microsoft. Graph. WorkbookTable
- Anpassade objekt typer (med bindning av strukturella modeller)










<a name="excel-output"></a>
## <a name="excel-output"></a>Excel-utdata

Utgående bindning för Excel ändrar innehållet i en Excel-tabell som lagras i OneDrive.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#excel-output---example)
* [Attribut](#excel-output---attributes)
* [Konfiguration](#excel-output---configuration)
* [Användning](#excel-output---usage)

### <a name="excel-output---example"></a>Excel-utdata-exempel

Se språkspecifika exempel:

* [C#-skript (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel-utdata – C#-skript exempel

I följande exempel läggs rader till i en Excel-tabell.

*function.jsi* -filen definierar en http-utlösare med en utgående bindning för Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C#-skript koden lägger till en ny rad i tabellen (antas vara en kolumn) baserat på indatatyper från frågesträngen:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, ILogger log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

#### <a name="excel-output---javascript-example"></a>Excel-utdata – JavaScript-exempel

I följande exempel läggs rader till i en Excel-tabell.

*function.jsi* -filen definierar en http-utlösare med en utgående bindning för Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Följande JavaScript-kod lägger till en ny rad i tabellen (antas vara en kolumn) baserat på indatamängden från frågesträngen.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Utdata-attribut för Excel

Använd [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) -attributet i [klass bibliotek i C#](functions-dotnet-class-library.md).

### <a name="excel-output---configuration"></a>Excel-utdata – konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `Excel` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**| saknas |Obligatoriskt – variabel namnet som används i funktions koden för auth-token. Se [använda en Excel-tabell utgående bindning från kod](#excel-output-code).|
|**bastyp**| saknas |Required-måste anges till `excel` .|
|**position**| saknas |Required-måste anges till `out` .|
|**Autentiseringsidentitet**|**Identitet**|Krävs – den identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [http-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> – Använder identiteten för en tidigare inloggad användare med angivet ID. Se <code>userId</code> egenskapen.</li><li><code>userFromToken</code> – Använder den identitet som representeras av angiven token. Se <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> – Använder appens identitet.</li></ul>|
|**UserId** |**userId** |Krävs om och endast om _identitet_ har angetts till `userFromId` . Ett huvud konto-ID som är kopplat till en tidigare inloggad användare.|
|**userToken**|**UserToken**|Krävs om och endast om _identitet_ har angetts till `userFromToken` . En giltig token för Function-appen. |
|**sökväg**|**Sökväg**|Obligatoriskt – sökvägen i OneDrive till Excel-arbetsboken.|
|**worksheetName**|**WorksheetName**|Det kalkyl blad som tabellen finns i.|
|**tableName**|**TableName**|Namnet på tabellen. Om inget anges används innehållet i kalkyl bladet.|
|**Uppdateringstyp**|**Uppdateringstyp**|Krävs – den typ av ändring som ska göras i tabellen. Kan vara något av följande värden:<ul><li><code>update</code> – Ersätter innehållet i tabellen i OneDrive.</li><li><code>append</code> – Lägger till nytto lasten i slutet av tabellen i OneDrive genom att skapa nya rader.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel-utdata-användning

Den här bindningen kräver följande Azure AD-behörigheter:

|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Ha fullständig åtkomst till användarfiler|

Bindningen visar följande typer av .NET-funktioner:
- sträng [] []
- Newtonsoft.Jspå. LINQ. JObject
- Microsoft. Graph. WorkbookTable
- Anpassade objekt typer (med bindning av strukturella modeller)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Fil indata

> [!IMPORTANT]
> Microsoft Graph för hands versions bindningar är nu föråldrade. Mer information om hur du använder Microsoft Graph med Azure Functions finns i självstudierna [Build Azure Functions med Microsoft Graph](https://docs.microsoft.com/graph/tutorials/azure-functions) .

Data bindningen i OneDrive läser innehållet i en fil som lagras i OneDrive.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#file-input---example)
* [Attribut](#file-input---attributes)
* [Konfiguration](#file-input---configuration)
* [Användning](#file-input---usage)

### <a name="file-input---example"></a>Fil indata-exempel

Se språkspecifika exempel:

* [C#-skript (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Fil indata – C#-skript exempel

I följande exempel läses en fil som lagras i OneDrive.

*function.jsi* filen definierar en http-utlösare med en inkommande bindning för OneDrive-filer:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C#-skript koden läser filen som anges i frågesträngen och loggar dess längd:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Fil indata – JavaScript-exempel

I följande exempel läses en fil som lagras i OneDrive.

*function.jsi* filen definierar en http-utlösare med en inkommande bindning för OneDrive-filer:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Följande JavaScript-kod läser filen som anges i frågesträngen och returnerar dess längd.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Fildata-attribut

Använd attributet [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) i [C#-klass bibliotek](functions-dotnet-class-library.md).

### <a name="file-input---configuration"></a>Fil indata-konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `OneDrive` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**| saknas |Obligatoriskt – variabel namnet som används i funktions koden för filen. Se [använda en OneDrive-indata-bindning från kod](#onedrive-input-code).|
|**bastyp**| saknas |Required-måste anges till `onedrive` .|
|**position**| saknas |Required-måste anges till `in` .|
|**Autentiseringsidentitet**|**Identitet**|Krävs – den identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [http-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> – Använder identiteten för en tidigare inloggad användare med angivet ID. Se <code>userId</code> egenskapen.</li><li><code>userFromToken</code> – Använder den identitet som representeras av angiven token. Se <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> – Använder appens identitet.</li></ul>|
|**userId**|**UserId**  |Krävs om och endast om _identitet_ har angetts till `userFromId` . Ett huvud konto-ID som är kopplat till en tidigare inloggad användare.|
|**userToken**|**UserToken**|Krävs om och endast om _identitet_ har angetts till `userFromToken` . En giltig token för Function-appen. |
|**sökväg**|**Sökväg**|Obligatoriskt – sökvägen i OneDrive till filen.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Fil indata-användning

Den här bindningen kräver följande Azure AD-behörigheter:

|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Läsa användarfiler|

Bindningen visar följande typer av .NET-funktioner:
- byte[]
- Dataström
- sträng
- Microsoft. Graph. DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Fil utdata

> [!IMPORTANT]
> Microsoft Graph för hands versions bindningar är nu föråldrade. Mer information om hur du använder Microsoft Graph med Azure Functions finns i självstudierna [Build Azure Functions med Microsoft Graph](https://docs.microsoft.com/graph/tutorials/azure-functions) .

Bindnings bindningen för OneDrive-filer ändrar innehållet i en fil som lagras i OneDrive.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#file-output---example)
* [Attribut](#file-output---attributes)
* [Konfiguration](#file-output---configuration)
* [Användning](#file-output---usage)

### <a name="file-output---example"></a>Fil utdata-exempel

Se språkspecifika exempel:

* [C#-skript (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Fil utdata – C#-skript exempel

Följande exempel skriver till en fil som lagras i OneDrive.

*function.jsi* -filen definierar en http-utlösare med en bindning för OneDrive-utdata:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C#-skript koden hämtar text från frågesträngen och skriver den till en textfil (FunctionsTest.txt som definieras i föregående exempel) i roten för anroparens OneDrive:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, ILogger log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    myOneDriveFile.Close();
    return;
}
```

#### <a name="file-output---javascript-example"></a>Fil utdata – JavaScript-exempel

Följande exempel skriver till en fil som lagras i OneDrive.

*function.jsi* -filen definierar en http-utlösare med en bindning för OneDrive-utdata:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript-koden hämtar text från frågesträngen och skriver den till en textfil (FunctionsTest.txt som definieras i konfigurationen ovan) i roten för anroparens OneDrive.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Filutdata-attribut

Använd attributet [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) i [C#-klass bibliotek](functions-dotnet-class-library.md).

### <a name="file-output---configuration"></a>Filutdata-konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `OneDrive` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**| saknas |Obligatoriskt – variabel namnet som används i funktions koden för filen. Se [använda en OneDrive-filutgående bindning från kod](#onedrive-output-code).|
|**bastyp**| saknas |Required-måste anges till `onedrive` .|
|**position**| saknas |Required-måste anges till `out` .|
|**Autentiseringsidentitet**|**Identitet**|Krävs – den identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [http-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> – Använder identiteten för en tidigare inloggad användare med angivet ID. Se <code>userId</code> egenskapen.</li><li><code>userFromToken</code> – Använder den identitet som representeras av angiven token. Se <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> – Använder appens identitet.</li></ul>|
|**UserId** |**userId** |Krävs om och endast om _identitet_ har angetts till `userFromId` . Ett huvud konto-ID som är kopplat till en tidigare inloggad användare.|
|**userToken**|**UserToken**|Krävs om och endast om _identitet_ har angetts till `userFromToken` . En giltig token för Function-appen. |
|**sökväg**|**Sökväg**|Obligatoriskt – sökvägen i OneDrive till filen.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Fil utmatning – användning

Den här bindningen kräver följande Azure AD-behörigheter:

|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Ha fullständig åtkomst till användarfiler|

Bindningen visar följande typer av .NET-funktioner:
- byte[]
- Dataström
- sträng
- Microsoft. Graph. DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Outlook-utdata

> [!IMPORTANT]
> Microsoft Graph för hands versions bindningar är nu föråldrade. Mer information om hur du använder Microsoft Graph med Azure Functions finns i självstudierna [Build Azure Functions med Microsoft Graph](https://docs.microsoft.com/graph/tutorials/azure-functions) .

Utgående bindning för Outlook-meddelanden skickar ett e-postmeddelande via Outlook.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#outlook-output---example)
* [Attribut](#outlook-output---attributes)
* [Konfiguration](#outlook-output---configuration)
* [Användning](#outlook-output---usage)

### <a name="outlook-output---example"></a>Outlook-utdata-exempel

Se språkspecifika exempel:

* [C#-skript (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook-utdata – C#-skript exempel

I följande exempel skickas ett e-postmeddelande via Outlook.

*function.jsi* -filen definierar en http-utlösare med ett Outlook-meddelande om utgående bindning:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

C#-skript koden skickar ett e-postmeddelande från anroparen till en mottagare som anges i frågesträngen:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequest req, out Message message, ILogger log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

#### <a name="outlook-output---javascript-example"></a>Exempel på Outlook-utdata – JavaScript

I följande exempel skickas ett e-postmeddelande via Outlook.

*function.jsi* -filen definierar en http-utlösare med ett Outlook-meddelande om utgående bindning:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

JavaScript-koden skickar ett e-postmeddelande från anroparen till en mottagare som anges i frågesträngen:

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```

### <a name="outlook-output---attributes"></a>Utdata-attribut för Outlook

Använd [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) -attributet i [klass bibliotek i C#](functions-dotnet-class-library.md).

### <a name="outlook-output---configuration"></a>Outlook-utdata-konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `Outlook` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**| saknas |Obligatoriskt – variabel namnet som används i funktions koden för e-postmeddelandet. Se [använda en databindning i Outlook-meddelanden från kod](#outlook-output-code).|
|**bastyp**| saknas |Required-måste anges till `outlook` .|
|**position**| saknas |Required-måste anges till `out` .|
|**Autentiseringsidentitet**|**Identitet**|Krävs – den identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [http-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> – Använder identiteten för en tidigare inloggad användare med angivet ID. Se <code>userId</code> egenskapen.</li><li><code>userFromToken</code> – Använder den identitet som representeras av angiven token. Se <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> – Använder appens identitet.</li></ul>|
|**userId**|**UserId**  |Krävs om och endast om _identitet_ har angetts till `userFromId` . Ett huvud konto-ID som är kopplat till en tidigare inloggad användare.|
|**userToken**|**UserToken**|Krävs om och endast om _identitet_ har angetts till `userFromToken` . En giltig token för Function-appen. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Outlook-utdata-användning

Den här bindningen kräver följande Azure AD-behörigheter:

|Resurs|Behörighet|
|--------|--------|
|Microsoft Graph|Skicka e-post som användare|

Bindningen visar följande typer av .NET-funktioner:
- Microsoft. Graph. Message
- Newtonsoft.Jspå. LINQ. JObject
- sträng
- Anpassade objekt typer (med bindning av strukturella modeller)






## <a name="webhooks"></a>Webhooks

> [!IMPORTANT]
> Microsoft Graph för hands versions bindningar är nu föråldrade. Mer information om hur du använder Microsoft Graph med Azure Functions finns i självstudierna [Build Azure Functions med Microsoft Graph](https://docs.microsoft.com/graph/tutorials/azure-functions) .

Med Webhooks kan du reagera på händelser i Microsoft Graph. För att stödja Webhooks behövs funktioner för att skapa, uppdatera och reagera på _webhook-prenumerationer_. En fullständig webhook-lösning kräver en kombination av följande bindningar:
- Med en [Microsoft Graph webhook-utlösare](#webhook-trigger) kan du reagera på en inkommande webhook.
- Med en [Microsoft Graph webhook-prenumeration med inbindning](#webhook-input) kan du Visa befintliga prenumerationer och eventuellt uppdatera dem.
- Med en [Microsoft Graph webhook-prenumeration med utgående bindning](#webhook-output) kan du skapa eller ta bort webhook-prenumerationer.

Själva bindningarna kräver inte några Azure AD-behörigheter, men du måste begära behörigheter som är relevanta för den resurs typ som du vill reagera på. En lista över vilka behörigheter som krävs för varje resurs typ finns i [prenumerations behörigheter](/graph/api/subscription-post-subscriptions?view=graph-rest-1.0).

Mer information om Webhooks finns [i arbeta med Webhooks i Microsoft Graph].





## <a name="webhook-trigger"></a>Webhook-utlösare

> [!IMPORTANT]
> Microsoft Graph för hands versions bindningar är nu föråldrade. Mer information om hur du använder Microsoft Graph med Azure Functions finns i självstudierna [Build Azure Functions med Microsoft Graph](https://docs.microsoft.com/graph/tutorials/azure-functions) .

Med den Microsoft Graph webhook-utlösaren kan en funktion reagera på en inkommande webhook från Microsoft Graph. Varje instans av den här utlösaren kan reagera på en resurstyp för Microsoft Graph.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#webhook-trigger---example)
* [Attribut](#webhook-trigger---attributes)
* [Konfiguration](#webhook-trigger---configuration)
* [Användning](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Webhook-utlösare – exempel

Se språkspecifika exempel:

* [C#-skript (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Webhook-utlösare – exempel på C#-skript

I följande exempel hanterar du Webhooks för inkommande Outlook-meddelanden. Om du vill använda en webhook-utlösare [skapar du en prenumeration](#webhook-output---example)och du kan [Uppdatera prenumerationen](#webhook-subscription-refresh) för att förhindra att den upphör att gälla.

*function.js* -filen definierar en webhook-utlösare:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

C#-skript koden reagerar på inkommande e-postmeddelanden och loggar innehållet i de som skickas av mottagaren och innehåller "Azure Functions" i ämnet:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(Message msg, ILogger log)  
{
    log.LogInformation("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.LogInformation($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>Webhook-utlösare – JavaScript-exempel

I följande exempel hanterar du Webhooks för inkommande Outlook-meddelanden. Om du vill använda en webhook-utlösare [skapar du en prenumeration](#webhook-output---example)och du kan [Uppdatera prenumerationen](#webhook-subscription-refresh) för att förhindra att den upphör att gälla.

*function.js* -filen definierar en webhook-utlösare:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

JavaScript-koden reagerar på inkommande e-postmeddelanden och loggar innehållet i de som skickas av mottagaren och innehåller "Azure Functions" i ämnet:

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

### <a name="webhook-trigger---attributes"></a>Webhook-utlösare-attribut

Använd attributet [GraphWebhookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookTriggerAttribute.cs) i [C#-klass bibliotek](functions-dotnet-class-library.md).

### <a name="webhook-trigger---configuration"></a>Webhook-utlösare – konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `GraphWebhookTrigger` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**| saknas |Obligatoriskt – variabel namnet som används i funktions koden för e-postmeddelandet. Se [använda en databindning i Outlook-meddelanden från kod](#outlook-output-code).|
|**bastyp**| saknas |Required-måste anges till `graphWebhook` .|
|**position**| saknas |Required-måste anges till `trigger` .|
|**Typer**|**Typer**|Krävs – den graf-resurs som den här funktionen ska svara på webhookar för. Kan vara något av följande värden:<ul><li><code>#Microsoft.Graph.Message</code> – ändringar som gjorts i Outlook-meddelanden.</li><li><code>#Microsoft.Graph.DriveItem</code> – ändringar som gjorts i objekt i OneDrive-roten.</li><li><code>#Microsoft.Graph.Contact</code> – ändringar som gjorts i personliga kontakter i Outlook.</li><li><code>#Microsoft.Graph.Event</code> – ändringar som gjorts i Outlooks Kalender objekt.</li></ul>|

> [!Note]
> En Function-app kan bara ha en funktion som har registrerats mot ett angivet `resourceType` värde.

### <a name="webhook-trigger---usage"></a>Webhook-utlösare – användning

Bindningen visar följande typer av .NET-funktioner:
- Microsoft Graph SDK-typer som är relevanta för resurs typen, till exempel `Microsoft.Graph.Message` eller `Microsoft.Graph.DriveItem` .
- Anpassade objekt typer (med bindning av strukturella modeller)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Webhook-ingång

> [!IMPORTANT]
> Microsoft Graph för hands versions bindningar är nu föråldrade. Mer information om hur du använder Microsoft Graph med Azure Functions finns i självstudierna [Build Azure Functions med Microsoft Graph](https://docs.microsoft.com/graph/tutorials/azure-functions) .

Med Microsoft Graph webhook-databindningen kan du hämta listan över prenumerationer som hanteras av den här funktions appen. Bindningen läser från funktionen app Storage, så den återspeglar inte andra prenumerationer som skapats utanför appen.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#webhook-input---example)
* [Attribut](#webhook-input---attributes)
* [Konfiguration](#webhook-input---configuration)
* [Användning](#webhook-input---usage)

### <a name="webhook-input---example"></a>Webhook-ingångs exempel

Se språkspecifika exempel:

* [C#-skript (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Webhook-skript exempel för Visual Hook

I följande exempel hämtas alla prenumerationer för den anropande användaren och de tas bort.

*function.js* -filen definierar en http-utlösare med en prenumerations data bindning och en utgående prenumeration för prenumerationen som använder åtgärden ta bort:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C#-skript koden hämtar prenumerationerna och tar bort dem:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.LogInformation($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>Webhook-ingångs-JavaScript-exempel

I följande exempel hämtas alla prenumerationer för den anropande användaren och de tas bort.

*function.js* -filen definierar en http-utlösare med en prenumerations data bindning och en utgående prenumeration för prenumerationen som använder åtgärden ta bort:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript-koden hämtar prenumerationerna och tar bort dem:

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

### <a name="webhook-input---attributes"></a>Webhook-dataattribut

Använd attributet [GraphWebhookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) i [C#-klass bibliotek](functions-dotnet-class-library.md).

### <a name="webhook-input---configuration"></a>Webhook-indatamängd – konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `GraphWebhookSubscription` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**| saknas |Obligatoriskt – variabel namnet som används i funktions koden för e-postmeddelandet. Se [använda en databindning i Outlook-meddelanden från kod](#outlook-output-code).|
|**bastyp**| saknas |Required-måste anges till `graphWebhookSubscription` .|
|**position**| saknas |Required-måste anges till `in` .|
|**Synkroniseringsfilter**|**Filter**| Om detta är inställt på `userFromRequest` , kommer bindningen bara hämta prenumerationer som ägs av den anropande användaren (endast giltig med [http-utlösare]).| 

### <a name="webhook-input---usage"></a>Webhook-ingångs användning

Bindningen visar följande typer av .NET-funktioner:
- sträng []
- Matriser för anpassad objekt typ
- Newtonsoft.Jspå. LINQ. JObject []
- Microsoft. Graph. Subscription []





## <a name="webhook-output"></a>Webhook-utdata

> [!IMPORTANT]
> Microsoft Graph för hands versions bindningar är nu föråldrade. Mer information om hur du använder Microsoft Graph med Azure Functions finns i självstudierna [Build Azure Functions med Microsoft Graph](https://docs.microsoft.com/graph/tutorials/azure-functions) .

Med utgångs bindningen webhook-prenumeration kan du skapa, ta bort och uppdatera webhook-prenumerationer i Microsoft Graph.

Det här avsnittet innehåller följande underavsnitt:

* [Exempel](#webhook-output---example)
* [Attribut](#webhook-output---attributes)
* [Konfiguration](#webhook-output---configuration)
* [Användning](#webhook-output---usage)

### <a name="webhook-output---example"></a>Webhook-utdata – exempel

Se språkspecifika exempel:

* [C#-skript (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Webhook-utdata – C#-skript exempel

I följande exempel skapas en prenumeration. Du kan [Uppdatera prenumerationen](#webhook-subscription-refresh) för att förhindra att den upphör att gälla.

*function.jsi* -filen definierar en http-utlösare med en prenumerations utgående bindning med åtgärden Skapa:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C#-skript koden registrerar en webhook som kommer att meddela den här funktions appen när den anropande användaren får ett Outlook-meddelande:

```csharp
using System;
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>Webhook-utdata – JavaScript-exempel

I följande exempel skapas en prenumeration. Du kan [Uppdatera prenumerationen](#webhook-subscription-refresh) för att förhindra att den upphör att gälla.

*function.jsi* -filen definierar en http-utlösare med en prenumerations utgående bindning med åtgärden Skapa:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

JavaScript-koden registrerar en webhook som kommer att meddela den här funktions appen när den anropande användaren får ett Outlook-meddelande:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Webhook-utdata-attribut

Använd attributet [GraphWebhookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) i [C#-klass bibliotek](functions-dotnet-class-library.md).

### <a name="webhook-output---configuration"></a>Webhook-utdata – konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `GraphWebhookSubscription` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**Namn**| saknas |Obligatoriskt – variabel namnet som används i funktions koden för e-postmeddelandet. Se [använda en databindning i Outlook-meddelanden från kod](#outlook-output-code).|
|**bastyp**| saknas |Required-måste anges till `graphWebhookSubscription` .|
|**position**| saknas |Required-måste anges till `out` .|
|**Autentiseringsidentitet**|**Identitet**|Krävs – den identitet som ska användas för att utföra åtgärden. Kan vara något av följande värden:<ul><li><code>userFromRequest</code> – Endast giltigt med [http-utlösare]. Använder identiteten för den anropande användaren.</li><li><code>userFromId</code> – Använder identiteten för en tidigare inloggad användare med angivet ID. Se <code>userId</code> egenskapen.</li><li><code>userFromToken</code> – Använder den identitet som representeras av angiven token. Se <code>userToken</code> egenskapen.</li><li><code>clientCredentials</code> – Använder appens identitet.</li></ul>|
|**userId**|**UserId**  |Krävs om och endast om _identitet_ har angetts till `userFromId` . Ett huvud konto-ID som är kopplat till en tidigare inloggad användare.|
|**userToken**|**UserToken**|Krävs om och endast om _identitet_ har angetts till `userFromToken` . En giltig token för Function-appen. |
|**tgärd**|**Åtgärd**|Required-anger den åtgärd som bindningen ska utföra. Kan vara något av följande värden:<ul><li><code>create</code> – Registrerar en ny prenumeration.</li><li><code>delete</code> -Tar bort en angiven prenumeration.</li><li><code>refresh</code> -Uppdaterar en angiven prenumeration så att den upphör att gälla.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Krävs om och endast om _åtgärden_ har angetts till `create` . Anger den Microsoft Graph resurs som ska övervakas för ändringar. Se [arbeta med Webhooks i Microsoft Graph]. |
|**Ändrings typs**|**Ändrings typs**|Krävs om och endast om _åtgärden_ har angetts till `create` . Anger typen av ändring i den prenumerations resurs som ska utlösa ett meddelande. De värden som stöds är: `created` , `updated` , `deleted` . Flera värden kan kombineras med hjälp av en kommaavgränsad lista.|

### <a name="webhook-output---usage"></a>Webhook-utdata-användning

Bindningen visar följande typer av .NET-funktioner:
- sträng
- Microsoft. Graph. Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Uppdatering av webhook-prenumeration

> [!IMPORTANT]
> Microsoft Graph för hands versions bindningar är nu föråldrade. Mer information om hur du använder Microsoft Graph med Azure Functions finns i självstudierna [Build Azure Functions med Microsoft Graph](https://docs.microsoft.com/graph/tutorials/azure-functions) .

Det finns två metoder för att uppdatera prenumerationer:

- Använd program identiteten för att hantera alla prenumerationer. Detta kräver medgivande från en Azure Active Directory administratör. Detta kan användas av alla språk som stöds av Azure Functions.
- Använd identiteten som är associerad med varje prenumeration genom att manuellt binda varje användar-ID. Detta kräver viss anpassad kod för att utföra bindningen. Detta kan endast användas av .NET functions.

Det här avsnittet innehåller ett exempel för var och en av dessa metoder:

* [Exempel på App-identitet](#webhook-subscription-refresh---app-identity-example)
* [Exempel på användar identitet](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Webhook-prenumeration uppdatera – app Identity-exempel

Se språkspecifika exempel:

* [C#-skript (.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>App Identity Refresh – C#-skript exempel

I följande exempel används program identiteten för att uppdatera en prenumeration.

*function.jspå* definierar en timer-utlösare med en prenumerations data bindning och en bindning för prenumerations utdata:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

C#-skript koden uppdaterar prenumerationerna:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, ILogger log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.LogInformation($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>App Identity Refresh – C#-skript exempel

I följande exempel används program identiteten för att uppdatera en prenumeration.

*function.jspå* definierar en timer-utlösare med en prenumerations data bindning och en bindning för prenumerations utdata:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

JavaScript-koden uppdaterar prenumerationerna:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Refreshing subscription ${existing[i]}`);
        toRefresh.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>Uppdatera webhook-prenumeration – exempel på användar identitet

I följande exempel används användar identiteten för att uppdatera en prenumeration.

*function.jsi* filen definierar en timer-utlösare och skjuter upp bindningen för prenumerations data till funktions koden:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

C#-skript koden uppdaterar prenumerationerna och skapar utmatnings bindningen i kod med hjälp av varje användares identitet:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, ILogger log)
{
  log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.LogInformation($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)

[HTTP-utlösare]: functions-bindings-http-webhook.md
[Arbeta med Webhooks i Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
