---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 3d4c908e0caf1cf84159df49d98603fd13b75994
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821561"
---
Hantering av komprimerat ljud implementeras med [gstreamer](https://gstreamer.freedesktop.org). Av licens skäl GStreamer binärfiler inte kompileras och länkas till tal-SDK: n. Utvecklare måste installera flera beroenden och plugin-program, se [installera på Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) eller [Installera i Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). GStreamer-binärfiler måste finnas i System Sök vägen, så att tal-SDK kan läsa in binärfilerna under körningen. Om exempelvis talet SDK kan hitta under körning i Windows, `libgstreamer-1.0-0.dll` innebär det att gstreamer-binärfilerna finns i System Sök vägen.

