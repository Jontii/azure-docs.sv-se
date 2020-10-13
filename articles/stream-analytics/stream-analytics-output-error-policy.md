---
title: Principer för utgående fel i Azure Stream Analytics
description: Lär dig mer om de fel hanterings principer som är tillgängliga i Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 22112272bb302769e5969cf6995d486438deb41f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "75431614"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Azure Stream Analytics fel princip för utdata
I den här artikeln beskrivs de fel hanterings principer för utdata som kan konfigureras i Azure Stream Analytics.

Hanterings principer för utgående data gäller endast för data konverterings fel som uppstår när utdata som genereras av ett Stream Analytics jobb inte följer schemat för mål mottagaren. Du kan konfigurera den här principen genom att välja antingen **försök igen** eller **ta bort**. I Azure Portal, i ett Stream Analytics jobb, under **Konfigurera**väljer du **fel princip** för att göra ditt val.

![Azure Stream Analytics plats för fel princip för utdata](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Försök igen
När ett fel uppstår försöker Azure Stream Analytics att skriva om händelsen oändligt tills skrivningen lyckas. Det finns ingen tids gräns för återförsök. Till sist blockeras alla efterföljande händelser från bearbetningen av händelsen som försöker igen. Det här alternativet är standard principen för fel hantering av utdata.

## <a name="drop"></a>Skugg
Azure Stream Analytics utelämnar eventuella utdatahändelser som resulterar i datakonverteringsfel. Utelämnade händelser kan inte återställas för bearbetning vid ett senare tillfälle.


Alla tillfälliga fel (t. ex. nätverks fel) görs nya försök oavsett princip konfigurationen för hantering av utdata.


## <a name="next-steps"></a>Nästa steg
[Fel söknings guide för Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
