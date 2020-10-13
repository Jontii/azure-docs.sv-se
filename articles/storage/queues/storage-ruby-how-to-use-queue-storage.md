---
title: Använda Queue Storage från ruby-Azure Storage
description: Lär dig hur du använder Azure-Kötjänst för att skapa och ta bort köer och infoga, hämta och ta bort meddelanden. Exempel som skrivits i Ruby.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: c29b864fb3a547599f32c00c9e8c27cbdb0e0a92
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89459614"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Använda Queue Storage från Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hjälp av tjänsten Microsoft Azure Queue Storage. Exemplen skrivs med ruby Azure-API: et.
De scenarier som beskrivs är att **Infoga**, **Granska**, **Hämta**och **ta bort** Kömeddelanden, samt **skapa och ta bort köer**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Skapa ett ruby-program
Skapa ett ruby-program. Instruktioner finns i [skapa en Ruby-app i App Service på Linux](https://docs.microsoft.com/azure/app-service/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Konfigurera ditt program för åtkomst till lagring
Om du vill använda Azure Storage måste du ladda ned och använda ruby Azure-paketet, som innehåller en uppsättning bekvämlighets bibliotek som kommunicerar med lagrings REST tjänsterna.

### <a name="use-rubygems-to-obtain-the-package"></a>Hämta paketet med hjälp av RubyGems
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix).
2. Skriv "gem install Azure" i kommando fönstret för att installera symbolen och beroendena.

### <a name="import-the-package"></a>Importera paketet
Använd din text redigerare och Lägg till följande överst i ruby-filen där du tänker använda Storage:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Konfigurera en Azure Storage anslutning
Azure-modulen läser miljövariablerna **Azure \_ Storage- \_ kontot** och **Azure \_ Storage- \_ ACCESS_KEY** för information som krävs för att ansluta till ditt Azure Storage-konto. Om de här miljövariablerna inte har angetts måste du ange konto informationen innan du använder **Azure:: QueueService** med följande kod:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Du kan hämta dessa värden från ett klassiskt eller Resource Manager-baserat lagringskonto på Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till det lagrings konto som du vill använda.
3. Klicka på **Åtkomstnycklar** till höger på bladet Inställningar.
4. Åtkomstnyckel 1 och åtkomstnyckel 2 visas på bladet som öppnas. Du kan använda vilken av nycklarna du vill. 
5. Kopiera nyckeln till Urklipp genom att klicka på kopieringsikonen. 

## <a name="how-to-create-a-queue"></a>Gör så här: skapa en kö
Följande kod skapar ett **Azure:: QueueService** -objekt, vilket gör att du kan arbeta med köer.

```ruby
azure_queue_service = Azure::QueueService.new
```

Använd metoden **create_queue ()** för att skapa en kö med det angivna namnet.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Så här gör du: infoga ett meddelande i en kö
Om du vill infoga ett meddelande i en kö använder du metoden **create_message ()** för att skapa ett nytt meddelande och lägger till det i kön.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Gör så här: granska vid nästa meddelande
Du kan titta på meddelandet överst i en kö utan att ta bort det från kön genom att anropa metoden **läsa \_ meddelanden ()** . Som standard tittar **gransknings \_ meddelanden ()** på ett enskilt meddelande. Du kan också ange hur många meddelanden du vill granska.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Gör så här: ta bort nästa meddelande i kö
Du kan ta bort ett meddelande från en kö i två steg.

1. När du anropar **list \_ meddelanden ()** får du nästa meddelande i en kö som standard. Du kan också ange hur många meddelanden du vill hämta. Meddelanden som returneras från **list \_ meddelanden ()** blir osynliga för all annan kod som läser meddelanden från den här kön. Du skickar tids gränsen för synlighet i sekunder som en parameter.
2. Om du vill slutföra borttagningen av meddelandet från kön måste du också anropa **delete_message ()**.

Den här två stegs processen för att ta bort ett meddelande säkerställer att när din kod inte kan bearbeta ett meddelande på grund av maskin-eller program varu fel, kan en annan instans av koden Hämta samma meddelande och försöka igen. Kod anropen **ta bort \_ meddelandet ()** direkt efter att meddelandet har bearbetats.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Så här gör du: ändra innehållet i ett köat meddelande
Du kan ändra innehållet i ett meddelande direkt i kön. I koden nedan används metoden **update_message ()** för att uppdatera ett meddelande. Metoden returnerar en tupel som innehåller pop-kvittot för Queue-meddelandet och ett UTC-datum/tid-värde som representerar när meddelandet ska visas i kön.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Gör så här: ytterligare alternativ för att avköa meddelanden
Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö.

1. Du kan få en batch med meddelandet.
2. Du kan ange en längre eller kortare tids gräns för insikter, vilket gör att koden får mer eller mindre tid att bearbeta varje meddelande fullständigt.

I följande kod exempel används metoden **list \_ Messages ()** för att få 15 meddelanden i ett anrop. Sedan skriver den ut och tar bort varje meddelande. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Så här gör du: Hämta Kölängd
Du kan få en uppskattning av antalet meddelanden i kön. Metoden **get \_ Queue \_ metadata ()** uppmanar Queue Service att returnera det ungefärliga antalet meddelanden och metadata om kön.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Så här gör du: ta bort en kö
Om du vill ta bort en kö och alla meddelanden som finns i den anropar du metoden **Delete \_ Queue ()** i objektet köobjekt.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Queue Storage kan du följa dessa länkar för att lära dig mer om komplexa lagrings uppgifter.

* Besök [Azure Storage teamets blogg](https://docs.microsoft.com/archive/blogs/windowsazurestorage/)
* Besök [Azure SDK för ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) -lagringsplatsen på GitHub

En jämförelse mellan Azure Queue Service som diskuteras i den här artikeln och Azure Service Bus köer som beskrivs i artikeln [så här använder du Service Bus köer](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) , se [Azure-köer och Service Bus köer-jämförda och kontrasterande](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
