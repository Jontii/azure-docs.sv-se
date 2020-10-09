---
title: Välj en domän för ett Custom Vision projekt – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du väljer en domän för projektet i Custom Vision Service.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 1fb30cc0634224213dc9a188a16902e07d379904
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "82127777"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Välj en domän för ett Custom Vision-projekt

Från bladet inställningar för ditt Custom Vision-projekt kan du välja en domän för projektet. Välj den domän som är närmast ditt scenario.

## <a name="image-classification"></a>Bildklassificering

|Domän|Syfte|
|---|---|
|__Allmänna__| Optimerad för ett brett utbud av bild klassificerings aktiviteter. Om ingen av de andra domänerna är lämpliga, eller om du är osäker på vilken domän du väljer, väljer du den allmänna domänen.|
|__Kost__|Optimerad för foton av rätter på samma sätt som du ser dem på en restaurang meny. Om du vill klassificera fotografier av enskilda frukter eller grönsaker använder du livsmedels domänen.|
|__Landmärken__|Optimerad för identifierbara landmärken, både naturliga och artificiella. Den här domänen fungerar bäst när landmärket är tydligt synligt i fotografiet. Den här domänen fungerar även om landmärken är något som är något som står framför det.|
|__Retail__|Optimerad för avbildningar som finns i en shopping katalog eller shopping webbplats. Om du vill att hög precision ska klassificeras mellan dresses, Pants och skjortor använder du den här domänen.|
|__Komprimerade domäner__| Optimerad för begränsningar i real tids klassificering av gräns enheter.|

## <a name="object-detection"></a>Objektidentifiering

|Domän|Syfte|
|---|---|
|__Allmänt__| Optimerad för ett brett utbud av objekt identifierings aktiviteter. Om ingen av de andra domänerna är lämpliga, eller om du är osäker på vilken domän du väljer, väljer du den allmänna domänen.|
|__Logotyp__|Optimerad för att hitta varumärkes logo typer i bilder.|
|__Produkter på hyllor__|Optimerad för att identifiera och klassificera produkter på hyllor.|
|__Komprimerade domäner__| Optimerad för begränsningar av objekt identifiering i real tid på gräns enheter.|

## <a name="compact-domains"></a>Komprimerade domäner

Modeller som genereras av komprimerade domäner kan exporteras för att köras lokalt. Modell prestanda varierar beroende på vald domän. I tabellen nedan rapporterar vi modell storlek och härlednings tid för Intel Desktop-processor och NVidia GPU \[ 1 \] . 

> [!NOTE]
> De här talen omfattar inte förbehandling och postprocessing tid.

|Uppgift|Domän|Modell storlek|CPU-härlednings tid|GPU-härlednings tid|
|---|---|---|---|---|
|Klassificering|Allmän (kompakt)|5 MB|13 MS|5 MS|
|Objektidentifiering|Allmän (kompakt)|45 MB|35 MS|5 MS|
|Objektidentifiering|Allmän (kompakt) [S1]|14 MB|27 MS|7 MS|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK (vision AI dev Kit)

När en komprimerad domän väljs får du ett extra alternativ "export funktioner" som gör att du kan skilja mellan "Basic Platforms" och "vision AI dev kit".

Under _export funktioner_ är de två alternativen:

- Basic-plattformar (Tensorflow, CoreML, ONNX osv.)
- Vision AI dev kit.

När _vision AI dev kit_ är valt är _generiska_, _landmärken_och _detalj handel_ men _inte de visuella_ komprimerade domänerna tillgängliga för bild klassificering medan både _General (Compact)_ och _General (Compact) [S1]_ är tillgängliga för objekt identifiering.

>[!NOTE]
>__Allmän (komprimera)__ domän för objekt identifiering kräver speciell postprocessing-logik. Mer information finns i ett exempel skript i det exporterade zip-paketet. Om du behöver en modell utan postprocessing Logic använder du __General (Compact) [S1]__.

>[!IMPORTANT]
>Det finns ingen garanti för att de exporterade modellerna ger exakt samma resultat som förutsägelse-API: et i molnet. Mindre skillnader i den plattform som körs eller för bearbetning av Förbearbetning kan orsaka större skillnader i modellens utdata. Information om för bearbetnings logiken finns i [det här dokumentet](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5 – 2690 CPU och NVIDIA Tesla M60
