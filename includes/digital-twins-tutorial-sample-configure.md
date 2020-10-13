---
author: baanders
description: inkludera fil för Azure Digitals dubblare-självstudier – konfigurera ett exempel projekt
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 4ac748c606d8ec3c8ba754c34d9c9e7512344a83
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91292748"
---
## <a name="configure-the-sample-project"></a>Konfigurera exempelprojektet

Konfigurera sedan ett exempel klient program som ska interagera med din Azure Digital-instansen.

Navigera till den dator som du laddade ned tidigare från [*Azure Digitals-exempel*](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples) (och zippa upp den om du inte redan har gjort det).

När du är i mappen navigerar du till _AdtSampleApp_. Öppna _**AdtE2ESample. SLN**_ i Visual Studio 2019. 

I Visual Studio använder du fönstret *Solution Explorer* för att skapa en kopia av den _SampleClientApp > **serviceConfig.js. MALLFIL** _ (du kan använda de högra menyerna för att kopiera och klistra in). Byt namn på kopian *serviceConfig.jspå*. Detta fungerar som en för hands uppsättnings-JSON-fil med nödvändiga konfigurationsfiler för att köra projektet.

Välj *serviceConfig.jspå* filen för att öppna den i redigerings fönstret. Ändra `tenantId` till ditt *katalog-ID*, `clientId` till ditt *program-ID*och `instanceUrl` till din Azure Digital-instansen *värdnamn* -URL (med *https://* framför den som visas nedan).

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```



Spara och stäng filen. 

Konfigurera sedan *serviceConfig.jspå* filen som ska kopieras till utdatakatalogen när du skapar *SampleClientApp*. Det gör du genom att högerklicka på *serviceConfig.jspå* filen och välja *Egenskaper.* I *egenskaps* panelen ändrar du värdet för egenskapen *Kopiera till utdata-katalog* som ska *kopieras om senare*.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Utdrag från Visual Studio-fönstret som visar Solution Explorer fönstret med serviceConfig.jsmarkerat och egenskaps fönstret med egenskapen kopiera till utgående katalog inställt på Kopiera om nyare" border="false":::

Se till att _**AdtE2ESample**_ -projektet är öppet i Visual Studio och fortsätt att använda det i självstudien.

