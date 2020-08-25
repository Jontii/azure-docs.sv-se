---
title: Check lista för att skapa SaaS i Microsofts kommersiella marknads platser
description: Den information som du kan ange i SaaS för att skapa erbjudanden i Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: c72d4d4f77ecf0bcad2b521650fd8ff7612fb604
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815359"
---
# <a name="saas-offer-creation-checklist-in-partner-center"></a>Check lista för att skapa SaaS i Partner Center

Med processen för att skapa SaaS går du igenom flera sidor.  Den här artikeln beskriver de uppgifter som du kan tillhandahålla på varje sida, med länkar till mer information om varje objekt.

> [!NOTE]
> Om du skapar ett transactable SaaS-erbjudande måste du implementera integrering med [API: er för SaaS-utförande](./pc-saas-fulfillment-apis.md).  Integrering med API: erna är det enda sättet för att det ska gå att använda tjänsten i marknads platsen för att fungera korrekt. Du måste också kontrol lera att din app använder Azure AD-autentisering med enkel inloggning (SSO). Se [Azure AD och transactable SaaS-erbjudanden på den kommersiella marknads platsen](../azure-ad-saas.md).

Objekt som du måste ange eller ange anges nedan.  Vissa områden är valfria eller har standardvärden som du kan ändra efter behov.  Du behöver inte arbeta med de här avsnitten i den ordning som visas här.

| Objekt | Syfte |
| ---------- | -------------------|
| [**Nytt erbjudande modal**](#new-offer-modal) | Samlar in identitets information för erbjudandet.  |
| [Installations sida för erbjudande](#offer-setup-page) | Gör att du kan välja att använda viktiga funktioner och välja hur du säljer ditt erbjudande via Microsoft.  |
| [Sidan Egenskaper](#properties-page) | Definiera de kategorier och branscher som används för att gruppera ditt erbjudande på marknads platser, juridiska avtal som stöder ditt erbjudande och din program version. |
| [Sida för erbjudande lista](#offer-listing-page) | Definiera information om erbjudandet som ska visas på Marketplace, inklusive beskrivningar av ditt erbjudande och marknadsförings till gångar.|
| [Sidan förhandsgranska](#preview-page) | Definiera en begränsad förhands gransknings grupp så att du kan släppa ditt erbjudande innan du publicerar erbjudandet Live till de bredare Marketplace-användarna.|
| [Sidan teknisk konfiguration](#technical-configuration-page)  |  Endast tillgängligt om du väljer att sälja erbjudandet via Microsoft.  Definiera teknisk information (landnings sidans URL, anslutningens webhook-URL, Azure AD-klient-ID och Azure AD App-ID) som används av Marketplace för att ansluta till ditt erbjudande.  Dessa parametrar krävs för att integreras korrekt med SaaS-uppfyllelse och Marketplace-avgiftsbelagda fakturerings-API: er.|
| [**Ny plan modal**](#plan-identity-modal) | Samlar in information om plan identitet.  |
| [Plan List sida](#plan-listing-page)  | Endast tillgängligt om du väljer att sälja erbjudandet via Microsoft. Definiera informationen som används för att Visa planen i Marketplace.  |
| [Sidan planera priser och tillgänglighet](#plan-pricing-and-availability-page)  | Endast tillgängligt om du väljer att sälja erbjudandet via Microsoft.  Samlar in affärs egenskaper (pris modell), mål grupp och marknads tillgänglighet för varje plan (version) av erbjudandet.  |
| [Sidan Testkör enhets listan](#test-drive-listing-page)  | Endast tillgängligt om du väljer att erbjuda en testen het för ditt erbjudande. Definiera informationen som används för att visa en lista på test enheten i Marketplace.  |
| Sidan teknisk konfiguration för test enhet  | Endast tillgängligt om du väljer att erbjuda en testen het för ditt erbjudande. Definiera de tekniska detaljerna för demonstrationen (eller "testenheten") som gör det möjligt för kunder att prova ditt erbjudande innan de genomför köpet.  |
| [Sidan granska och publicera](#review-and-publish-page)  | Välj de ändringar som du vill publicera, se status för varje sida och lämna kommentarer till certifierings teamet.  |
|

## <a name="new-offer-modal"></a>Nytt erbjudande modal

De första uppgifterna du kommer att uppmanas att ange är ett ID och alias för ditt erbjudande.

| Fältnamn | Anteckningar |  
| ---------------- | -----------|
| Erbjudande-ID  | Krävs, kan inte ändras efter att det har skapats. Högst 50 tecken och får bara bestå av gemener, alfanumeriska tecken, bindestreck eller under streck. |
| Erbjud alias  | Obligatorisk |
|

## <a name="offer-setup-page"></a>Installations sida för erbjudande

På sidan erbjudande konfiguration kan du välja olika kanaler och sälja rörelser, samt förklara användningen av viktiga funktioner, till exempel test enhet och kund leads.

| Fältnamn | Anteckningar |
| ---------------- | -----------|  
| Vill du sälja via Microsoft?  | Krävs. Standard: Ja. |
| Hur vill du att potentiella kunder ska kunna interagera med erbjudande listan? (Anrop till åtgärd)  | Krävs om de inte säljs via Microsoft. Standard: kostnads fri utvärderings version, alternativ: "Hämta nu", "kostnads fri utvärdering", "kontakta mig". |
| Utvärderings-URL  | Krävs om "kostnads fri utvärdering" är valt, på det sätt som kunderna ska interagera med erbjudande listan. |
| Erbjudande-URL  | Krävs om "Hämta nu" är valt, på det sätt som kunderna ska interagera med erbjudande listan. |
| Kanaler  | Valfritt. Standard: inte att du valde CSP (åter försäljare)-kanalen.  |
| Test Drive | Valfritt. Standard: ingen testen het har Aktiver ATS.  |
| Typ av test enhet | Krävs om den är aktive rad en testen het. Standard: inget har valts. Alternativ: Azure Resource Manager, Dynamics 365 for Business Central, Dynamics 365 för kund engagemang, Dynamics 365 for Operations, Logic app, Power BI.  |
| Kund ledare – Anslut till ett CRM-system | Krävs om du säljer via Microsoft eller om du har en lista över erbjudanden som "kontakta mig". Standard: inget CRM-system är anslutet. CRM-alternativ: Azure Table, Azure-Blob, Dynamics CRM Online, HTTPs-slutpunkt, Marketo, Salesforce.  |
|

## <a name="properties-page"></a>Sidan Egenskaper

På sidan egenskaper definierar du de kategorier och branscher som används för att gruppera ditt erbjudande på Marketplace, de juridiska avtalen som stöder ditt erbjudande och din program version. Se till att tillhandahålla fullständig och korrekt information om ditt erbjudande på den här sidan, så att det visas på lämpligt sätt och erbjuds till rätt kund uppsättning.

| Fältnamn | Anteckningar |
| ---------------- | -----------|  
| Kategori och under kategori | Krävs 1 och max 3. Standard: inget har valts. |
| Branscher och under branscher | Valfritt. högst 2 L1-branscher och högst 2 under branscher inom varje L1-bransch, standard: ingen vald |
| Appversion  | Valfritt. Standard: ingen. |
| Använd standard kontrakt  | Valfritt. Standard: inte valt.  |
| Villkor för användning  | Krävs om standard kontrakt inte har valts.  |
|

## <a name="offer-listing-page"></a>Sida för erbjudande lista

På registrerings sidan är det där du anger den text och de bilder som kunderna ser när du visar ditt erbjudandes lista på Marketplace.

| Fältnamn | Anteckningar |
| ---------------- | -----------|
| Name  | Obligatoriskt, max 50 tecken. |
| Sammanfattning  | Obligatoriskt, max 100 tecken. |
| Beskrivning  | Obligatoriskt, max 3000 tecken. |
| Komma igång-instruktioner  | Obligatoriskt, max 3000 tecken. |
| Komma igång-instruktioner  | Obligatoriskt, max 3000 tecken. |
| Sök efter nyckelord  | Valfria, rekommenderade, max 3 nyckelord. |
| URL för sekretess policy  | Obligatorisk |
| URL för marknadsförings material för CSP-program  | Valfritt |
| Rubrik för användbara länkar + URL  | Valfritt |
| Rubrik för stöd dokument + fil  | Krävs, min 1 och max 3. Måste vara PDF-filformat. |
| Skärmbilder  | Obligatorisk, minimal 1 skärm bild och max 5; fyra eller fler rekommenderas. Måste vara 1280 X 720 i PNG-format. |
| Butiks logo typer (liten, medel, stor)  | Den stora logo typen (från 216 x 216 till 350 x 350 BPT) krävs. Partner Center använder detta för att skapa en liten (48 x 48 BPT) och medelhög (90 x 90 BPT)-logo typ. Du kan också ersätta dem med olika avbildningar senare. Logo typer måste vara i PNG-format. |
| Video namn + URL + miniatyr  | Valfria, rekommenderade, max 4 videor. Miniatyren måste vara 1280 x 720 i PNG-format. Video måste ligga i YouTube eller Vimeo. |
| Kontakter (CSP-program, teknik, support)  | Teknisk kontakt krävs (namn, e-postadress och telefonnummer). CSP-programmet kontaktar valfritt men rekommenderas. |
| Support-URL  | Obligatorisk |
|

## <a name="preview-page"></a>Sidan förhandsgranska

På sidan förhands granskning kan du ange vilken mål grupp som ska ha åtkomst till din för hands version av erbjudandet, för att kontrol lera att erbjudandet uppfyller alla krav innan det går live.

| Fältnamn | Anteckningar |
| ---------------- | -----------|
| AAD/MSA e-post + Beskrivning | Krävs, min 1 och max 10 om de anges manuellt, eller upp till 20 vid överföring av en CSV-fil. |
|

## <a name="technical-configuration-page"></a>Sidan teknisk konfiguration

På sidan teknisk konfiguration anger du de tekniska uppgifter som används av Microsoft för att ansluta till ditt erbjudande. Den här sidan visas inte om du valde att inte sälja via Microsoft.

> [!NOTE]
> För att kunna använda transacte erbjudanden måste du skapa en landnings sida och din app måste använda Azure AD-autentisering med enkel inloggning (SSO). Mer information finns [i Azure AD och transactable SaaS-erbjudanden på den kommersiella marknaden](../azure-ad-saas.md).

| Fältnamn | Anteckningar |  
| ---------------- | -----------| 
| URL för landnings sida | Krävs om du säljer via Microsoft. |
| Anslutning-webhook | Krävs om du säljer via Microsoft. |
| Azure AD-klient-ID | Krävs om du säljer via Microsoft. |
| ID för Azure AD-App | Krävs om du säljer via Microsoft. |
|

## <a name="plan-identity-modal"></a>Planera identitet, modal

De första delarna av informationen som du uppmanas att ange är ett namn och ett ID för din plan. Den här sidan är inte synlig för dig om du har valt att inte sälja via Microsoft.

| Fältnamn | Anteckningar |  
| ---------------- | -----------|
| Plan-ID  | Krävs om du säljer via Microsoft. Den kan inte ändras efter att den har skapats. Högst 50 tecken och får bara bestå av gemener, alfanumeriska tecken, bindestreck eller under streck. |
| Plannamn  | Krävs om du säljer via Microsoft. Måste vara unikt för alla planer i erbjudandet. Högst 50 tecken. |
|

## <a name="plan-listing-page"></a>Plan List sida

På sidan med prenumerations listan kan du se hur kunderna ser när de visar planen i Marketplace. Den här sidan visas inte om du valde att inte sälja via Microsoft.

| Fältnamn | Anteckningar |  
| ---------------- | -----------|
| Beskrivning av plan   | Krävs om du säljer via Microsoft. Högst 500 tecken. |
|

## <a name="plan-pricing-and-availability-page"></a>Sidan planera priser och tillgänglighet

På sidan planera priser och tillgänglighet kan du definiera affärs egenskaper, mål grupp och marknads tillgänglighet för varje plan (version) av erbjudandet. Den här sidan visas inte om du valde att inte sälja via Microsoft.

| Fältnamn | Anteckningar |
| ---------------- | -----------|
| Marknads tillgänglighet  | Krävs, min 1 och Max 141. |
| Pris modell  | Krävs. Standard: fast pris. Alternativ: fast pris, per användare. |
| Lägsta och högsta antal platser  | Valfritt, endast tillgängligt om den platsbaserade pris modellen har valts. |
| Fakturerings period  | Krävs. Standard: varje månad. Alternativ: månatlig, årlig. |
| Pris  | Krävs USD per månad om fakturerings perioden per månad har valts. eller USD per år om årlig fakturerings period har valts. |
| Planera mål grupp  | Valfritt. Standard: offentlig plan. Alternativ: offentligt, privat efter klient-ID |
| Begränsad Plans mål grupp (klient-ID + beskrivning)  | Krävs om privat plan har valts. Minst 1 och max 10 klient-ID: n om de anges manuellt. Max 20000 om import av CSV-fil. |
|

## <a name="test-drive-listing-page"></a>Sidan Testkör enhets listan

Endast tillgängligt om du väljer att erbjuda en testen het för ditt erbjudande. Definiera informationen som används för att visa en lista på test enheten i Marketplace.

| Fältnamn | Anteckningar |
| ---------------- | -----------|
| Beskrivning  | Krävs |
| Användarens manuella namn + fil  | Obligatoriskt, max 1 dokument måste vara PDF-format. |
| Video namn, URL + miniatyr  | Valfritt, rekommenderas. Miniatyr bilden måste vara 533 x 324 i JPGP-eller PNG-format. Video måste ligga i YouTube eller Vimeo. |
|

## <a name="review-and-publish-page"></a>Sidan granska och publicera

| Fältnamn | Anteckningar |
| ---------------- | -----------|
| Kommentarer för certifiering  | Valfritt |
|

## <a name="next-steps"></a>Nästa steg

- [Skapa ett nytt SaaS-erbjudande](./create-new-saas-offer.md)
