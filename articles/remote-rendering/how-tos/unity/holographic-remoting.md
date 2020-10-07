---
title: Använda Holographic-fjärrstyrning och Remote Rendering i Unity
description: Hur Holographic för fjärrhantering kan användas i kombination med Azure-fjärrrendering
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: 180af30f57a8123b6e90cc8b11848b92b3c86db1
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802182"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Använda Holographic-fjärrstyrning och Remote Rendering i Unity

[Holographic fjärr kommunikation](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) och Azure fjärrrendering är ömsesidigt uteslutande i ett program. Enhets [uppspelnings läget](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) är inte heller tillgängligt.

För varje körning av Unity-redigeraren går det bara att använda en av de två. Om du vill använda den andra måste du först starta om den.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Använd enhets förhands visnings läge för för hands versionen på HoloLens 2

 Enhets uppspelnings läget kan fortfarande användas, till exempel för att testa programmets användar gränssnitt. Det är dock viktigt att ARR aldrig initieras. Annars kommer den att krascha.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Använd ett WMR VR-headset för att förhandsgranska på Skriv bordet

Om det finns ett headset i Windows Mixed Reality kan du använda det för att för hands versionen av enheten. I det här fallet är det bra att initiera ARR, men det går inte att ansluta till en session medan WMR-headsetet används.
