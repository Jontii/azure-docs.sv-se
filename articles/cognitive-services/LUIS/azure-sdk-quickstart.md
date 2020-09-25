---
title: 'Snabb start: Language Understanding (LUIS) SDK-klientcertifikat'
description: Skapa och fråga en LUIS-app med LUIS SDK-klientcertifikat med den här snabb starten med C#, python eller Java Script.
ms.topic: quickstart
ms.date: 09/14/2020
ms.service: cognitive-services
ms.subservice: language-understanding
keywords: Azure, artificiell intelligens, AI, naturlig språk bearbetning, NLP, LUIS, Azure Luis, Natural Language förståelseing, AI chattrobot, chattrobot Maker, förstå naturligt språk
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-diberry-3core
ms.openlocfilehash: c2bc607fb5a5b5b38249453a1d0fce833547191f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327310"
---
# <a name="quickstart-language-understanding-luis-sdk-client-libraries-to-create-and-query-your-luis-app"></a>Snabb start: Language Understanding (LUIS) SDK-klientcertifikat för att skapa och skicka frågor till LUIS-appen

Skapa och fråga en Azure LUIS artificiell intelligens (AI)-app med klient biblioteken för LUIS SDK med den här snabb starten med C#, python eller Java Script.

Language Understanding (LUIS) gör det möjligt att använda naturlig språk bearbetning (NLP) till en användares konversation, naturligt språk text för att förutsäga den övergripande innebörden och hämta relevant, detaljerad information.

* Med klient biblioteket för **redigering av SDK** kan du skapa, redigera, träna och publicera Luis-appen. * Klient biblioteket för **förutsägelse körnings-SDK** gör det möjligt att fråga den publicerade appen.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort appen från [Luis-portalen](https://www.luis.ai) och ta bort Azure-resurserna från [Azure Portal](https://portal.azure.com/).

## <a name="troubleshooting"></a>Felsökning

* Autentisering till klient biblioteket – autentiseringsfel indikerar vanligt vis att fel nyckel & slut punkten användes. I den här snabb starten används redigerings nyckeln och slut punkten för förutsägelse körningen, som en bekvämlighet, men fungerar bara om du inte redan har använt den månatliga kvoten. Om du inte kan använda redigerings nyckeln och slut punkten måste du använda den förutsägelse körnings nyckeln och slut punkten vid åtkomst till klient biblioteket för förutsägelse körnings-SDK.
* Skapa entiteter – om du får ett fel när du skapar den kapslade enheten för maskin inlärning som används i den här självstudien, se till att du kopierade koden och inte ändrade koden för att skapa en annan entitet.
* Skapa exempel yttranden – om du får ett fel när du skapar den märkta exempel uttryck som används i den här självstudien, se till att du kopierade koden och inte ändrade koden för att skapa ett annat etikett exempel.
* Utbildning – om du får ett tränings fel indikerar detta vanligt vis en tom app (inga avsikter med exempel yttranden) eller en app med intenter eller entiteter som är felaktiga.
* Övriga fel – eftersom koden anropar klient biblioteken med text-och JSON-objekt ser du till att du inte har ändrat koden.

Andra fel – om du får ett fel som inte omfattas av föregående lista kan du meddela oss genom att ge feedback längst ned på den här sidan. Inkludera programmeringsspråk och version för de klient bibliotek som du har installerat.

## <a name="next-steps"></a>Nästa steg

* [Vad är API: et för Language Understanding (LUIS)?](what-is-luis.md)
* [Vad är det senaste?](whats-new.md)
* [Avsikter](luis-concept-intent.md), [entiteter](luis-concept-entity-types.md)och [exempel yttranden](luis-concept-utterance.md)och [färdiga entiteter](luis-reference-prebuilt-entities.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code).
* Förstå naturligt språk: [natur språks förståelse (NLU) och NLP (Natural Language Processing](artificial-intelligence.md) )
* Robotar: [AI-chattrobotar](luis-csharp-tutorial-bf-v4.md "själv studie kurs om chattrobot Maker")
