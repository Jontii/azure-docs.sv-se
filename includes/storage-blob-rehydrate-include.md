---
title: inkludera fil
description: inkludera fil
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 53d2d47143c5a2cefbd50faca9a02af18ffae315
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84754632"
---
Om du vill läsa data i arkivlagring måste du först ändra nivå för din blob till frekvent eller lågfrekvent. Den här processen kallas ÅTERUPPVÄCKNING och kan ta timmar att slutföra. Vi rekommenderar stora BLOB-storlekar för optimal ÅTERUPPVÄCKNING-prestanda. Återställning av flera små blobbar samtidigt kan ta ännu längre tid. Det finns för närvarande två rehydratisera-prioriteter, hög och standard, som kan ställas in via den valfria egenskapen *x-MS-rehydratiseraable-Priority* på en [set BLOB-nivå](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) eller [Kopiera BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) -åtgärd.

* **Standard prioritet**: ÅTERUPPVÄCKNING-begäran kommer att bearbetas i den ordning som den togs emot och kan ta upp till 15 timmar.
* **Hög prioritet**: ÅTERUPPVÄCKNING-begäran prioriteras över standard begär Anden och kan slutföras under 1 timme för objekt som är under 10 GB stora. 

> [!NOTE]
> Standard prioritet är standard alternativet ÅTERUPPVÄCKNING för arkiv. Hög prioritet är ett snabbare alternativ som kommer att kosta mer än ÅTERUPPVÄCKNING med standard prioritet och är vanligt vis reserverat för användning i situationer med nöd Data återställning.
>
> Hög prioritet kan ta längre tid än en timme, beroende på BLOB-storlek och aktuell efter frågan. Begär Anden med hög prioritet garanteras att prioriteras över förfrågningar om standard prioritet.

När en ÅTERUPPVÄCKNING-begäran har initierats kan den inte avbrytas. Under ÅTERUPPVÄCKNING-processen kommer BLOB *-egenskapen x-MS-Access-Tier* att fortsätta att visas som arkiv tills ÅTERUPPVÄCKNING har slutförts till en onlinenivå. För att bekräfta ÅTERUPPVÄCKNING status och förlopp kan du anropa [Get BLOB-egenskaper](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties) för att kontrol lera BLOB-egenskaperna *x-MS-Archive-status* och *x-MS-rehydratiserad-Priority* . Status för arkivet kan läsa "rehydratisera-Pending-to-hett" eller "rehydratisera-Pending-to-kylning" beroende på den omhaltiga mål nivån. Den rehydratiserade prioriteten anger hastigheten "hög" eller "standard". När du är klar tas egenskaperna Arkiv status och rehydratiserad Priority bort och åtkomst nivåns BLOB-egenskap kommer att uppdateras för att avspegla den valda frekvent eller låg frekventa nivån.
