---
title: Microsoft Azure StorSimple versions anmärkningar för Virtual Array-uppdatering 1,3 | Microsoft Docs
description: Beskriver kritiska öppna problem och lösningar för den virtuella Azure StorSimple-matrisen som kör uppdatering 1,3.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 02611bdf9689d2f62f661f558fd547ea46bd4d36
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744891"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>StorSimple Virtual Array Update 1,3 viktig information

I följande versions information identifieras kritiska öppna problem och lösta problem för Microsoft Azure StorSimple virtuell mat ris uppdatering.

Viktig information uppdateras kontinuerligt. Allteftersom lösningar på de allvarliga problemen upptäcks, läggs de till. Läs igenom informationen i viktig information innan du distribuerar din virtuella StorSimple-matris.

Uppdatering 1,3 motsvarar program varu versionen 10.0.10319.0.

> [!IMPORTANT]
> - Uppdatering 1,3 är en viktig uppdatering. Vi rekommenderar starkt att du installerar det så snart som möjligt.
> - Du kan bara installera uppdatering 1,3 på enheter som kör uppdatering 1,2.
> - Uppdateringar är störande och startar om enheten. Om I/O pågår förorsakar enheten avbrott. Detaljerade anvisningar om paket som används för att tillämpa den här uppdateringen finns i [Hämta uppdatering 1,3](#download-update-13).

## <a name="whats-new-in-update-13"></a>Nyheter i uppdatering 1,3

Den här uppdateringen innehåller följande förbättringar:

- Transport Layer Security (TLS) 1,2 är en obligatorisk uppdatering och måste vara installerad. I den här versionen kommer TLS 1,2 att bli standard protokoll för all Azure Portal kommunikation.
- Skräp insamling fel korrigeringar förbättra prestanda för skräp insamlings cykeln när enhets-och lagrings kontot finns i två regioner.
- Korrigering för säkerhets kopierings problem på grund av BLOB-timeout.
- Uppdaterade säkerhets korrigeringar för OS/. NET Framework:
  - [KB4540725](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Windows6.3\RTM\KB4540725\V1.001\free\NEU\X64): mars 2020 självbetjänings (uppdatering av underhålls stack)
  - [KB4565541](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Windows6.3\RTM\KB4565541\V1.014\free\NEU\X64): samlad 2020 juli
  - [KB4565622](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Partner\DOTNET47x\KB4565622\V1.000\free\NEU\x64): 2020 juli .NET Framework uppdatering

## <a name="download-update-13"></a>Hämta uppdatering 1,3

Om du vill hämta uppdateringen går du till [Microsoft Update katalog](https://www.catalog.update.microsoft.com/Home.aspx) Server och laddar ned KB4575898-paketet. Det här paketet innehåller följande paket:

- **KB4540725**, som innehåller kumulativa Windows-uppdateringar för 2012 R2 till 2020 mars. För mer information om vad som ingår i den här samlade uppdateringen, gå till [månatlig säkerhets](https://support.microsoft.com/help/4540725)insamling i mars.
- **KB4565541**, som innehåller kumulativa Windows-uppdateringar för 2012 R2 till 2020 juli. För mer information om vad som ingår i den här samlade uppdateringen, gå till [månatlig säkerhets](https://support.microsoft.com/help/4565541)insamling i februari.
- **KB4565622**, som innehåller Cumulative.NET Framework-uppdateringar upp till 2020 juli. För mer information om vad som ingår i den här samlade uppdateringen, gå till [månatlig säkerhets](https://support.microsoft.com/help/4565622)insamling i februari.
- **KB3011067**, som innehåller enhets program uppdateringar.

Hämta KB4575898 och följ de här anvisningarna för att [tillämpa uppdateringen via det lokala webb gränssnittet](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="known-issues-in-update-13"></a>Kända problem i uppdatering 1,3
Inga nya problem har frisläppts – anges i uppdatering 1,3. Alla problem med versions informationen överförs från tidigare versioner. Om du vill se en översikt över kända problem som ingår i tidigare versioner går du till [kända problem i uppdatering 1,2](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12).

## <a name="next-steps"></a>Nästa steg
Hämta KB4575898 och [Använd uppdateringen via det lokala webb gränssnittet](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui).

## <a name="references"></a>Referenser
Letar du efter en äldre versions anteckning? Gå till:

- [StorSimple Virtual Array Update 1,2 viktig information](./storsimple-virtual-array-update-12-release-notes.md)
- [StorSimple Virtual Array Update 1,0 viktig information](./storsimple-virtual-array-update-1-release-notes.md)
- [StorSimple Virtual Array Update 0,6 viktig information](./storsimple-virtual-array-update-06-release-notes.md)
- [StorSimple Virtual Array Update 0,5 viktig information](./storsimple-virtual-array-update-05-release-notes.md)
- [StorSimple Virtual Array Update 0,4 viktig information](./storsimple-virtual-array-update-04-release-notes.md)
- [StorSimple Virtual Array Update 0,3 viktig information](./storsimple-ova-update-03-release-notes.md)
- [StorSimple Virtual Array Update 0,1 och 0,2 viktig information](./storsimple-ova-update-01-release-notes.md)
- [Viktig information om allmän tillgänglighet för StorSimple Virtual Array](https://review.docs.microsoft.com/en-us/azure/storsimple/storsimple-ova-pp-release-notes)