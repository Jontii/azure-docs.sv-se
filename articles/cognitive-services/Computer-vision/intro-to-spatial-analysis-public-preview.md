---
title: Introduktion till Visuellt innehåll rums analys
titleSuffix: Azure Cognitive Services
description: Det här dokumentet beskriver de grundläggande begreppen och funktionerna i en Visuellt innehåll rums analys behållare.
services: cognitive-services
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f90e4e5e187977f0ee77a565ff9143902ea3a10d
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736843"
---
# <a name="introduction-to-computer-vision-spatial-analysis"></a>Introduktion till Visuellt innehåll rums analys

Visuellt innehåll rums analys är en ny funktion i Azure Cognitive Services Visuellt innehåll som hjälper organisationer att maximera värdet på deras fysiska utrymmen genom att förstå personernas rörelser och närvaro inom ett angivet ställe. Det gör att du kan mata in video från CCTV eller övervaknings kameror, köra AI-åtgärder för att extrahera insikter från video strömmarna och generera händelser som ska användas av andra system. Med indata från en kamera ström kan en AI-åtgärd göra saker som att räkna antalet personer som anger ett utrymme eller mäter efterlevnad med rikt linjer för ansikts mask och sociala distancing.

## <a name="the-basics-of-spatial-analysis"></a>Grunderna för rums analys

Idag är kärn åtgärderna för rums analys allt byggt på en pipeline som inleder video, identifierar personer i videon, spårar människor när de rör sig över tid och genererar händelser när människor interagerar med intresse områden.

## <a name="spatial-analysis-terms"></a>Villkor för rums analys

| Period | Definition |
|------|------------|
| Person identifiering | Den här komponenten besvarar frågan "var är personerna i den här bilden"? Den hittar människor i en bild och skickar en avgränsnings ruta som visar var personen är ansvarig för personer som spårar komponenten. |
| Personer som spårar | Den här komponenten ansluter person identifieringar över tid då de flyttar runt en kamera. Den använder temporal logik om hur användare vanligt vis flyttar och grundläggande information om det övergripande utseendet på dem. Det spårar inte personer över flera kameror. Om en person finns i fältet från en kamera i mer än cirka en minut och sedan anger om kameravy, kommer systemet att se detta som en ny person. Personer som spårar kan inte unikt identifiera individer i kameror. Den använder inte ansikts igenkänning eller gång spårning. |
| Identifiering av ansikts mask | Den här komponenten identifierar platsen för en persons ansikte i kamerans visnings område och identifierar förekomsten av en ansikts mask. För att göra det genomsöker AI-åtgärden bilder från video. När en ansikte identifieras ger tjänsten en avgränsnings ruta runt om. Genom att använda funktioner för objekt identifiering identifieras förekomsten av ansikts masker i markerings rutan. Identifiering av ansikts mask innebär inte att skilja en ansikte från en annan ansikte, förutsäga eller klassificera ansikts attribut eller att utföra ansikts igenkänning. |
| Intresse region | Detta är en zon eller linje som definierats i Indataporten som en del av konfigurationen. När en person interagerar med regionen i videon genererar systemet en händelse. Till exempel definieras en rad i videon för PersonCrossingLine-åtgärden. När en person korsar raden skapas en händelse. |
| Händelse | En händelse är primär utdata från rums analys. Varje åtgärd genererar en speciell händelse antingen med jämna mellanrum (t. ex. en gång per minut) eller när en viss utlösare sker. Händelsen innehåller information om vad som hände i indata-videon men som inte innehåller några bilder eller video. PeopleCount-åtgärden kan till exempel generera en händelse som innehåller det uppdaterade antalet varje gång antalet personer ändras (utlösare) eller en gång i minuten (med jämna mellanrum). |

## <a name="example-use-cases-for-spatial-analysis"></a>Exempel på användnings fall för rums analys

Följande är exempel på användnings områden som vi hade i åtanke när vi konstruerade och testade rums analyser.

**Social Distancing-kompatibilitet** – ett kontors utrymme har flera kameror som använder rums analys för att övervaka social Distancing-kompatibilitet genom att mäta avståndet mellan personer. Anläggningar Manager kan använda termiska kartor som visar sammanställd statistik över sociala distancing-kompatibilitet över tid för att justera arbets ytan och göra sociala distancing enklare.

**Konsument-analys** – en moln lagrings plats använder kameror som är pekade på produkt visar för att mäta påverkan av försäljnings ändringar på lagrings trafik. Systemet gör det möjligt för Store Manager att identifiera vilka nya produkter som är mest ändrade för engagemang.

**Hantering av köer** – kameror som pekas i utcheckningsstatus ger aviseringar till chefer när vänte tiden blir för lång, så att de kan öppna fler rader. Historiska data om att överge kön ger insikter om konsumenternas beteende.

**Kompatibilitet för ansikts mask** – butiker kan använda kameror som pekar på Store-fronten för att kontrol lera om kunder som går in i butiken använder sig av ansikts masker för att upprätthålla säkerhetskompatibilitet och analysera sammanställd statistik för att få insikter om mask användnings trender. 

**Skapa användnings & analys** – en kontors byggnad använder kameror som fokuserar på ingångar till nyckel utrymmen för att mäta Footfall och hur användarna använder arbets platsen. Med insikter kan bygg chefen justera tjänster och layout för att bättre betjäna personer.

**Lägsta personal identifiering** – i ett Data Center övervakar kameror aktiviteter kring servrar. När de anställda fysiskt reparerar känslig utrustning behöver två personer alltid vara närvarande under reparationen av säkerhets skäl. Kameror används för att kontrol lera att den här rikt linjen följs.

**Optimering av arbets platsen** – i en snabb och lätt restaurang används kameror i köket för att generera sammanställd information om arbets flödet för medarbetare. Detta används av chefer för att förbättra processer och utbildning för teamet.

## <a name="considerations-when-choosing-a-use-case"></a>Att tänka på när du väljer ett användnings fall

**Undvik kritiska säkerhets varningar** – avstånds analys har inte utformats för kritiska säkerhets varningar i real tid. Det bör inte förlita sig på för scenarier när real tids aviseringar behövs för att utlösa åtgärder för att förhindra skada, t. ex. genom att stänga av en del tung maskiner när en person är närvarande. Den kan användas för att minska risken för risker med hjälp av statistik och åtgärder för att minska riskfyllda beteenden, t. ex. personer som anger ett begränsat/förbjudet utrymme.

**Undvik användning av arbetsrelaterade beslut** – avstånds analys ger Probabilistic mått gällande plats och förflyttning av personer inom ett utrymme. Även om dessa data kan vara användbara för en samlad process förbättring, är data inte en bra indikator för enskilda arbets prestationer och bör inte användas för att fatta arbetsrelaterade beslut.

**Undvik att använda hälso vårds beslut** – rums analys ger Probabilistic och delar data som rör personernas rörelser. Data är inte lämpliga för att fatta hälso-relaterade beslut.

**Undvik att använda skyddade utrymmen** – skydda individers integritet genom att utvärdera kamera platser och positioner, justera vinklar och region för intressen så att de inte förbise skyddade områden som toaletter.

**Överväg omsorgsfullt användning i skolor eller äldre skötsel anläggningar** – rums analys har inte kraftigt testats med data som innehåller minderåriga under 18 eller vuxna över ålder 65. Vi rekommenderar att kunderna noggrant utvärderar fel frekvenser för deras situation i miljöer där dessa åldrar förväger.

**Överväg noggrant att använda i offentliga utrymmen** – utvärdera kamera platser och platser, justera vinklar och region för att minimera samling från offentliga utrymmen. Belysning och väder i offentliga utrymmen, t. ex. gator och parker, påverkar avsevärt prestandan hos det omgivande analys systemet, och det är mycket svårt att tillhandahålla ett effektivt avslöjande i offentliga utrymmen.

## <a name="spatial-analysis-gating-for-public-preview"></a>Hantera för spatial analys för offentlig för hands version

För att säkerställa rums analys används för scenarier som den har utformats för, vi gör den här tekniken tillgänglig för kunder genom en program process. För att få åtkomst till avstånds analys måste du börja med att fylla i vårt insugnings formulär online. [Börja ditt program här](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

Till gång till den offentliga för hands versionen av spatial analys omfattas av Microsofts enda val som baseras på våra berättiganderegler, först konsumentsajter process och tillgänglighet för att stödja ett begränsat antal kunder under den här för hands versionen. I offentlig för hands version letar vi efter kunder som har en betydande relation med Microsoft, är intresserade av att arbeta med oss i de rekommenderade användnings fallen och ytterligare scenarier som är i drift med våra ansvariga AI-åtaganden.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Egenskaper och begränsningar för rums analys](/legal/cognitive-services/computer-vision/accuracy-and-limitations?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)