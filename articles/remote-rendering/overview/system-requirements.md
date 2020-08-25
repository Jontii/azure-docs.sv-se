---
title: Systemkrav
description: Visar en lista över system kraven för Azure Remote rendering
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: 81480bea735017d3fc59e9c6cf126c2146a0c968
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798473"
---
# <a name="system-requirements"></a>Systemkrav

> [!IMPORTANT]
> **Azure Remote rendering** är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I det här kapitlet anges de minsta system kraven för att arbeta med *Azure Remote rendering* (arr).

## <a name="development-pc"></a>Utvecklings dator

* Windows 10 version 1903 eller senare.
* Uppdaterade grafik driv rutiner.
* Valfritt: [H265 för maskin vara](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)om du vill använda en lokal förhands granskning av fjärrrenderat innehåll (till exempel i Unity).

> [!IMPORTANT]
> Windows Update levererar inte alltid de allra senaste GPU-drivrutinerna, kontrol lera GPU-tillverkarens webbplats för de senaste driv rutinerna:
>
> * [AMD-drivrutiner](https://www.amd.com/en/support)
> * [Intel-drivrutiner](https://www.intel.com/content/www/us/en/support/detect.html)
> * [NVIDIA-drivrutiner](https://www.nvidia.com/Download/index.aspx)

Tabellen nedan visar vilka GPU: er som stöder H265 av maskin varu video.

| GPU-tillverkare | Modeller som stöds |
|-----------|:-----------|
| NVIDIA | Kontrol lera **NVDEC support Matrix** längst [ned på den här sidan](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix). Din GPU behöver ett ja i kolumnen **H. 265 4:2:0 8-bit** . |
| EFFEKTIV | GPU: er med minst version 6 av AMD: s [Unified Video Decoder](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6). |
| Intel | Skylake och nyare processorer |

Även om rätt H265-codec kan vara installerad kan säkerhets egenskaper för codec-dll: er orsaka codec-initierings fel. [Fel söknings guiden](../resources/troubleshoot.md#h265-codec-not-available) beskriver steg hur du löser det här problemet. DLL-problemet kan bara utföras när tjänsten används i ett Skriv bords program, till exempel i Unity.

## <a name="devices"></a>Enheter

Azure Remote rendering stöder för närvarande bara **HoloLens 2** och Windows Desktop som en målenhet. Se avsnittet [plattforms begränsningar](../reference/limits.md#platform-limitations) .

Det är viktigt att använda den senaste HEVC-codecen, eftersom nya versioner har betydande förbättringar i svars tiden. Så här kontrollerar du vilken version som är installerad på enheten:

1. Starta **Microsoft Store**.
1. Klicka på knappen **"..."** längst upp till höger.
1. Välj **nedladdningar och uppdateringar**.
1. Sök i listan efter **hevc video tillägg från enhets tillverkaren**. Om det här objektet inte visas under uppdateringar är den senaste versionen redan installerad.
1. Kontrol lera att den angivna codecen har minst version **1.0.21821.0**.
1. Klicka på knappen **Hämta uppdateringar** och vänta tills den har installerats.

## <a name="network"></a>Nätverk

En stabil nätverks anslutning med låg fördröjning är avgörande för en bättre användar upplevelse.

Se särskilda kapitel för [nätverks krav](../reference/network-requirements.md).

Information om fel sökning av nätverks problem finns i [fel söknings guiden](../resources/troubleshoot.md#unstable-holograms).

## <a name="software"></a>Programvara

Följande program vara måste vara installerad:

* Den senaste versionen av **Visual Studio 2019** [(Hämta)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio Tools för Mixad verklighet](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). Mer specifikt är följande *arbets belastnings* installationer obligatoriska:
  * **Skriv bords utveckling med C++**
  * **Universell Windows-plattform (UWP) utveckling**
* **Windows SDK 10.0.18362.0** [(Hämta)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **Git** [(nedladdning)](https://git-scm.com/downloads)
* Valfritt: om du vill visa video strömmen från servern på en stationär dator behöver du **hevc video Extensions** [(Microsoft Store länk)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7). Kontrol lera att den senaste versionen är installerad genom att söka efter uppdateringar i butiken.

## <a name="unity"></a>Unity

För utveckling med Unity, installera

* Unity 2019.3.1 [(Hämta)](https://unity3d.com/get-unity/download)
* Installera de här modulerna i Unity:
  * **UWP** -stöd för Build-universell Windows-plattform
  * **IL2CPP** – stöd för Windows-build (IL2CPP)

## <a name="next-steps"></a>Nästa steg

* [Snabb start: rendera en modell med Unity](../quickstarts/render-model.md)
