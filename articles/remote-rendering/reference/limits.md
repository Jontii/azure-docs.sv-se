---
title: Begränsningar
description: Kod begränsningar för SDK-funktioner
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: e6b12c2bac4a9732f868f6a6ac3491ef993f54c3
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90976564"
---
# <a name="limitations"></a>Begränsningar

Ett antal funktioner har storlek, antal eller andra begränsningar.

## <a name="azure-frontend"></a>Azure-frontend

* Totalt antal AzureFrontend-instanser per process: 16.
* Totalt antal AzureSession-instanser per AzureFrontend: 16.

## <a name="objects"></a>Objekt

* Totalt antal tillåtna objekt av en enskild typ (entitet, CutPlaneComponent osv.): 16 777 215.
* Totalt antal tillåtna aktiva klipp plan: 8.

## <a name="geometry"></a>Geometri

* Totalt tillåtet material i en till gång: 65 535. Mer information finns i avsnittet om att [duplicera material](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) .
* Högsta dimension för en enda textur: 16 384 x 16 384. Större käll texturer skalas ned av konverterings processen.

## <a name="overall-number-of-polygons"></a>Totalt antal polygoner

Det tillåtna antalet polygoner för alla laddade modeller beror på storleken på den virtuella datorn som skickas till [sessionen hanterings REST API](../how-tos/session-rest-api.md#create-a-session):

| Server storlek | Maximalt antal polygoner |
|:--------|:------------------|
|standard| 20 000 000 |
|denaturering| ingen gräns |

Mer detaljerad information om den här begränsningen finns i kapitel för [Server storlek](../reference/vm-sizes.md) .

## <a name="platform-limitations"></a>Plattforms begränsningar

**Windows 10 Desktop**

* Win32/x64 är den enda Win32-plattform som stöds. Win32/x86 stöds inte.

**HoloLens 2**

* [Kamera funktionen rendera från PV](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) stöds inte.
