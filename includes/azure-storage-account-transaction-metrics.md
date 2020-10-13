---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: b2e753a3f9741856dd8b81755912ad7bd78b5557
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711418"
---
Azure Storage tillhandahåller följande transaktions mått i Azure Monitor.

| Mått | Beskrivning |
| ------------------- | ----------------- |
| Transaktioner | Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. <br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Tillämpliga dimensioner: ResponseType, typ av typ, ApiName och autentisering ([definition](#metrics-dimensions))<br/> Värde exempel: 1024 |
| Ingress | Mängden inkommande data. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure. <br/><br/> Enhet: byte <br/> Sammansättnings typ: totalt <br/> Tillämpliga dimensioner: typ av typ, ApiName och autentisering ([definition](#metrics-dimensions)) <br/> Värde exempel: 1024 |
| Utgående | Mängden utgående data. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data. <br/><br/> Enhet: byte <br/> Sammansättnings typ: totalt <br/> Tillämpliga dimensioner: typ av typ, ApiName och autentisering ([definition](#metrics-dimensions)) <br/> Värde exempel: 1024 |
| SuccessServerLatency | Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency. <br/><br/> Enhet: millisekunder <br/> Sammansättnings typ: genomsnitt <br/> Tillämpliga dimensioner: typ av typ, ApiName och autentisering ([definition](#metrics-dimensions)) <br/> Värde exempel: 1024 |
| SuccessE2ELatency | Den genomsnittliga svarstiden från slutpunkt till slutpunkt för lyckade begäranden som gjorts till en lagringstjänst eller för en angiven API-åtgärd. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret. <br/><br/> Enhet: millisekunder <br/> Sammansättnings typ: genomsnitt <br/> Tillämpliga dimensioner: typ av typ, ApiName och autentisering ([definition](#metrics-dimensions)) <br/> Värde exempel: 1024 |
| Tillgänglighet | Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även förfrågningar som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. <br/><br/> Enhet: procent <br/> Sammansättnings typ: genomsnitt <br/> Tillämpliga dimensioner: typ av typ, ApiName och autentisering ([definition](#metrics-dimensions)) <br/> Värde exempel: 99,99 |