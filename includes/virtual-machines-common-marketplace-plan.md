---
title: inkludera fil
description: inkludera fil
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8e0e549f88caf4a541642bab77faf54b5e536b29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "71174996"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Distribuera en avbildning med Marketplace-villkor

Vissa VM-avbildningar på Azure Marketplace har ytterligare licens-och Köp villkor som du måste acceptera innan du kan distribuera dem program mässigt.  

Om du vill distribuera en virtuell dator från en sådan avbildning måste du både acceptera avbildningens villkor och aktivera program distribution. Du behöver bara göra detta en gång per prenumeration. Efteråt, varje gången du distribuerar en virtuell dator program mässigt från avbildningen, måste du också ange *inköps plan* parametrar.

I följande avsnitt visas hur du:

* Ta reda på om en Marketplace-avbildning har ytterligare licens villkor 
* Godkänn villkoren program mässigt
* Ange parametrar för inköps planer när du distribuerar en virtuell dator program mässigt

