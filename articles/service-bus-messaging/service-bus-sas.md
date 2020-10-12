---
title: Azure Service Bus åtkomst kontroll med signaturer för delad åtkomst
description: 'Översikt över Service Bus åtkomst kontroll med signaturer för delad åtkomst: översikt, information om SAS-auktorisering med Azure Service Bus.'
ms.topic: article
ms.date: 07/30/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: fb90b2ae290752753b58b5e96c6c8a8b23f4c168
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89012083"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Service Bus åtkomst kontroll med signaturer för delad åtkomst

*Signaturer för delad åtkomst* (SAS) är den primära säkerhetsmekanismen för Service Bus meddelande hantering. I den här artikeln beskrivs SAS, hur de fungerar och hur de används på ett oberoende sätt.

SAS skyddar åtkomsten till Service Bus baserat på auktoriseringsregler. De konfigureras antingen på ett namn område eller en meddelande enhet (relä, kö eller ämne). En auktoriseringsregel har ett namn, är kopplat till vissa rättigheter och har ett par kryptografiska nycklar. Du använder regelns namn och nyckel via Service Bus SDK eller i din egen kod för att skapa en SAS-token. En klient kan sedan skicka token till Service Bus för att bevisa auktoriseringen för den begärda åtgärden.

> [!NOTE]
> Azure Service Bus stöder auktorisering av åtkomst till ett Service Bus-namnområde och dess entiteter med hjälp av Azure Active Directory (Azure AD). Auktorisering av användare eller program med OAuth 2,0-token som returnerades av Azure AD ger överlägsen säkerhet och enkel användning över signaturer för delad åtkomst (SAS). Med Azure AD behöver du inte lagra tokens i din kod och potentiella säkerhets risker.
>
> Microsoft rekommenderar att du använder Azure AD med dina Azure Service Bus-program när det är möjligt. Mer information finns i följande artiklar:
> - [Autentisera och auktorisera ett program med Azure Active Directory för att få åtkomst till Azure Service Bus entiteter](authenticate-application.md).
> - [Autentisera en hanterad identitet med Azure Active Directory för att få åtkomst till Azure Service Bus resurser](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>Översikt över SAS

Signaturer för delad åtkomst är en anspråksbaserad mekanism för auktorisering med enkla tokens. Med SAS skickas aldrig nycklar i kabeln. Nycklar används för att signera information kryptografiskt som senare kan verifieras av tjänsten. SAS kan användas på samma sätt som användar namn och lösen ord, där klienten har omedelbar till gång till ett namn för auktoriseringsregeln och en matchande nyckel. SAS kan också användas på samma sätt som en federerad säkerhets modell, där klienten får en tidsbegränsad och signerad åtkomsttoken från en säkerhetstokentjänst utan att någonsin ha fått till gång till signerings nyckeln.

SAS-autentisering i Service Bus har kon figurer ATS med namngivna [regler för delad åtkomst](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) som har associerade åtkomst rättigheter och ett par med primära och sekundära kryptografiska nycklar. Nycklarna är 256-bitars värden i base64-representation. Du kan konfigurera regler på namn områdes nivå, på Service Bus [reläer](../azure-relay/relay-what-is-it.md), [köer](service-bus-messaging-overview.md#queues)och [ämnen](service-bus-messaging-overview.md#topics).

Token för [signaturen för delad åtkomst](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) innehåller namnet på den valda AUKTORISERINGSREGELN, URI för den resurs som ska användas, ett upphör ande omedelbart och en SHA256 kryptografisk signatur som beräknas över dessa fält med antingen den primära eller sekundära kryptografiska nyckeln för den valda auktoriseringsregeln.

## <a name="shared-access-authorization-policies"></a>Auktoriseringsprinciper för delad åtkomst

Varje Service Bus-namnrymd och varje Service Bus entitet har en princip för delad åtkomst som består av regler. Principen på namn områdes nivån gäller för alla entiteter i namn området, oavsett deras enskilda princip konfiguration.

För varje auktoriseringsregel bestämmer du om tre delar av information: **namn**, **omfattning**och **rättigheter**. **Namnet** är bara det. ett unikt namn inom det omfånget. Omfånget är tillräckligt enkelt: det är URI: n för den aktuella resursen. För ett Service Bus-namnområde är omfånget det fullständigt kvalificerade domän namnet (FQDN), till exempel `https://<yournamespace>.servicebus.windows.net/` .

Rättigheterna som tilldelas av princip regeln kan vara en kombination av:

* Send-ger behörighet att skicka meddelanden till entiteten
* Lyssna-ger rätt att lyssna (relä) eller ta emot (kö, prenumerationer) och all relaterad meddelande hantering
* Hantera – ger behörighet att hantera topologin för namn området, inklusive att skapa och ta bort entiteter

Rättigheten "hantera" innehåller rättigheterna "Send" och "Receive".

Ett namn område eller en enhets princip kan innehålla upp till 12 regler för delad åtkomst, vilket ger plats för tre uppsättningar regler, var och en täcker de grundläggande rättigheterna och kombinationen av skicka och lyssna. Den här gränsen visar att SAS-principagenten inte är avsedd att vara ett användar-eller tjänst konto arkiv. Om ditt program behöver bevilja åtkomst till Service Bus baserat på användar-eller tjänst identiteter bör det implementera en säkerhetstokentjänst som utfärdar SAS-token efter autentisering och åtkomst kontroll.

En auktoriseringsregel tilldelas en *primär nyckel* och en *sekundär nyckel*. Dessa är kryptografiskt starka nycklar. Tappa inte bort dem eller läcker dem – de är alltid tillgängliga i [Azure Portal][Azure portal]. Du kan använda någon av de genererade nycklarna och du kan återskapa dem när du vill. Om du återskapar eller ändrar en nyckel i principen blir alla tidigare utfärdade token baserade på den nyckeln omedelbart ogiltiga. Pågående anslutningar som skapas baserat på sådana tokens fortsätter dock att fungera tills token upphör att gälla.

När du skapar ett Service Bus-namnområde skapas automatiskt en princip regel med namnet **RootManageSharedAccessKey** för namn området. Den här principen har behörighet att hantera hela namn området. Vi rekommenderar att du behandlar den här regeln som ett administratörs **rot** konto och inte använder den i ditt program. Du kan skapa ytterligare princip regler på fliken **Konfigurera** för namn området i portalen via PowerShell eller Azure CLI.

## <a name="configuration-for-shared-access-signature-authentication"></a>Konfiguration för autentisering av signatur för delad åtkomst

Du kan konfigurera [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -regeln på Service Bus namn områden, köer eller ämnen. Det finns för närvarande inte stöd för att konfigurera en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) på en Service Bus-prenumeration, men du kan använda regler som kon figurer ATS i ett namn område eller ämne för att skydda åtkomsten till prenumeration Ett arbets exempel som illustrerar den här proceduren finns i [använda autentisering med signatur för delad åtkomst (SAS) med Service Bus prenumerations](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) exempel.

![SÄKERHETS](./media/service-bus-sas/service-bus-namespace.png)

I den här bilden gäller *manageRuleNS*-, *SendRuleNS*-och *listenRuleNS* -auktoriseringsregler både för kön Q1 och ämnet T1, medan *ListenRuleQ* och *sendRuleQ* endast gäller för kön Q1 och *sendRuleT* gäller endast ämne T1.

## <a name="generate-a-shared-access-signature-token"></a>Generera en token för signatur för delad åtkomst

Alla klienter som har åtkomst till namnet på ett namn för auktoriseringsregeln och en av dess signerings nycklar kan generera en SAS-token. Token genereras genom att en sträng skapas i följande format:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** -Token upphör att gälla omedelbart. Heltal som motsvarar sekunder sedan den `00:00:00 UTC` 1 januari 1970 (UNIX-epok) när token upphör att gälla.
* **`skn`** – Namnet på auktoriseringsregeln.
* **`sr`** -URI för resursen som används.
* **`sig`** Signatur.

`signature-string`Är SHA-256-hashen som beräknats över resurs-URI: n (**omfånget** enligt beskrivningen i föregående avsnitt) och sträng representationen av token som upphör att gälla, avgränsade med LF.

Hash-beräkningen ser ut ungefär som följande pseudo-kod och returnerar ett hash-värde på 256 bitar/32 byte.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token innehåller de värden som inte är hash-kodade, så att mottagaren kan beräkna hashen på samma parametrar och kontrol lera att utfärdaren har en giltig signerings nyckel.

Resurs-URI är den fullständiga URI: n för den Service Bus resurs som åtkomst begärs till. Till exempel `http://<namespace>.servicebus.windows.net/<entityPath>` eller `sb://<namespace>.servicebus.windows.net/<entityPath>` ; det vill säga `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3` . 

**URI: n måste vara i [procent kodad](/dotnet/api/system.web.httputility.urlencode?view=netcore-3.1).**

Den auktoriseringsregler för delad åtkomst som används för signering måste konfigureras på den entitet som anges av denna URI, eller av en av dess hierarkiska överordnade. Till exempel `http://contoso.servicebus.windows.net/contosoTopics/T1` eller `http://contoso.servicebus.windows.net` i föregående exempel.

En SAS-token är giltig för alla resurser som har prefixet som `<resourceURI>` används i `signature-string` .

> [!NOTE]
> Exempel på hur du skapar en SAS-token med olika programmeringsspråk finns i [skapa SAS-token](/rest/api/eventhub/generate-sas-token). 

## <a name="regenerating-keys"></a>Återskapar nycklar

Vi rekommenderar att du regelbundet återskapar nycklarna som används i [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -objektet. De primära och sekundära nyckel platserna finns så att du kan rotera nycklar gradvis. Om programmet vanligt vis använder primär nyckeln kan du kopiera primär nyckeln till den sekundära nyckel platsen och sedan återskapa den primära nyckeln. Det nya värdet för primär nyckel kan sedan konfigureras i klient programmen, som har fortsatt åtkomst med hjälp av den gamla primär nyckeln på den sekundära platsen. När alla klienter har uppdaterats kan du återskapa den sekundära nyckeln för att slutligen dra tillbaka den gamla primär nyckeln.

Om du vet eller misstänker att en nyckel har komprometterats och du måste återkalla nycklarna kan du återskapa både [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) och [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) för en [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)och ersätta dem med nya nycklar. Den här proceduren gör att alla token som signerats med gamla nycklar inte verifieras.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Autentisering med signatur för delad åtkomst med Service Bus

Scenarierna som beskrivs nedan omfattar konfiguration av auktoriseringsregler, generering av SAS-token och klient auktorisering.

För ett komplett fungerande exempel på ett Service Bus program som illustrerar konfigurationen och använder SAS-auktorisering, se [signaturen för delad åtkomst-autentisering med Service Bus](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Ett relaterat exempel som illustrerar användningen av SAS-auktoriseringsregler som kon figurer ATS på namn områden eller ämnen för att skydda Service Bus prenumerationer finns här: [använda autentisering med signatur för delad åtkomst (SAS) med Service Bus prenumerationer](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Åtkomst auktoriseringsregler för delad åtkomst på en entitet

Med Service Bus .NET Framework bibliotek kan du komma åt ett [Microsoft. Service Bus. Messaging. SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -objekt som kon figurer ATS i en Service Bus kö eller ett ämne via [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) -samlingen i motsvarande [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) eller [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

Följande kod visar hur du lägger till auktoriseringsregler för en kö.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Använd autentisering med signaturer för delad åtkomst

Program som använder Azure .NET SDK med Service Bus .NET-bibliotek kan använda SAS-auktorisering via klassen [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) . Följande kod illustrerar användningen av token-providern för att skicka meddelanden till en Service Bus kö. Alternativt till användningen som visas här kan du också skicka en tidigare Utfärdad token till fabriks metoden för token-providern.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Du kan också använda token-providern direkt för att utfärda token som ska skickas till andra klienter.

Anslutnings strängar kan innehålla ett regel namn (*SharedAccessKeyName*) och en regel nyckel (*SharedAccessKey*) eller en tidigare Utfärdad token (*SharedAccessSignature*). När de finns i anslutnings strängen som skickas till en konstruktor eller fabriks metod som accepterar en anslutnings sträng skapas och fylls SAS-token-providern automatiskt.

Observera att om du vill använda SAS-auktorisering med Service Bus reläer kan du använda SAS-nycklar som kon figurer ATS i namn området Service Bus. Om du uttryckligen skapar ett relä i namn området ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) med ett [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription))-objekt kan du ange SAS-reglerna för det reläet. Om du vill använda SAS-auktorisering med Service Bus prenumerationer kan du använda SAS-nycklar som kon figurer ATS på ett Service Bus-namnområde eller i ett ämne.

## <a name="use-the-shared-access-signature-at-http-level"></a>Använd signaturen för delad åtkomst (på HTTP-nivå)

Nu när du vet hur du skapar signaturer för delad åtkomst för alla entiteter i Service Bus är du redo att utföra ett HTTP-inlägg:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Kom ihåg att detta fungerar för allt. Du kan skapa SAS för en kö, ett ämne eller en prenumeration.

Om du ger en avsändare eller klient en SAS-token har de inte nyckeln direkt och de kan inte återställa hashen för att hämta den. Därför har du kontroll över vad de kan komma åt och hur länge. Ett viktigt att komma ihåg är att om du ändrar primär nyckeln i principen blir alla signaturer för delad åtkomst som skapats från den ogiltiga.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Använd signaturen för delad åtkomst (på AMQP nivå)

I föregående avsnitt fick du lära dig hur du använder SAS-token med en HTTP POST-begäran för att skicka data till Service Bus. Som du vet kan du komma åt Service Bus med hjälp av Advanced Message Queueing Protocol (AMQP) som är det prioriterade protokoll som ska användas av prestanda skäl, i många fall. Användningen av SAS-token med AMQP beskrivs i dokumentet [AMQP Claim-Based säkerhets Version 1,0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) i arbets utkast sedan 2013, men stöds av Azure idag.

Innan du börjar skicka data till Service Bus måste utgivaren skicka SAS-token i ett AMQP-meddelande till en väldefinierad AMQP-nod med namnet **$CBS** (du kan se den som en "speciell" kö som används av tjänsten för att hämta och validera alla SAS-token). Utgivaren måste ange **ReplyTo** -fältet i AMQP-meddelandet. Detta är den nod där tjänsten svarar på utgivaren med resultatet av verifieringen av token (ett enkelt fråge-/svars mönster mellan utgivare och tjänst). Denna Reply-nod skapas "i farten", "tala om" dynamisk generering av fjärrnod "enligt beskrivningen i AMQP 1,0-specifikationen. När du har kontrollerat att SAS-token är giltig kan utgivaren gå framåt och börja skicka data till tjänsten.

Följande steg visar hur du skickar SAS-token med AMQP-protokollet med hjälp av [AMQP.net lite](https://github.com/Azure/amqpnetlite) -biblioteket. Detta är användbart om du inte kan använda den officiella Service Bus SDK (till exempel på WinRT, .NET Compact Framework, .NET Micro Framework och mono) som utvecklas i C \# . Det här biblioteket är självklart användbart för att hjälpa dig att förstå hur anspråksbaserad säkerhet fungerar på AMQP nivå, eftersom du såg hur det fungerar på HTTP-nivå (med en HTTP POST-begäran och SAS-token som skickas i "Authorization"-rubriken). Om du inte behöver sådana djupgående kunskaper om AMQP kan du använda den officiella Service Bus SDK: n med .NET Framework program som gör det åt dig.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

`PutCbsToken()`Metoden tar emot *anslutningen* (AMQP Connection Class-instansen som anges av [AMQP .net lite-biblioteket](https://github.com/Azure/amqpnetlite)) som representerar TCP-anslutningen till tjänsten och den *SASTOKEN* -parameter som är den SAS-token som ska skickas.

> [!NOTE]
> Det är viktigt att anslutningen skapas med **sasl inställt på anonym** (och inte standardvärdet Plain med användar namn och lösen ord som används när du inte behöver skicka SAS-token).
>
>

Sedan skapar utgivaren två AMQP-Länkar för att skicka SAS-token och ta emot svaret (verifierings resultatet för token) från tjänsten.

AMQP-meddelandet innehåller en uppsättning egenskaper och mer information än ett enkelt meddelande. SAS-token är bröd texten i meddelandet (med hjälp av konstruktorn). Värdet **"ReplyTo"** är inställt på nodnamn för att ta emot verifierings resultatet på mottagar länken (du kan ändra dess namn om du vill och det kommer att skapas dynamiskt av tjänsten). De sista tre programmen/anpassade egenskaperna används av tjänsten för att ange vilken typ av åtgärd som ska utföras. Som det beskrivs i CBS-utkastet, måste de vara ett **Åtgärds namn** ("placering-token"), **typ av token** (i det här fallet a `servicebus.windows.net:sastoken` ) och **"namn" för den mål grupp** som token gäller (hela entiteten).

När du har skickat SAS-token på avsändaren måste utgivaren läsa svaret på mottagar länken. Svaret är ett enkelt AMQP-meddelande med en program egenskap med namnet **"status kod"** som kan innehålla samma värden som en HTTP-statuskod.

## <a name="rights-required-for-service-bus-operations"></a>Rättigheter som krävs för Service Bus åtgärder

Följande tabell visar de åtkomst behörigheter som krävs för olika åtgärder på Service Bus resurser.

| Åtgärd | Anspråk krävs | Anspråks omfång |
| --- | --- | --- |
| **Namnområde** | | |
| Konfigurera auktoriseringsregeln i ett namn område |Hantera |Alla namn områdes adresser |
| **Tjänst register** | | |
| Räkna upp privata principer |Hantera |Alla namn områdes adresser |
| Börja lyssna på ett namn område |Lyssna |Alla namn områdes adresser |
| Skicka meddelanden till en lyssnare i ett namn område |Skicka |Alla namn områdes adresser |
| **Kö** | | |
| Skapa en kö |Hantera |Alla namn områdes adresser |
| Ta bort en kö |Hantera |En giltig Queue-adress |
| Räkna upp köer |Hantera |/$Resources/Queues |
| Hämta köns Beskrivning |Hantera |En giltig Queue-adress |
| Konfigurera auktoriseringsregeln för en kö |Hantera |En giltig Queue-adress |
| Skicka till kön |Skicka |En giltig Queue-adress |
| Ta emot meddelanden från en kö |Lyssna |En giltig Queue-adress |
| Överge eller Slutför meddelanden när meddelandet har tagits emot i gransknings lås läge |Lyssna |En giltig Queue-adress |
| Skjuta upp ett meddelande för senare hämtning |Lyssna |En giltig Queue-adress |
| Obeställbara meddelanden kön ett meddelande |Lyssna |En giltig Queue-adress |
| Hämta det tillstånd som är associerat med en Message Queue-session |Lyssna |En giltig Queue-adress |
| Ange det tillstånd som är associerat med en Message Queue-session |Lyssna |En giltig Queue-adress |
| Schemalägg ett meddelande för senare leverans. till exempel [ScheduleMessageAsync ()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Lyssna | En giltig Queue-adress
| **Avsnitt** | | |
| Skapa ett ämne |Hantera |Alla namn områdes adresser |
| Ta bort ett ämne |Hantera |En giltig ämnes adress |
| Räkna upp ämnen |Hantera |/$Resources/topics |
| Hämta ämnes beskrivningen |Hantera |En giltig ämnes adress |
| Konfigurera auktoriseringsregeln för ett ämne |Hantera |En giltig ämnes adress |
| Skicka till ämnet |Skicka |En giltig ämnes adress |
| **Prenumeration** | | |
| Skapa en prenumeration |Hantera |Alla namn områdes adresser |
| Ta bort prenumeration |Hantera |.. /myTopic/Subscriptions/mySubscription |
| Räkna upp prenumerationer |Hantera |.. /myTopic/Subscriptions |
| Beskrivning av Hämta prenumeration |Hantera |.. /myTopic/Subscriptions/mySubscription |
| Överge eller Slutför meddelanden när meddelandet har tagits emot i gransknings lås läge |Lyssna |.. /myTopic/Subscriptions/mySubscription |
| Skjuta upp ett meddelande för senare hämtning |Lyssna |.. /myTopic/Subscriptions/mySubscription |
| Obeställbara meddelanden kön ett meddelande |Lyssna |.. /myTopic/Subscriptions/mySubscription |
| Hämta det tillstånd som är associerat med en ämnes session |Lyssna |.. /myTopic/Subscriptions/mySubscription |
| Ange det tillstånd som är associerat med en ämnes session |Lyssna |.. /myTopic/Subscriptions/mySubscription |
| **Regler** | | |
| Skapa en regel |Hantera |.. /myTopic/Subscriptions/mySubscription |
| Ta bort en regel |Hantera |.. /myTopic/Subscriptions/mySubscription |
| Räkna upp regler |Hantera eller lyssna |.. /myTopic/Subscriptions/mySubscription/Rules

## <a name="next-steps"></a>Nästa steg

I följande ämnen kan du lära dig mer om Service Bus-meddelanden.

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Använd Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com