---
title: Konfigurera aviseringar – Azure Portal-Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver hur du konfigurerar och får åtkomst till mått aviseringar för Azure Database for PostgreSQL-enskild server från Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: a221e3c32d458135111583885a0b9f97743d36ce
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490041"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---single-server"></a>Använd Azure Portal för att ställa in aviseringar för mått för Azure Database for PostgreSQL-enskild server

Den här artikeln visar hur du konfigurerar Azure Database for PostgreSQL aviseringar med hjälp av Azure Portal. Du kan få en avisering utifrån övervaknings måtten för dina Azure-tjänster.

Aviseringen utlöses när värdet för ett angivet mått korsar ett tröskelvärde som du tilldelar. Aviseringen utlöses både när villkoret först uppfylls och därefter när villkoret inte längre uppfylls. 

Du kan konfigurera en avisering för att utföra följande åtgärder när den utlöser:
* Skicka e-postaviseringar till tjänst administratören och medadministratörer.
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* Anropa en webhook.

Du kan konfigurera och hämta information om aviserings regler med hjälp av:
* [Azure-portalen](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [REST-API:et för Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Skapa en varnings regel för ett mått från Azure Portal
1. I [Azure Portal](https://portal.azure.com/)väljer du den Azure Database for PostgreSQLs server som du vill övervaka.

2. Under avsnittet **övervakning** på sid panelen väljer du **aviseringar** som visas:

   :::image type="content" source="./media/howto-alert-on-metric/2-alert-rules.png" alt-text="Välj aviserings regler":::

3. Välj **Lägg till mått avisering** (+ ikon).

4. Sidan **Skapa regel** öppnas som visas nedan. Fyll i nödvändig information:

   :::image type="content" source="./media/howto-alert-on-metric/4-add-rule-form.png" alt-text="Välj aviserings regler":::

5. I avsnittet **villkor** väljer du **Lägg till villkor**.

6. Välj ett mått i listan över signaler att bli aviserad om. I det här exemplet väljer du "lagrings procent".
   
   :::image type="content" source="./media/howto-alert-on-metric/6-configure-signal-logic.png" alt-text="Välj aviserings regler":::

7. Konfigurera aviserings logiken inklusive **villkoret** (t. ex. "Större än"), **tröskel** (t. ex. 85 procent), **tids agg regering** **, tids mängd som mått** regeln måste vara nöjd innan aviserings utlösare (t. ex. "Under de senaste 30 minuterna") och **frekvens**.
   
   Välj **klar** när du är klar.

   :::image type="content" source="./media/howto-alert-on-metric/7-set-threshold-time.png" alt-text="Välj aviserings regler" om du vill välja prenumerations ägare, deltagare och läsare för att ta emot meddelanden.
   
    Du kan också ange en giltig URI i fältet **webhook** om du vill att det ska anropas när aviseringen utlöses.

    Välj **OK** när du är klar.

    :::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Välj aviserings regler":::

11. Ange ett namn, en beskrivning och en allvarlighets grad för varnings regeln.

    :::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Välj aviserings regler"::: 

12. Välj **skapa aviserings regel** för att skapa aviseringen.

    Inom några minuter är aviseringen aktiv och utlösare enligt beskrivningen ovan.

## <a name="manage-your-alerts"></a>Hantera aviseringar
När du har skapat en avisering kan du välja den och utföra följande åtgärder:

* Visa ett diagram som visar mått tröskelvärdet och de faktiska värdena från föregående dag som är relevanta för den här aviseringen.
* **Redigera** eller **ta bort** varnings regeln.
* **Inaktivera** eller **Aktivera** aviseringen om du tillfälligt vill stoppa eller återuppta mottagning av meddelanden.

## <a name="next-steps"></a>Nästa steg
* Läs mer om hur du [konfigurerar Webhooks i aviseringar](../azure-monitor/platform/alerts-webhooks.md).
* Få en [Översikt över mått samlingen](../azure-monitor/platform/data-platform.md) för att se till att tjänsten är tillgänglig och svarar.