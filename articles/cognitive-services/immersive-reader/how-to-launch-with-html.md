---
title: Starta Avancerad läsare med HTML-innehåll
titleSuffix: Azure Cognitive Services
description: I den här artikeln visas hur du startar den fördjupade läsaren med HTML-innehåll.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-js
ms.openlocfilehash: f7f55d1d349fc7d02c2ac53f7a50f9d649688cbd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326770"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>Så här startar du den fördjupade läsaren med HTML-innehåll

Den här artikeln visar hur du startar den fördjupade läsaren med HTML-innehåll.

## <a name="prepare-the-html-content"></a>Förbereda HTML-innehållet

Placera det innehåll som du vill återge i den fördjupade läsaren i ett behållar element. Se till att behållar elementet har ett unikt `id` . Den fördjupade läsaren tillhandahåller stöd för grundläggande HTML-element, se [referensen](./reference.md#html-support) för mer information.

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>Hämta HTML-innehåll i Java Script

Använd `id` elementet container för att hämta HTML-innehållet i JavaScript-koden.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Starta den fördjupade läsaren med ditt HTML-innehåll

När du anropar `ImmersiveReader.launchAsync` ställer du in segmentets `mimeType` egenskap till `text/html` för att aktivera åter givning av HTML.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Nästa steg

* Utforska [SDK-referensen för avancerad läsare](./reference.md)