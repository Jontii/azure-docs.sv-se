---
title: Regler för namngivning av Azure Data Factory entiteter – version 1
description: Beskriver namngivnings regler för Data Factory v1-entiteter.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9a329a9c25b9ea85886938a170eb4194ca84643e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91282603"
---
# <a name="rules-for-naming-azure-data-factory-entities"></a>Regler för namngivning av Azure Data Factory entiteter
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [namngivnings regler i Data Factory](../naming-rules.md).

Följande tabell innehåller namngivnings regler för Data Factory artefakter.

| Namn | Namn unikhet | Verifierings kontroller |
|:--- |:--- |:--- |
| Data Factory |Unikt mellan Microsoft Azure. Namn är inte Skift läges känsliga, det `MyDF` vill säga och `mydf` referera till samma data fabrik. |<ul><li>Varje data fabrik är knuten till exakt en Azure-prenumeration.</li><li>Objekt namn måste börja med en bokstav eller en siffra och får bara innehålla bokstäver, siffror och bindestreck (-).</li><li>Varje bindestreck (-) måste föregås omedelbart och följas av en bokstav eller en siffra. Efterföljande bindestreck tillåts inte i behållar namn.</li><li>Namnet kan vara 3-63 tecken långt.</li></ul> |
| Länkade tjänster/tabeller/pipelines |Unikt med i en data fabrik. Namn är inte Skift läges känsliga. |<ul><li>Maximalt antal tecken i tabell namn: 260.</li><li>Objekt namn måste börja med en bokstav, en siffra eller ett under streck (_).</li><li>Följande tecken är inte tillåtna: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \\ "</li></ul> |
| Resursgrupp |Unikt mellan Microsoft Azure. Namn är inte Skift läges känsliga. |<ul><li>Maximalt antal tecken: 1000.</li><li>Namnet får innehålla bokstäver, siffror och följande tecken: "-", "_", "," och "."</li></ul> |

