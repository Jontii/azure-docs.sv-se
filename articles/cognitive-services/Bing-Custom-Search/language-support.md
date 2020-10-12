---
title: Språk stöd – API för anpassad Bing-sökning
titleSuffix: Azure Cognitive Services
description: En lista över språk och regioner som stöds för API för anpassad Bing-sökning.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 004bd973651d5903db4254a8883be2c8a83d9b38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84310588"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Språk-och region stöd för API för anpassad Bing-sökning

API för anpassad Bing-sökning har stöd för fler än tre dussin länder/regioner, många med fler än ett språk.

Även om det är valfritt, bör begäran ange Frågeparametern [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) , som identifierar den marknad där du vill att resultatet ska komma från. En lista med parametrar för valfria frågor finns i [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)

Du kan ange ett land/en region med hjälp av `cc` Frågeparametern. Om du anger ett land/en region måste du också ange en eller flera språk koder med hjälp av `Accept-Language` rubriken. Vilka språk som stöds varierar beroende på land/region; de anges för varje land/region i tabellen **marknad** .

`Accept-Language`Rubriken och `setLang` frågeparametern är ömsesidigt uteslutande – ange inte båda. Mer information finns i [Accept-language](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage).

## <a name="countriesregions"></a>Länder/regioner

|Land/region|Kod|
|-------|----|
|Argentina|AR|
|Australien|AU|
|Österrike|AT|
|Belgien|BE|
|Brasilien|BR|
|Kanada|CA|
|Chile|CL|
|Danmark|DK|
|Finland|FI|
|Frankrike|FR|
|Tyskland|DE|
|Folkrepubliken Kinas särskilda administrativa region Hongkong|HK|
|Indien|IN|
|Indonesien|ID|
|Italien|IT|
|Japan|JP|
|Korea|KR|
|Malaysia|MY|
|Mexico|MX|
|Nederländerna|NL|
|Nya Zeeland|NZ|
|Norge|NO|
|Kina|CN|
|Polen|PL|
|Portugal|PT|
|Filippinerna|PH|
|Ryssland|RU|
|Saudiarabien|SA|
|Sydafrika|ZA|
|Spanien|ES|
|Sverige|SE|
|Schweiz|CH|
|Taiwan|TW|
|Turkiet|TR|
|Storbritannien|GB|
|USA|USA|


## <a name="markets"></a>Marknaden

|Land/region|Språk|Marknads kod|
|-------|--------|-----------|
|Argentina|Spanska|ES-AR|
|Australien|Engelska|en – AU|
|Österrike|Tyska|avinstallation|
|Belgien|Nederländska|NL-vara|
|Belgien|Franska|fr-vara|
|Brasilien|Portugisiska|pt-BR|
|Kanada|Engelska|en-CA|
|Kanada|Franska|fr-CA|
|Chile|Spanska|ES-CL|
|Danmark|Danska|da-DK|
|Finland|Finska|fi-FI|
|Frankrike|Franska|fr-FR|
|Tyskland|Tyska|de-DE|
|Folkrepubliken Kinas särskilda administrativa region Hongkong|Traditionell kinesiska|zh-HK|
|Indien|Engelska|en-IN|
|Indonesien|Engelska|en-ID|
|Italien|Italienska|it-IT|
|Japan|Japanska|ja-JP|
|Korea|Koreanska|ko-KR|
|Malaysia|Engelska|en-MY|
|Mexico|Spanska|ES – MX|
|Nederländerna|Nederländska|nl-NL|
|Nya Zeeland|Engelska|en-NZ|
|Norge|Norska|ingen – nej|
|Kina|Kinesiska|zh-CN|
|Polen|Polska|pl-PL|
|Portugal|Portugisiska|pt-PT|
|Filippinerna|Engelska|en-PH|
|Ryssland|Ryska|ru-RU|
|Saudiarabien|Arabiska|ar-SA|
|Sydafrika|Engelska|en-ZA|
|Spanien|Spanska|es-ES|
|Sverige|Svenska|sv-SE|
|Schweiz|Franska|Frankrike-CH|
|Schweiz|Tyska|de-CH|
|Taiwan|Traditionell kinesiska|zh-TW|
|Turkiet|Turkiska|tr-TR|
|Storbritannien|Engelska|en-GB|
|USA|Engelska|sv-SE|
|USA|Spanska|ES – USA|
