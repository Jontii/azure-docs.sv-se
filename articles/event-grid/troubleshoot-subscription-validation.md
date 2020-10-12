---
title: Azure Event Grid – fel sökning av prenumerations validering
description: Den här artikeln visar hur du kan felsöka prenumerations valideringar.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 48844859013507ab684ef8879b7b85dd6b6fe8cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86118995"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Felsöka Azure Event Grid prenumerations valideringar
Den här artikeln innehåller information om hur du felsöker händelse prenumerations valideringar. 

> [!IMPORTANT]
> Detaljerad information om slut punkts validering för Webhooks finns i avsnittet om att [leverera en webhook](webhook-event-delivery.md).

## <a name="validate-event-grid-event-subscription-using-postman"></a>Verifiera Event Grid händelse prenumeration med Postman
Här är ett exempel på hur du använder Postman för att verifiera en webhook-prenumeration på en Event Grid händelse: 

![Händelse prenumerations verifiering i Event Grid med Postman](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Här är ett exempel på en **SubscriptionValidationEvent** JSON:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Här är exempel på lyckat svar:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Mer information om hur du Event Grid händelse validering för Webhooks finns i [slut punkts validering med event Grid-händelser](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).


## <a name="validate-event-grid-event-subscription-using-curl"></a>Verifiera Event Grid händelse prenumerationen med hjälp av sväng 
Här är ett exempel på en spiral-kommando för att verifiera en webhook-prenumeration för en Event Grid händelse: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Verifiera moln händelse prenumeration med Postman
Här är ett exempel på hur du använder Postman för att verifiera en webhook-prenumeration på en moln händelse: 

![Moln händelse prenumerations validering med Postman](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Använd **http Options-** metoden för att verifiera med moln händelser. Mer information om validering av moln händelser för Webhooks finns i [slut punkts validering med moln händelser](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="error-code-403"></a>Felkod: 403
Om webhooken returnerar 403 (förbjuden) i svaret kontrollerar du att webhooken ligger bakom en Azure Application Gateway eller brand vägg för webbaserade program. Om det är så måste du inaktivera följande brand Väggs regler och göra ett HTTP-inlägg igen:

  - 920300 (begäran saknar ett accept-huvud, vi kan åtgärda detta)
  - 942430 (begränsad SQL Character-avvikelse identifiering (argument): # av specialtecken har överskridits (12))
  - 920230 (flera URL-kodning identifierade)
  - 942130 (SQL-injektering: SQL-Tautology har identifierats.)
  - 931130 (möjligt RFI-angrepp (Remote File inkludering) = Off-Domain referens/länk)

## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp kan du publicera ditt problem i [Stack Overflow-forumet](https://stackoverflow.com/questions/tagged/azure-eventgrid) eller öppna ett [support ärende](https://azure.microsoft.com/support/options/). 
