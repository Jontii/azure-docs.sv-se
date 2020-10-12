---
title: Geo haveri beredskap i Azure Event Grid | Microsoft Docs
description: Beskriver hur Azure Event Grid stöder geo Disaster Recovery (följande geodr) automatiskt.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: ccb16971020a65932daa8f9adf4b7cd9008a9253
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86105854"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Geo haveri beredskap på Server sidan i Azure Event Grid
Event Grid har nu en automatisk geo haveri beredskap (följande geodr) för meta-data som inte bara är till för nya, utan alla befintliga domäner, ämnen och händelse prenumerationer. Om en hel Azure-region stängs av kommer Event Grid redan att ha alla dina säkerhetsrelaterade infrastruktur-metadata synkroniserade med en kopplad region. De nya händelserna börjar flöda igen utan att du behöver göra något. 

Haveri beredskap mäts med två mått:

- [Återställnings punkt mål (återställnings punkt mål)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective): de minuter eller timmar med data som kan gå förlorade.
- [Mål för återställnings tid (RTO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective): hur många minuter tjänsten kan vara nere.

Event Grids automatiska redundans har olika återställnings punkter och återställnings tider för dina metadata (händelse prenumerationer osv.) och data (händelser). Om du behöver en annan specifikation från följande kan du fortfarande implementera en egen [klient växlings fel på klient sidan med hjälp av ämnet hälso-API: er](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Mål för återställningspunkt (RPO)
- Återställnings- **metadata**: noll minuter. När som helst en resurs skapas i Event Grid replikeras den genast i flera regioner. När en redundansväxling inträffar går inga metadata förlorade.
- Återställa **data**: om systemet är felfritt och har registrerats på befintlig trafik vid tidpunkten för regional redundansväxling, är återställningen av händelser på ungefär 5 minuter.

## <a name="recovery-time-objective-rto"></a>Mål för återställningstid (RTO)
- **Metadata RTO**: även om det vanligt vis sker mycket snabbare, inom 60 minuter kommer Event Grid att börja acceptera anrop för att skapa/uppdatera/ta bort för ämnen och prenumerationer.
- **Data RTO**: som metadata sker vanligt vis mycket snabbare, men inom 60 minuter börjar Event Grid att ta emot ny trafik efter en regional redundansväxling.

> [!NOTE]
> Kostnaden för följande geodr för metadata på Event Grid är: $0.


## <a name="next-steps"></a>Nästa steg
Om du vill implementera en egen redundansrelation på klient sidan, se [# skapa din egen haveri beredskap för anpassade ämnen i Event Grid](custom-disaster-recovery.md)
