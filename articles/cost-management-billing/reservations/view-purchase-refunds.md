---
title: Visa köp- och återbetalningstransaktioner för Azure-reservationer
description: Lär dig hur du visar köp- och återbetalningstransaktioner för Azure-reservationer.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: b986aa2bfce203be85adbcde8e2966c167bf7ca1
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151778"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>Visa köp- och återbetalningstransaktioner för reservationer

Det finns flera olika sätt för att visa köp- och återbetalningstransaktioner för reservationer. Du kan använda API:erna för Azure-portalen, Power BI och REST.

## <a name="view-reservation-transactions-in-the-azure-portal"></a>Så här visar du transaktioner för reservationer i Azure-portalen

En faktureringsadministratör för en Enterprise-registrering eller ett Microsoft-kundavtal kan visa transaktioner för reservationer i Kostnadshantering och fakturering.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **Kostnadshantering + fakturering**.
1. Välj **Reservationstransaktioner**.
1. Du kan filtrera resultaten genom att välja **Tidsintervall**, **Typ** eller **Beskrivning**.
1. Välj **Använd**.

[![Skärmbild som visar transaktioner för reservationer i Azure-portalen](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>Visa transaktioner för reservationer i Power BI

En faktureringsadministratör för en Enterprise-registrering eller ett Microsoft-kundavtal kan visa transaktioner för reservationer med Cost Management Power BI-appen.

1. Hämta [Cost Management Power BI-appen](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
1. Gå till rapporten för RI-köp.

[![Exempel som visar transaktioner för reservationer](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

Ta reda på mer genom [Azure Cost Management Power BI-appen för Enterprise-avtal](../costs/analyze-cost-data-azure-cost-management-power-bi-template-app.md).

## <a name="use-apis-to-get-reservation-transactions"></a>Använd API:er för att hämta transaktioner för reservationer

Användare av Enterprise-avtal (EA) och Microsoft-kundavtal kan hämta transaktioner för reservationer med [Reservation Transactions - List API](/rest/api/consumption/reservationtransactions/list).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- Information om hur du hanterar en reservation finns i [Hantera Azure-reservationer](manage-reserved-vm-instance.md).
- Mer information om Azure-reservationer finns i följande artiklar:
  - [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
  - [Hantera reservationer i Azure](manage-reserved-vm-instance.md)