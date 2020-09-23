---
title: Jämförelse av Azure Stream Analyticss funktion
description: I den här artikeln jämförs de funktioner som stöds för Azure Stream Analytics moln-och IoT Edges jobb i Azure Portal, Visual Studio och Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 2ba7dc3b0f2bc4f62234c480da0af1061dea1f91
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885569"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Jämförelse av Azure Stream Analyticss funktion

Med Azure Stream Analytics kan du skapa strömmande lösningar i molnet och på IoT Edge med hjälp av [Azure Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)och [Visual Studio Code](quick-create-visual-studio-code.md). Tabellerna i den här artikeln visar vilka funktioner som stöds av varje plattform för båda jobb typerna.

> [!NOTE]
> Visual Studio-och Visual Studio Code-verktyg stöder inte jobb i regionerna Kina, östra, Kina, norra, Tyskland, centrala och Tyskland nordöstra.

## <a name="cloud-job-features"></a>Funktioner i moln jobb


|Funktion  |Portalen  |Visual Studio  |Visual Studio-koden  |
|---------|---------|---------|---------|
|Plattforms oberoende     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Skript redigering     |Ja         |Ja         |Ja         |
|IntelliSense för skript     |Markering av syntax         |Markering av syntax</br>Kod komplettering</br>Fel markör         |Markering av syntax</br>Kod komplettering</br>Fel markör         |
|Definiera alla typer av indata, utdata och jobb konfiguration     |Ja         |Ja         |Ja         |
|Källkontroll     |Nej         |Ja         |Ja         |
|CI/CD-stöd     |Delvis         |Ja         |Ja         |
|Dela indata och utdata över flera frågor     |Nej         |Ja         |Ja         |
|Fråga testning med en exempel fil     |Ja         |Ja        |Ja         |
|Lokal testning av Live data     |Nej         |Ja       |Ja      |
|Visa lista över jobb och Visa jobb enheter     |Ja         |Ja        |Ja         |
|Exportera ett jobb till ett lokalt projekt     |Nej         |Ja         |Ja         |
|Skicka, starta och stoppa jobb     |Ja         |Ja         |Ja         |
|Visa jobb mått och diagram     |Ja         |Ja         |Öppna i portalen         |
|Visa jobb körnings fel     |Ja         |Ja         |Nej         |
|Resursloggar     |Ja         |Nej         |Nej         |
|Egenskaper för anpassat meddelande     |Ja         |Ja         |Nej       |
|Anpassad kod funktion i C# och deserialiserare|Skrivskyddat läge|Ja|Nej|
|JavaScript UDF och UDA     |Ja         |Ja         |Endast Windows         |
|Machine Learning Service     |Ja        |Ja         |Nej         |
|Machine Learning Studio     |Ja, men det går inte att testa frågan        |Ja |Nej         |
|Efterlevnadsnivå     |1,0</br>1,1</br>1,2 (standard)         |1,0</br>1,1</br>1,2 (standard)           |1,0</br>1,1</br>1,2 (standard)           |
|Inbyggda ML-baserade avvikelse identifierings funktioner     |Ja         |Ja         |Ja         |
|Inbyggda GeoSpatiala funktioner     |Ja         |Ja         |Ja         |



## <a name="iot-edge-job-features"></a>IoT Edge jobb funktioner

|Funktion  |Portalen  |Visual Studio  |Visual Studio-koden  |
|---------|---------|---------|---------|
|Jobb redigering     |Ja         |Ja         |Nej         |
|Källkontroll     |Nej         |Ja         |Nej         |
|Exportera ett jobb till ett lokalt projekt     |Nej         |Ja         |Nej         |
|Fråga testning med en exempel fil     |Ja         |Ja         |Nej         |
|Dela indata och utdata över flera frågor     |Nej         |Ja         |Nej         |
|UDF för C#     |Nej         |Ja         |Nej         |
|Skicka jobb     |Ja         |Ja         |Nej         |
|Visa lista över jobb och Visa jobb enheter     |Ja         |Ja         |Nej         |
|Visa jobb mått och diagram     |Yes         |Delvis         |No         |
|Visa jobb körnings fel     |Yes         |Delvis         |No         |
|CI/CD-stöd     |Nej         |Nej         |Nej         |


## <a name="next-steps"></a>Nästa steg

* [Azure Stream Analytics på IoT Edge](stream-analytics-edge.md)
* [Självstudie: skriva en C#-användardefinierad funktion för Azure Stream Analytics IoT Edge jobb (för hands version)](stream-analytics-edge-csharp-udf.md)
* [Utveckla Stream Analytics IoT Edge jobb med Visual Studio-verktyg](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)
* [Utforska Azure Stream Analytics med Visual Studio Code (för hands version)](visual-studio-code-explore-jobs.md)


