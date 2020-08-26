---
title: Visa och ladda ned din organisations Azure-priser
description: Lär dig hur du visar och laddar ned priser eller beräknar kostnader med prissättningen för din organisation.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: b4905b1aba11a2f8c457d4785897cbe70dd31f76
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687424"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Visa och ladda ned din organisations Azure-priser

Azure-kunder med ett Azure Enterprise-avtal (EA), ett Microsoft-kundavtal (MCA) eller ett Microsoft-partneravtal (MPA) kan visa och ladda ned aktuella priser från Azure-portalen. [Lär dig hur du kontrollerar typen av faktureringskonto](#check-your-billing-account-type).

## <a name="download-pricing-for-an-enterprise-agreement"></a>Ladda ned priser för ett Enterprise-avtal

Beroende på vilka policyer som företagsadministratören har ställt in för organisationen så har endast vissa administrativa roller åtkomst till organisationens EA-prisinformation. Mer information finns i [Förstå administrativa roller för Azure Enterprise-avtal i Azure](understand-ea-roles.md).

1. Logga in på [Azure-portalen](https://portal.azure.com/) som företagsadministratör.
1. Sök efter *Kostnadshantering och fakturering*.

   ![Skärmbild som visar en sökning i Azure-portalen](./media/ea-pricing/portal-cm-billing-search.png)

1. Välj **Användning + kostnader** under faktureringskontot.

   ![Skärmbild som visar användning och kostnader under Fakturering](./media/ea-pricing/ea-pricing-usage-charges-nav.png)

1. Välj ![Skärmbild som visar en sökning i Azure-portalen](./media/ea-pricing/download-icon.png) **Ladda ned** för månaden.

1. Välj **Hämta CSV** under **Prisdokument**.

   ![Skärm bild som visar knappen Hämta CSV i prisdokumentet](./media/ea-pricing/download-ea-price-sheet.png)

## <a name="download-pricing-for-an-mca-or-mpa-account"></a>Ladda ned priser för ett MCA- eller MPA-konto

Om du har ett MCA-konto måste du vara ägare, deltagare, läsare eller fakturaansvarig för faktureringsprofilen för att kunna visa och ladda ned prisinformation. Om du har ett MPA-konto måste du ha rollen global administratör eller administratörsrepresentant för partnerorganisationen för att kunna visa och ladda ned prisinformation.

### <a name="download-price-sheets-for-billed-charges"></a>Ladda ned prisdokument för fakturerade avgifter

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter *Kostnadshantering och fakturering*.
1. Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto först.
1. Välj **Fakturor**.
1. Leta rätt på fakturaraden i rutnätet som motsvarar det prisdokument du vill ladda ned.
1. Klicka på ellipsen (`...`) i slutet av raden.
![Skärmbild där ellipsen är vald](./media/ea-pricing/billingprofile-invoicegrid-new.png)

1. Om du vill se priser för tjänsterna i den valda fakturan väljer du **Fakturaprisdokument**.
1. Om du vill se priser för alla Azure-tjänster för den aktuella faktureringsperioden väljer du **Azure-prisdokument**.

![Skärmbild som visar snabbmenyn med prisdokument](./media/ea-pricing/contextmenu-pricesheet01.png)

### <a name="download-price-sheets-for-the-current-billing-period"></a>Ladda ned prisdokument för den aktuella faktureringsperioden

Om du har ett MCA-konto kan du ladda ned priser för den aktuella faktureringsperioden.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter *Kostnadshantering och fakturering*.
1. Välj en faktureringsprofil. Beroende på din åtkomst kan du behöva välja ett faktureringskonto först.
1. I området **Översikt** finns nedladdningslänkarna nedanför avgifterna hittills under månaden.
1. Välj **Azure-prisdokument**.
![Skärmbild som visar nedladdning från Översikt](./media/ea-pricing/open-pricing01.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Beräkna kostnader med priskalkylatorn för Azure

Du kan också använda organisationens priser till att beräkna kostnaderna i priskalkylatorn för Azure.

1. Öppna [priskalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator).
1. Välj **Logga in** uppe till höger.
1. Under **Program och erbjudande** > **Licensprogram** väljer du **Enterprise-avtal (EA)** .
1. Under **Program och erbjudande** > **Valt avtal** väljer du **Inga valda**.

    ![Skärm bild som visar knappen Hämta CSV i prisdokumentet](./media/ea-pricing/ea-pricing-calculator-estimate.png)

1. Välj organisationen.
1. Välj **Använd**.
1. Sök efter och lägg sedan till produkter i beräkningen.
1. De uppskattade priserna som visas baseras på priserna för den organisation du har valt.

## <a name="check-your-billing-account-type"></a>Kontrollera typen av faktureringskonto
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Nästa steg

Om du är EA-kund kan du läsa:

- [Hantera åtkomst till din EA-faktureringsinformation för Azure](manage-billing-access.md)
- [Visa och ladda ned din faktura för Microsoft Azure](../understand/download-azure-invoice.md)
- [Visa och ladda ned information om din användning och dina avgifter för Microsoft Azure](../understand/download-azure-daily-usage.md)
- [Förstå din faktura för EA-kunder](../understand/review-enterprise-agreement-bill.md)

Om du har ett Microsoft-kundavtal kan du läsa:

- [Förstå termerna i prisdokumentet för ett Microsoft-kundavtal](mca-understand-pricesheet.md)
- [Visa och ladda ned din faktura för Microsoft Azure](../understand/download-azure-invoice.md)
- [Visa och ladda ned information om din användning och dina avgifter för Microsoft Azure](../understand/download-azure-daily-usage.md)
- [Visa och ladda ned skattedokument för din faktureringsprofil](../understand/mca-download-tax-document.md)
- [Förstå avgifterna på faktureringsprofilens faktura](../understand/review-customer-agreement-bill.md)
