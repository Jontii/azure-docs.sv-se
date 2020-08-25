---
title: 'Självstudie: mönster – LUIS'
description: Använd mönster för att öka avsikt och enhets förutsägelse samtidigt som du ger färre exempel yttranden i den här självstudien. Mönstret anges som en mall uttryck exempel som innehåller syntax för att identifiera entiteter och text som kan ignoreras.
ms.topic: tutorial
ms.date: 07/06/2020
ms.openlocfilehash: 3ca8bb15d19b0fa0dd6b33d35a380c0b1b07abe0
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "86039508"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Självstudie: Lägg till vanliga mönster mal len uttryck format för att förbättra förutsägelser

I den här självstudien använder du mönster för att öka avsikt och enhets förutsägelse, vilket gör att du kan ange färre exempel yttranden. Mönstret är en mall som uttryck tilldelas till en avsikt, som innehåller syntax för att identifiera entiteter och text som kan ignoreras.

**I den här guiden får du lära dig att:**

> [!div class="checklist"]
> * Skapa ett mönster
> * Verifiera förbättringar av mönsterförutsägelser
> * Markera text ignorerbar och kapsla i mönstret
> * Använda testpanel för att verifiera lyckat mönster

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>Yttranden i avsikt och mönster

Det finns två typer av yttranden lagrade i LUIS-appen:

* Exempel på yttranden i avsikten
* Mallens yttranden i mönstret

Genom att lägga till mallen yttranden som ett mönster kan du ange färre exempel-yttranden för ett avsikts sätt.

Ett mönster tillämpas som en kombination av textmatchning och maskininlärning.  Malltalindata i mönstret tillsammans med exempeltalindata i avsikten ger LUIS bättre förståelse för vilka talindata som passar avsikten.

## <a name="import-example-app-and-clone-to-new-version"></a>Importera exempel App och klon till ny version

Gör så här:

1.  Ladda ned och spara [app-JSON-filen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true).

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Importera JSON till en ny app i Luis- [portalen](https://www.luis.ai). På sidan **Mina appar** väljer du **+ ny app för konversation**och väljer sedan **Importera som JSON**. Välj den fil som du laddade ned i föregående steg, namnge appen, `Patterns tutorial` .

## <a name="create-new-intents-and-their-utterances"></a>Skapa nya avsikter och deras yttranden

De två avsikterna hittar chefen eller chefens direkt rapporter baserat på uttryck-texten. Det är svårt att de två avsikterna _betyder_ olika saker men de flesta av orden är desamma. Endast ord ordningen är annorlunda. För att avsikten ska kunna förutsägas korrekt måste det ha många exempel.

1. Välj **build** i navigerings fältet.

1. På sidan **avsikter** väljer du **+ skapa** för att skapa en ny avsikt.

1. Ange `OrgChart-Manager` i popup-dialogrutan och välj sedan **Done** (Klar).

    ![Skapa ett nytt popup-fönster för meddelanden](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. Lägg till exempel på yttranden i avsikten. Dessa yttranden är inte _lika identiska_ , men har ett mönster som kan extraheras.

    |Exempel på yttranden|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

1. Välj **Intents** (Avsikter) i det vänstra navigeringsfältet.

1. Välj **+ skapa** för att skapa en ny avsikt. Ange `OrgChart-Reports` i popup-dialogrutan och välj sedan **Done** (Klar).

1. Lägg till exempel på yttranden i avsikten.

    |Exempel på yttranden|
    |--|
    |`Who are John W. Smith's subordinates?`|
    |`Who reports to John W. Smith?`|
    |`Who does John W. Smith manage?`|
    |`Who are Jill Jones direct reports?`|
    |`Who does Jill Jones supervise?`|

### <a name="caution-about-example-utterance-quantity"></a>Varning om kvantiteten för exempeluttryck

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

### <a name="train-the-app-before-testing-or-publishing"></a>Träna appen innan du testar eller publicerar

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicera appen att fråga från slutpunkten

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>Hämta avsikter och entiteter från slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Gå till slutet av webb adressen i adress fältet och Ersätt _YOUR_QUERY_HERE_ med: `Who is the boss of Jill Jones?` .

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.326605469
                },
                "OrgChart-Reports": {
                    "score": 0.127583548
                },
                "EmployeeFeedback": {
                    "score": 0.0299124215
                },
                "MoveEmployee": {
                    "score": 0.01159851
                },
                "GetJobInformation": {
                    "score": 0.0104600191
                },
                "ApplyForJob": {
                    "score": 0.007508645
                },
                "Utilities.StartOver": {
                    "score": 0.00359402061
                },
                "Utilities.Stop": {
                    "score": 0.00336530479
                },
                "FindForm": {
                    "score": 0.002653719
                },
                "Utilities.Cancel": {
                    "score": 0.00263288687
                },
                "None": {
                    "score": 0.00238638581
                },
                "Utilities.Help": {
                    "score": 0.00226386427
                },
                "Utilities.Confirm": {
                    "score": 0.00211663754
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Rätt Top-avsikt förväntades, `OrgChart-Manager` men poängen är inte över 70% och är inte tillräckligt lång för nästa högsta avsikt. Använd mönster för att göra den korrekta avsiktens poäng betydligt högre procentuellt och längre ifrån den näst högsta poängen.

Lämna det här andra webbläsarfönstret öppet. Du kommer att använda den igen senare i självstudien.

## <a name="template-utterances"></a>Mallyttranden
På grund av typen av mänsklig resurs ämnes domän finns det några vanliga sätt att fråga om medarbetar relationer i organisationer. Ett exempel:

|Yttranden|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Dessa yttranden är för nära för att fastställa den sammanhangsbaserade unika kontexten för var och en utan att tillhandahålla _många_ uttryck-exempel. Genom att lägga till ett mönster för ett avsikts sätt lär sig LUIS vanliga uttryck-mönster för en avsikt utan att du behöver tillhandahålla många uttryck-exempel.

Exempel på mallyttranden för den här avsikten är:

|Exempel på mallyttranden|syntaxbetydelse|
|--|--|
|`Who does {EmployeeListEntity} report to[?]`|utbytbara `{EmployeeListEntity}`<br>Ignorera `[?]`|
|`Who reports to {EmployeeListEntity}[?]`|utbytbara `{EmployeeListEntity}`<br>Ignorera `[?]`|

Syntaxen `{EmployeeListEntity}` markerar entitetsplatsen i mallyttrandet och vilken entitet det är. Valfri syntax, `[?]` , markerar ord eller [interpunktion](luis-reference-application-settings.md#punctuation-normalization) som är valfria. LUIS matchar yttrandet, ignorerar den valfria texten inom hakparentes.

När syntaxen ser ut som ett reguljärt uttryck är det inte ett reguljärt uttryck. Endast syntax inom klamrar, `{}`, och hakparentes, `[]`, stöds. De kan kapslas upp till två nivåer.

För att ett mönster ska matchas med en uttryck måste _först_ entiteterna i uttryck matcha entiteterna i mallen uttryck. Det innebär att entiteterna måste ha tillräckligt många exempel i exempel yttranden med en hög grad av förutsägelse innan mönster med entiteter har lyckats. Men mallen hjälper inte att förutsäga entiteter, bara avsikter.

**Med mönster kan du ge färre exempelyttranden, men om entiteterna inte identifieras matchar inte mönstret.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Lägg till mönster för organisationens chefs avsikt

1. Välj **Build** (Skapa) i huvudmenyn.

1. I den vänstra navigeringen, under **Improve app performance** (Förbättra appens prestanda) väljer du **Patterns** (Mönster) i den vänstra navigeringen.

1. Välj avsikten **OrgChart-Manager** och ange sedan följande mallyttranden:

    |Mallyttranden|
    |:--|
    |`Who is {EmployeeListEntity} the subordinate of[?]`|
    |`Who does {EmployeeListEntity} report to[?]`|
    |`Who is {EmployeeListEntity}['s] manager[?]`|
    |`Who does {EmployeeListEntity} directly report to[?]`|
    |`Who is {EmployeeListEntity}['s] supervisor[?]`|
    |`Who is the boss of {EmployeeListEntity}[?]`|

    Dessa mallar yttranden inkluderar entiteten **EmployeeListEntity** med klammerns notation.

1. Medan du fortfarande finns på sidan mönster väljer du **schema för organisations schema** , och anger sedan följande mall yttranden:

    |Mallyttranden|
    |:--|
    |`Who are {EmployeeListEntity}['s] subordinates[?]`|
    |`Who reports to {EmployeeListEntity}[?]`|
    |`Who does {EmployeeListEntity} manage[?]`|
    |`Who are {EmployeeListEntity} direct reports[?]`|
    |`Who does {EmployeeListEntity} supervise[?]`|
    |`Who does {EmployeeListEntity} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Frågeslutpunkt där mönster används

Nu när mönstren har lagts till i appen, träna, publicera och fråga appen på den förutsägelse körnings slut punkten.

1. Välj **Träna** (Träna). När utbildningen är klar väljer du **publicera** och väljer **produktions** platsen och väljer sedan **klar**.

1. När publiceringen är klar växlar du tillbaka till fliken slut punkts-URL i webbläsarens flik.

1. Gå till slutet av webb adressen i adress fältet och kontrol lera att din fråga fortfarande `Who is the boss of Jill Jones?` skickar in URL: en för en ny förutsägelse.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999999046
                },
                "OrgChart-Reports": {
                    "score": 3.237443E-05
                },
                "EmployeeFeedback": {
                    "score": 4.364242E-06
                },
                "GetJobInformation": {
                    "score": 1.616159E-06
                },
                "MoveEmployee": {
                    "score": 7.575752E-07
                },
                "ApplyForJob": {
                    "score": 5.234157E-07
                },
                "None": {
                    "score": 3.3E-09
                },
                "Utilities.StartOver": {
                    "score": 1.26E-09
                },
                "FindForm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Cancel": {
                    "score": 1.13636367E-09
                },
                "Utilities.Confirm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Help": {
                    "score": 1.13636367E-09
                },
                "Utilities.Stop": {
                    "score": 1.13636367E-09
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Avsikten med avsikten är nu avsevärt mer tryggt och den näst högsta avsikten är mycket låg. Dessa två avsikter vänder inte sig på vippa vid träning.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Arbeta med valfri text och fördefinierade entiteter

Föregående mallyttranden för mönster i den här kursen hade några exempel på valfri text, som possessiv användning av bokstaven s `'s` (se exemplen på engelska) och användningen av frågetecken, `?`. Anta att du behöver tillåta aktuella och framtida datum i uttryck-texten.

Exempelyttranden är:

|Avsikt|Exempelyttranden med valfri text och fördefinierade entiteter|
|:--|:--|
|OrgChart-Manager (Organisationsschema-Chef)|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager (Organisationsschema-Chef)|`Who is Jill Jones manager now?`|
|OrgChart-Manager (Organisationsschema-Chef)|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager (Organisationsschema-Chef)|`Who will be Jill Jones manager on March 3?`|

Alla dessa exempel använder ett verbtempus, `was`, `is`, `will be` samt ett datum, `March 3`, `now` och `in a month`, som LUIS behöver för att förutsäga korrekt. Observera att de två sista exemplen i tabellen använder nästan samma text förutom `in` och `on` .

Exempel mal yttranden som tillåter denna valfria information:

|Avsikt|Exempelyttranden med valfri text och fördefinierade entiteter|
|:--|:--|
|OrgChart-Manager (Organisationsschema-Chef)|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager (Organisationsschema-Chef)|`who is {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|


Användningen av valfri syntax inom hakparentes, `[]`, gör den här valfria texten enkel att lägga till i mallyttrandet och kan kapslas upp till en andra nivå, `[[]]`, och innehåller entiteter eller text.


**Fråga: Varför är alla `w` bokstäver, den första bokstaven i varje mall uttryck, gemener? Bör de inte vara alternativt övre eller små?** Yttrandet som skickas till frågeslutpunkten, av frågeslutpunkten, konverteras till gemener. Mallyttrandet kan vara versaler eller gemener och slutpunktsyttrandet kan också vara antingen eller. Jämförelsen görs alltid efter konverteringen till gemener.

**Fråga: Varför är inte fördefinierat tal ett mallyttrande om 3 mars förutsägs både som talet `3` och datumet `March 3`?** Mallyttrandet använder kontextuellt ett datum, antingen bokstavligen som i `March 3` eller abstraheras som `in a month`. Ett datum innehåller ett tal men ett tal kanske inte nödvändigtvis ses som ett datum. Använd alltid entiteten som bäst representerar den typ du vill ska returneras i resultatet för förutsägelse-JSON.

**Fråga: Hur är det med dåligt uttryckta yttranden som `Who will {EmployeeListEntity}['s] manager be on March 3?`?** Grammatiskt olika verbtempus som det här där `will` och `be` är åtskilda måste vara ett nytt mallyttrande. Det befintliga mallyttrandet kommer inte att matcha det. Avsikten för yttrandet har inte ändrats men ordets placering i yttrandet har ändrats. Den här ändringen påverkar förutsägelsen i LUIS. Du kan [gruppera och eller](#use-the-or-operator-and-groups) verben för att kombinera dessa yttranden.

> [!CAUTION]
> **Kom ihåg: entiteter hittas först, sedan matchas mönstret.**

### <a name="add-new-pattern-template-utterances"></a>Lägga till nya mallyttranden för mönster

1. Lägg till flera nya mallyttranden för mönster när du fortfarande är i avsnittet **Patterns** (Mönster) i **Build** (Skapa). Välj **OrgChart-Manager** i den nedrullningsbara menyn Intent (Avsikt) och ange vart och ett av följande yttranden:

    |Avsikt|Exempelyttranden med valfri text och fördefinierade entiteter|
    |--|--|
    |OrgChart-Manager (Organisationsschema-Chef)|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager (Organisationsschema-Chef)|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager (Organisationsschema-Chef)|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

2. Välj **träna** i navigerings fältet för att träna appen.

3. När utbildningen är klar väljer du **testa** överst i panelen för att öppna test panelen.

4. Ange flera testyttranden för att verifiera att mönstret matchas och avsiktspoängen är mycket höga.

    När du har angett det första yttrandet väljer du **Inspect** (Granska) under resultatet så att du kan se alla förutsägelseresultat. Varje uttryck bör ha ett **schema för organisations chef** och ska extrahera värdena för `EmployeeListEntity` `datetimeV2` entiteterna och.

    |Yttrande|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Alla dessa yttranden hittade entiteterna inuti, och därför matchar de samma mönster, och har höga förutsägelsepoäng. Du har lagt till några mönster som ska matcha många varianter av yttranden. Du behöver inte lägga till några exempel yttranden i avsikten för att mal len uttryck ska fungera i mönstret.

Användningen av mönster har angetts:
* Högre förutsägelse resultat
* Med samma exempel yttranden i avsikten
* Med bara en liten och yttranden mall i mönstret

### <a name="use-the-or-operator-and-groups"></a>Använd operator och grupper

Flera av de tidigare mallarna yttranden är mycket nära. Använd **gruppen** `()` och **eller** `|` syntaxen för att minska mallen yttranden.

Följande två mönster kan kombineras i ett enda mönster med hjälp av gruppen `()` och eller `|` syntaxen.

|Avsikt|Exempelyttranden med valfri text och fördefinierade entiteter|
|--|--|
|OrgChart-Manager (Organisationsschema-Chef)|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager (Organisationsschema-Chef)|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

Den nya mallens uttryck kommer att vara:

`who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`.

Detta använder en **grupp** som omger de nödvändiga verben och de valfria `in` och `on` med en **eller** ett rör mellan dem.

> [!NOTE]
> När du använder _or_ -symbolen, `|` (pipe), måste du separera pipe-symbolen med ett blank steg före och efter den i exempel mal len.

1. På sidan **mönster** väljer du filtret **organisations hanterare** . Begränsa listan genom att söka efter `manager` .

1. Behåll en version av mallen uttryck (för att redigera i nästa steg) och ta bort de andra varianterna.

1. Ändra mallyttrandet till: 

    `who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`

2. Välj **träna** i navigerings fältet för att träna appen.

3. När utbildningen är klar väljer du **testa** överst i panelen för att öppna test panelen.

    Använd test fönstret för att testa versioner av uttryck:

    |Yttranden att ange i test fönstret|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

Genom att använda fler mönstermatchningssyntax minskar du antalet yttranden som du måste underhålla i din app, samtidigt som du fortfarande har en hög förutsägelse poäng.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>Använd uttryck start-och slut ankare

Pattern-syntaxen ger inledande och avslutande uttryck Anchor-syntax för ett cirkumflex `^` . De inledande och avslutande uttryck-ankarena kan användas tillsammans för att rikta in sig på mycket specifika och eventuellt exakta uttryck eller som används separat för mål avsikter.

## <a name="using-patternany-entity"></a>Med Pattern.any-entitet

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

### <a name="add-example-utterances-with-patternany"></a>Lägg till exempel yttranden med mönster. alla

1. Välj **Skapa** från det övre navigeringsfältet, och välj sedan **Avsikter** i det vänstra navigeringsfältet.

1. Välj **FindForm** från listan över avsikter.

1. Lägg till några exempel yttranden. Den text som ska förutsägas som ett mönster. det finns i **fet text**. Formulär namnet är svårt att avgöra från de andra orden runt det i uttryck. Mönstret. om du vill kan du markera gränserna för entiteten.

    |Exempel på yttrande|Formulär namn|
    |--|--|
    |Var finns formuläret **Vad gör du vid eldsvåda i laboratoriet** och vem behöver signera det när jag har läst det?|Vad du kan göra när en brand släcks i labbet
    |Var finns **Begär flytt från medarbetare som är ny i företaget** på servern?|Begär omlokalisering från medarbetare som är ny för företaget|
    |Vem skapade "**Hälsofrågor på huvudcampus**" och vilken version är mest aktuell?|Hälso-och Wellness förfrågningar om huvud Campus|
    |Jag letar efter formuläret med namnet "**Begäran om kontorsflytt inklusive fysiska tillgångar**". |Begäran om att flytta kontor inklusive fysiska till gångar|

    Utan en Pattern.any-entitet skulle det vara svårt för LUIS för att förstå var formulärrubriken slutar på grund av de många varianterna på formulärnamn.

### <a name="create-a-patternany-entity"></a>Skapa en Pattern.any-entitet
Entiteten Pattern.any extraherar entiteter med olika längd. Det fungerar bara i ett mönster eftersom det innehåller ett mönster som anger att entitetens början och slut har syntax.

1. Välj **Entiteter** i det vänstra navigeringsfältet.

1. Välj **+ skapa**, ange namnet `FormName` och välj **mönster. vilken** typ som helst. Välj **Skapa**.

### <a name="add-a-pattern-that-uses-the-patternany"></a>Lägga till ett mönster som använder Pattern.any

1. Välj **Mönster** i det vänstra navigeringsfönstret.

1. Välj avsikten **FindForm**.

1. Ange följande mallyttranden, som använder den nya entiteten:

    |Mallyttranden|
    |--|
    |`Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]`|
    |`Where is ["]{FormName}["] on the server[?]`|
    |`Who authored ["]{FormName}["] and what is the most current version[?]`|
    |`I'm looking for the form named ["]{FormName}["][.]`|

1. Träna appen.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testa det nya mönstret för extrahering av friformsdata
1. Välj **Testa** från det översta fältet för att öppna testpanelen.

1. Fyll i följande yttrande:

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. Välj **Granska** under resultatet för att se testresultaten för entitet och avsikt.

    Entiteten `FormName` hittas först och sedan mönstret, som fastställer avsikten. Om du har ett testresultat där entiteterna inte identifieras och mönstret därmed inte hittas, behöver du lägga till fler exempelyttranden för avsikten (inte mönstret).

1. Stäng testpanelen genom att välja knappen **Testa** i det övre navigeringsfältet.

### <a name="using-an-explicit-list"></a>Använda en explicit lista

Om du tycker att mönstret extraherar entiteterna felaktigt när det innehåller en Pattern.any-entitet, kan du åtgärda problemet med hjälp av en [explicit lista](reference-pattern-syntax.md#explicit-lists).

## <a name="what-did-this-tutorial-accomplish"></a>Vad har den här självstudien att göra?

Den här självstudien har lagt till mönster för att hjälpa LUIS att förutse avsikten med en betydligt högre poäng utan att behöva lägga till fler exempel yttranden. Markera entiteter och ignorerbar text tillåtna LUIS för att tillämpa mönstret på en mängd olika yttranden.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg


> [!div class="nextstepaction"]
> [Lär dig hur du använder roller med ett mönster](luis-tutorial-pattern.md)
