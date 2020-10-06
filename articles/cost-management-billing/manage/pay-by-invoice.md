---
title: Betala för Azure-prenumerationer med faktura
description: Lär dig hur du betalar Azure-prenumerationer med faktura. Se vanliga frågor och svar och visa fler resurser.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 0667e253d50ecc34acef822a928a95e49f13282e
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91439781"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Betala för din Azure-prenumeration med faktura

Om du byter till att betala med faktura innebär det att du betalar din faktura inom 30 dagar från fakturadatumet via check eller banköverföring. För att du ska kunna betala Azure-prenumerationen med faktura måste du först skicka en begäran till Azure-supporten. När din begäran har godkänts kan du byta till betalning med faktura (check/banköverföring) i [Azure-portalen](https://portal.azure.com).

> [!IMPORTANT]
> * Betalning med faktura (check/banköverföring) är endast tillgängligt för kunder som använder Azure på uppdrag av ett företag.
> * Betala alla utestående avgifter innan du byter till fakturabetalning.
> * För närvarande stöds inte fakturabetalning för Global Azure i Kina.

## <a name="request-to-pay-by-invoice"></a>Begära att betala via faktura

1. Gå till [Azure-portalen](https://portal.azure.com) för att skicka en supportbegäran. Leta upp och välj **Hjälp + support**.

    ![Leta upp Hjälp och support, Microsoft Azure-portalen](./media/pay-by-invoice/search-for-help-and-support.png)

2. Välj **Ny supportbegäran**.

    ![Ny länk för supportbegäranden, skärmen Hjälp och support, Microsoft Azure-portalen](./media/pay-by-invoice/help-and-support.png)

2. Välj **Fakturering** som **Problemtyp**. *Problemtyp* är kategorin för supportbegäran. Välj den prenumeration du vill betala för med faktura. Välj sedan ett supportavtal och därefter **Nästa**.

3. Välj **Betalning** som **Problemtyp**. *Problemtypen* är underkategorin för supportbegäran.

4. Välj **Byt till att betala med faktura** som **Undergrupp av problem**.

5. Ange följande information i rutan **Information** och välj sedan **Nästa**.

     Ny eller befintlig kund:<br>
     Betalningsmetod för befintlig kund:<br>
     Order-ID (begärs för fakturaalternativet):<br>
     Live-ID för kontoadministratörer (eller organisations-ID) (bör vara företagsdomän):<br>
     Handelskonto-ID:<br>
     Företagets namn (som det är registrerat för moms eller myndighetswebbplats):<br>
     Företagets adress (som den är registrerad för moms eller myndighetswebbplats):<br>
     Företagets webbplats:<br>
     Land:<br>
     Organisationsnummer/momsregistreringsnummer:<br>
     Företaget etablerades (år):<br>
     Tidigare affärer med Microsoft:<br>
     Kontaktnamn:<br>
     Kontaktens telefonnummer:<br>
     Kontaktens e-postadress:<br>
     Motivering om varför du föredrar faktura framför kreditkort:<br>

     Ange följande ytterligare information om du vill öka antalet kärnor:<br>

     (Gammal kvot) Befintliga kärnor:<br>
     (Ny kvot) Begärda kärnor:<br>
     Specifik region och serie för prenumeration:<br>

    - **Företagsnamn** och **Företagsadress** ska matcha den information som du angav för Azure-kontot. Om du vill visa eller uppdatera informationen går du till [Ändra information för din Azure-kontoprofil](change-azure-account-profile.md).
    - Lägg till dina kontaktuppgifter för fakturering i Azure-portalen innan kreditgränsen kan godkännas. Kontaktuppgifterna bör vara relaterade till företagets leverantörsreskontra eller ekonomiavdelning. Om du vill uppdatera kontaktuppgifterna för fakturering går du till [Azure-portalen](https://portal.azure.com).

6. Kontrollera din kontaktinformation, din önskade kontaktmetod och välj **Skapa**.

Om vi behöver utföra en kreditkontroll på grund av den mängd kredit som du behöver skickar vi en ansökan om kreditkontroll.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Byta till fakturabetalning (check/banköverföring)

När du har godkänts för betalning med faktura kan du byta till betalning med faktura (check/banköverföring) i Azure-portalen.

Om du har ett Microsoft Online Services-programkonto kan du byta till att betala Azure-prenumerationen via check/banköverföring. Med ett Microsoft-kundavtal kan du byta faktureringsprofil till check/banköverföring. [Lär dig hur du kontrollerar din kontotyp](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Byta Azure-prenumeration till check/banköverföring

Följ stegen nedan för att byta din Azure-prenumeration till fakturabetalning (check/banköverföring). *När du har bytt till fakturabetalning (check/banköverföring) kan du inte byta tillbaka till betalning med kreditkort*.

1. Gå till [Azure-portalen](https://portal.azure.com) för att logga in som kontoadministratör. Leta upp och välj **Kostnadshantering + fakturering**.

    ![Skärmbild som visar sökning efter kostnadshantering och fakturering i Azure-portalen.](./media/pay-by-invoice/search.png)

1. Välj den prenumeration som du vill börja betala med faktura.
1. Välj **Betalningsmetoder**.
1. Välj knappen **Betala per faktura** i kommandofältet.

    ![Knappen Betala per faktura, Betalningsmetoder, Microsoft Azure-portalen](./media/pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Byta faktureringsprofil till check/banköverföring

Följ stegen nedan om du vill byta faktureringsprofil till check/banköverföring. Endast den person som registrerade sig för Azure kan ändra standardbetalningsmetoden för en faktureringsprofil.

1. Gå till [Azure-portalen](https://portal.azure.com) för att visa din faktureringsinformation. Leta upp och välj **Kostnadshantering + fakturering**.
1. Välj **Faktureringsprofiler** på menyn.

    ![Menyalternativet Faktureringsprofiler, Kostnadshantering och fakturering, Microsoft Azure-portalen](./media/pay-by-invoice/billing-profile.png)

1. Välj en faktureringsprofil.
1. Välj **Betalningsmetoder** på menyn **Faktureringsprofil**.

   ![Menyalternativet Betalningsmetoder, Faktureringsprofiler, Kostnadshantering, Microsoft Azure-portalen](./media/pay-by-invoice/billing-profile-payment-methods.png)

1. Välj den blå banderollen där det står att du är berättigad till check/banköverföring.

    ![Banderoll för att byta till check/banköverföring, Betalningsmetoder, Microsoft Azure-portalen](./media/pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

*Varför har jag fått en förfrågan om ett juridiskt dokument?*

Ibland behöver Microsoft juridisk dokumentation om den information du har angett är ofullständig eller inte går att verifiera. Exempel:

* Namnet på kontonamn och företagsnamn är olika
* Ändrat namn

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

* Om det behövs uppdaterar du dina kontaktuppgifter för fakturering i [Azure-kontocenter](https://account.azure.com/Profile).
