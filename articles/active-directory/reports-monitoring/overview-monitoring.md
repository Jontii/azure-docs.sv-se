---
title: Vad är Azure Active Directory-övervakning? | Microsoft Docs
description: En allmän översikt över Azure Active Directory-övervakning.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: efa4b625afb641209d3920c8663ed810ee27e1ad
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228655"
---
# <a name="what-is-azure-active-directory-monitoring"></a>Vad är Azure Active Directory-övervakning?

Med Azure Active Directory-övervakning (Azure AD) kan du nu dirigera dina Azure AD-aktivitetsloggar till olika slutpunkter. Du kan antingen behålla dem för långvarig användning eller integrera dem med SIEM-verktyg (säkerhetsinformation och händelsehantering) för att få insikter om din miljö.

För närvarande kan du dirigera loggarna till:

- Ett Azure-lagringskonto.
- En Azure-händelsehubb, så du kan integrera med dina Splunk- och Sumologic-instanser.
- Azure Log Analytics-arbetsyta, där du kan analysera data, skapa en instrumentpanel och avisera vid specifika händelser

**Nödvändig roll**: global administratör

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="diagnostic-settings-configuration"></a>Konfiguration av diagnostikinställningar

Du konfigurerar övervakningsinställningarna för Azure AD-aktivitetsloggarna genom att först logga in på [Azure-portalen](https://portal.azure.com) och sedan välja **Azure Active Directory**. Härifrån kan du komma åt konfigurationssidan Diagnostikinställningar på två sätt:

* Välj **Diagnostikinställningar** från avsnittet **Övervakning**.

    ![Diagnostikinställningar](./media/overview-monitoring/diagnostic-settings.png)
    
* Välj **Granskningsloggar** eller **Inloggningar**och välj sedan **Exportinställningar**. 

    ![Exportinställningar](./media/overview-monitoring/export-settings.png)


## <a name="route-logs-to-storage-account"></a>Dirigera loggar till lagringskonto

Genom att dirigera loggar till ett Azure Storage-konto kan du behålla dem längre än standardperioden för kvarhållning som beskrivs i våra [principer för kvarhållning](reference-reports-data-retention.md). Lär dig hur du [dirigerar data till ditt lagringskonto](quickstart-azure-monitor-route-logs-to-storage-account.md).

## <a name="stream-logs-to-event-hub"></a>Strömma loggar till händelsehubb

När du dirigerar loggar till en Azure-händelsehubb kan du integrera med SIEM-tredjepartsverktyg som Sumologic och Splunk. Den här integrationen gör att du kan kombinera Azure AD-aktivitetsloggdata med andra data som hanteras av ditt SIEM, för att ge bättre inblick i din miljö. Lär dig hur du [skickar händelser till en händelsehubb](tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="send-logs-to-azure-monitor-logs"></a>Skicka loggar till Azure Monitor-loggar

[Azure Monitor-loggar](../../azure-monitor/log-query/log-query-overview.md) är en lösning som konsoliderar övervakningsdata från olika källor och tillhandahåller ett frågespråk och en analysmotor som ger dig insikter om hur dina program och resurser fungerar. Genom att skicka Azure AD-aktivitetsloggar till Azure Monitor-loggar kan du snabbt hämta, övervaka och agera på insamlade data. Lär dig att [skicka data till Azure Monitor-loggar](howto-integrate-activity-logs-with-log-analytics.md).

Du kan också installera fördefinierade vyer för Azure AD-aktivitetsloggar för vanliga scenarier som omfattar inloggningar och granskningshändelser. Lär dig att [installera och använda logganalysvyer för Azure AD-aktivitetsloggar](howto-install-use-log-analytics-views.md).

## <a name="next-steps"></a>Nästa steg

* [Aktivitetsloggar i Azure Monitor](concept-activity-logs-azure-monitor.md)
* [Strömma loggar till händelsehubb](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Skicka loggar till Azure Monitor-loggar](howto-integrate-activity-logs-with-log-analytics.md)