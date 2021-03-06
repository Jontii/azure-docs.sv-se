---
title: Azure Media Services äldre komponenter | Microsoft Docs
description: I det här avsnittet beskrivs Azure Media Services äldre komponenter.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: 495b6bc68e79e50a0bda773e7229225595236fac
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98694350"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services äldre komponenter

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Med tiden förbättrar vi medie tjänst komponenter och tar sedan bort gamla komponenter. Den här artikeln hjälper dig att migrera ditt program från en äldre komponent till en aktuell komponent.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Indragnings planer för äldre komponenter och vägledning för migrering

Medie processorerna för *Windows Azure Media Encoder* (WAME) och *Azure Media Encoder* (amn) är föråldrade.

* [Migrera från Windows Azure Media Encoder till Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrera från Azure Media Encoder till Media Encoder Standard](migrate-azure-media-encoder.md)

Följande Medieanalys medie processorer är antingen inaktuella eller snart inaktuella:

  
 
| **Namn på mediebearbetare** | **Datum för indragning** | **Ytterligare information** |
| --- | --- | ---|
| Azure Media Indexer 2 | 1 januari 2020 | Medie processorn kommer att ersättas av [Media Services v3 AudioAnalyzerPreset Basic-läge](../latest/analyzing-video-audio-files-concept.md). Mer information finns i [Migrera från Azure Media Indexer 2 till Azure Media Services video Indexer](migrate-indexer-v1-v2.md). |
| Azure Media Indexer | 1 mars 2023 | Medie processorn kommer att ersättas av [Media Services v3 AudioAnalyzerPreset Basic-läge](../latest/analyzing-video-audio-files-concept.md). Mer information finns i [Migrera från Azure Media Indexer 2 till Azure Media Services video Indexer](migrate-indexer-v1-v2.md). |
| Rörelse identifiering | 1 juni 2020|Inga ersättnings planer för tillfället. |
| Video Sammanfattning |1 juni 2020|Inga ersättnings planer för tillfället.|
| Optisk typsnitts igenkänning i video | 1 juni 2020 |Medie processorn har ersatts av [Azure Media Services video Indexer](../video-indexer/index.yml). Överväg också att använda [Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Se [jämföra Azure Media Services v3 för inställningar och video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md). |
| Ansiktsigenkänning | 1 juni 2020 | Medie processorn har ersatts av [Azure Media Services video Indexer](../video-indexer/index.yml). Överväg också att använda [Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Se [jämföra Azure Media Services v3 för inställningar och video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md). |
| Content Moderator | 1 juni 2020 |Medie processorn har ersatts av [Azure Media Services video Indexer](../video-indexer/index.yml). Överväg också att använda [Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Se [jämföra Azure Media Services v3 för inställningar och video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md). |

## <a name="next-steps"></a>Nästa steg

[Vägledning för migrering för att flytta från Media Services v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)
