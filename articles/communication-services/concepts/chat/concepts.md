---
title: Chatta koncept i Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Lär dig mer om chatt-koncept för kommunikations tjänster.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: f33d7efd1c136619767c3eadd93740442ae7239a
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762050"
---
# <a name="chat-concepts"></a>Chattbegrepp

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure Communication Services Chat-klient bibliotek kan användas för att lägga till text i real tid i dina program. Den här sidan sammanfattar viktiga chatt-koncept och-funktioner.

Se [Översikt över kommunikations tjänstens klient bibliotek](./sdk-features.md) för att lära dig mer om aktuella klient biblioteks språk och-funktioner.

## <a name="chat-overview"></a>Översikt över chatt 

Chat-konversationer sker inom chatt-trådar. En chatt-tråd kan innehålla många meddelanden och många användare. Varje meddelande tillhör en enda tråd och en användare kan vara en del av en eller flera trådar. 

Varje användare i chatten kallas för en medlem. Du kan ha upp till 250 medlemmar i en chatt-tråd. Endast tråd medlemmar kan skicka och ta emot meddelanden eller lägga till/ta bort medlemmar i en chatt-tråd. Den största tillåtna meddelande storleken är ungefär 28KB. Du kan hämta alla meddelanden i en chatt-tråd med hjälp av `List/Get Messages` åtgärden. Kommunikations tjänsterna lagrar chatten tills du kör en borttagnings åtgärd i chatten eller meddelandet, eller tills inga medlemmar återstår i chatt-tråden där det är överblivna och bearbetas för borttagning.   

För chatt-trådar med fler än 20 medlemmar inaktive ras Läs kvitton och skriv indikator funktioner. 

## <a name="chat-architecture"></a>Chatt-arkitektur

Det finns två kärn delar för att chatta arkitektur: 1) klient program för betrodda tjänster och 2).

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagram som visar kommunikations tjänsternas Chat-arkitektur.":::

 - **Betrodd tjänst:** Om du vill hantera en chatt korrekt behöver du en tjänst som hjälper dig att ansluta till kommunikations tjänster med hjälp av resurs anslutnings strängen. Den här tjänsten ansvarar för att skapa chatt-trådar, hantera tråd medlemskap och tillhandahålla åtkomsttoken till användare. Du hittar mer information om åtkomsttoken i snabb starten för våra [åtkomsttoken](../../quickstarts/access-tokens.md) .

 - **Klient app:**  Klient programmet ansluter till din betrodda tjänst och tar emot åtkomsttoken som används för att ansluta direkt till kommunikations tjänsterna. När anslutningen har upprättats kan klient programmet skicka och ta emot meddelanden.
    
## <a name="message-types"></a>Meddelande typer

Kommunikations tjänster-chatt delar användarspecifika meddelanden och systemgenererade meddelanden som kallas **tråd aktiviteter**. Tråd aktiviteter skapas när en chatt-tråd uppdateras. När du anropar `List Messages` eller `Get Messages` på en chatt-tråd innehåller resultatet de användare-genererade textmeddelandena och system meddelandena i kronologisk ordning. Detta hjälper dig att identifiera när en medlem har lagts till eller tagits bort eller när chatt ämnet uppdaterades. Följande meddelande typer stöds:  

 - `Text`: Det faktiska meddelandet som skapas och skickas av användaren som en del av Chat-konversationen. 
 - `ThreadActivity/AddMember`: System meddelande som anger att en eller flera medlemmar har lagts till i chatt-tråden. Exempel:

```xml

<addmember>
    <eventtime>1598478187549</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</id>
        <friendlyName>User 1</friendlyName>
    </detailedtargetinfo>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</addmember>

```  

- `ThreadActivity/DeleteMember`: System meddelande som anger att en medlem har tagits bort från chatt-tråden. Exempel:

```xml

<deletemember>
    <eventtime>1598478187642</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</deletemember>

```

- `ThreadActivity/TopicUpdate`: System meddelande som anger att ämnet har uppdaterats. Exempel:

```xml

<topicupdate>
    <eventtime>1598477591811</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <value>New topic</value>
</topicupdate>

```

## <a name="real-time-signaling"></a>Real tids signalering 

Klient biblioteket för chat Java Script innehåller real tids signalering. Detta gör det möjligt för klienter att lyssna efter uppdateringar och inkommande meddelanden i real tid till en chatt-tråd utan att behöva avsöka API: erna. Tillgängliga händelser är:

 - `ChatMessageReceived` – När ett nytt meddelande skickas till en chatt-tråd som användaren är medlem i. Den här händelsen skickas inte för automatiskt genererade system meddelanden som vi beskrivit i föregående avsnitt.  
 - `ChatMessageEdited` – När ett meddelande redige ras i en chatt-tråd som användaren är medlem i. 
 - `ChatMessageDeleted` – När ett meddelande tas bort i en chatt-tråd som användaren är medlem i. 
 - `TypingIndicatorReceived` – När en annan medlem skriver ett meddelande i en chatt-tråd som användaren är medlem i. 
 - `ReadReceiptReceived` – När en annan medlem har läst meddelandet som användaren skickade i en chatt-tråd. 

## <a name="chat-events"></a>Chatta händelser 

Med real tids signaler kan användarna chatta i real tid. Dina tjänster kan använda Azure Event Grid för att prenumerera på chatt-relaterade händelser. Mer information finns i [begrepp för händelse hantering](../event-handling.md).

## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>Använda Cognitive Services med klient biblioteket i Chat för att aktivera intelligenta funktioner

Du kan använda [Azure kognitiva API: er](https://docs.microsoft.com/azure/cognitive-services/) med klient biblioteket i Chat för att lägga till smarta funktioner i dina program. Du kan till exempel:

- Gör det möjligt för användare att chatta med varandra på olika språk. 
- Hjälp en support agent att prioritera biljetter genom att identifiera ett negativt sentiment av ett inkommande problem från en kund.
- Analysera inkommande meddelanden för nyckel identifiering och entitets igenkänning och fråga relevant information till användaren i din app baserat på meddelandets innehåll.

Ett sätt att uppnå detta är genom att låta din betrodda tjänst agera som medlem i en chatt. Anta att du vill aktivera översättning av språk. Den här tjänsten ansvarar för att lyssna på meddelanden som utbyts av andra medlemmar [1], anropar kognitiva API: er för att översätta innehållet till det önskade språket [2, 3] och skicka det översatta resultatet som ett meddelande i chatt-tråden [4]. 

På så sätt kommer meddelande historiken innehålla både original meddelanden och översatta meddelanden. I klient programmet kan du lägga till logik för att visa det ursprungliga eller översatta meddelandet. I [den här snabb](https://docs.microsoft.com/azure/cognitive-services/translator/quickstart-translator) starten får du veta hur du använder kognitiva API: er för att översätta text till olika språk. 

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagram som visar kommunikations tjänsternas Chat-arkitektur.":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med chatt](../../quickstarts/chat/get-started.md)

Följande dokument kan vara intressanta för dig:

- Bekanta dig med [klient biblioteket för chat](sdk-features.md)
