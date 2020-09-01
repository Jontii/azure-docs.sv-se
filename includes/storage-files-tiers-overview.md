---
title: inkludera fil
description: inkludera fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7fd91e898c12a13e35ae8b9055ebb5a57de2a051
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89272149"
---
Azure Files erbjuder fyra olika nivåer av lagring, Premium, transaktion optimerad, frekvent och låg frekvent så att du kan skräddarsy dina resurser efter prestanda-och pris krav för ditt scenario:

- **Premium**: Premium-filresurser backas upp av solid state-hårddiskar (SSD) och distribueras i **FileStorage-lagrings konto** typen. Premium-filresurser ger konsekvent höga prestanda och låg latens i ensiffriga millisekunder för de flesta i/o-åtgärder för i/o-intensiva arbets belastningar. Detta gör dem lämpliga för en mängd olika arbets belastningar, t. ex. databaser, webbplats värdar och utvecklings miljöer. 
- **Transaktion optimerad**: transaktions optimerade fil resurser möjliggör transaktions intensiva arbets belastningar som inte behöver svars tiden som erbjuds av Premium-filresurser. Transaktions optimerade fil resurser erbjuds på den standard lagrings maskin vara som backas upp av hård diskar (HDD) och distribueras i **GPv2-lagrings konto typen (General Purpose version 2)** . Den här lagrings nivån har tidigare kallats "standard", men detta refererar till lagrings medie typen i stället för själva nivån (frekvent och låg frekvent är även "standard"-nivåer, eftersom de är på standard maskin vara för lagring).
- Frekvent **: Hot**fil resurser erbjuder lagring som är optimerad för generella fil delnings scenarier, till exempel team resurser och Azure File Sync. Frekventa fil resurser erbjuds den standard lagrings maskin vara som backas upp av hård diskar och distribueras i **GPv2-lagrings konto typen (General Purpose version 2)** .
- Låg **frekvent: låg**frekventa fil resurser erbjuder kostnads effektiv lagring som är optimerad för online-scenarier för Arkiv lag ring. Azure File Sync kan också vara en bra anpassning för mindre omsättnings arbets belastningar. Häftiga fil resurser erbjuds på den standard lagrings maskin vara som backas upp av hård diskar och distribueras i **GPv2-lagrings konto typen (General Purpose version 2)** .

Premium-filresurser är bara tillgängliga i en etablerad fakturerings modell. Mer information om den etablerade fakturerings modellen för Premium-filresurser finns i [förstå etablering för Premium-filresurser](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). Standard fil resurser, inklusive transaktions optimerings-, frekventa och häftiga fil resurser, är tillgängliga i en betala per användning-modell.

Frekventa och häftiga fil resurser är för närvarande tillgängliga i följande delmängd av offentliga regioner (transaktions optimerade fil resurser är tillgängliga i alla Azure-regioner):

- Australien, centrala
- Australien, centrala 2
- Australien, östra
- Australien, sydöstra
- Brasilien, södra
- Kanada, östra
- Kanada, centrala
- Frankrike, centrala
- Frankrike, södra
- Tyskland, norra (offentlig)
- Tyskland, västra centrala (offentlig)
- Indien, centrala
- Södra Indien
- Indien, västra
- Japan, östra
- Japan, västra
- Sydkorea, centrala
- Sydkorea, södra
- Östra Norge
- Norge, väst
- Sydafrika, norra
- Sydafrika, västra
- Schweiz, norra
- Schweiz, västra
- Förenade Arabemiraten Central
- Förenade Arabemiraten, norra
- Storbritannien, södra
- Storbritannien, västra
- USA, norra centrala
- USA, södra centrala

Information om hur du distribuerar en frekvent eller svag fil resurs finns i [skapa en frekvent eller låg frekvent fil resurs](../articles/storage/files/storage-how-to-create-file-share.md#create-a-hot-or-cool-file-share). 