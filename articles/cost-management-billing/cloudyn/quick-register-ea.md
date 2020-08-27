---
title: Registrera ditt Azure Enterprise-avtal med Cloudyn
description: Den här snabbstarten beskriver registreringsprocessen som krävs för att skapa en utvärderingsprenumeration på Cloudyn och logga in på Cloudyn-portalen.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: af8eb5c06d427981c7154750bc069d802152f8c5
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688121"
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Registrera ett Azure Enterprise-avtal och visa kostnadsdata

Du använder ditt Azure Enterprise-avtal för att registrera dig för Cloudyn. Registreringen ger dig åtkomst till Cloudyn-portalen. Den här snabbstarten beskriver registreringsprocessen som krävs för att skapa en utvärderingsprenumeration på Cloudyn och logga in på Cloudyn-portalen. Den visar också hur du kan börja visa kostnadsdata direkt.

Azure Cost Management innehåller liknande funktioner som Cloudyn. Azure Cost Management är en lösning för intern kostnadshantering i Azure. Du får hjälp att analysera kostnader, skapa och hantera budgetar, exportera data samt granska och arbeta med optimeringsrekommendationer för att spara pengar. Mer information finns i [Azure Cost Management](../cost-management-billing-overview.md).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="register-with-cloudyn"></a>Registrera dig för Cloudyn

1. Klicka på **Kostnadshantering + fakturering** i listan med tjänster i Azure Portal.
2. Klicka på **Cloudyn** under **Översikt**  
    ![Cloudyn-sida som visas på Azure Portal](./media/quick-register-ea/cost-mgt-billing-service.png)
3. På sidan **Cloudyn** klickar du på **Go to Cloudyn** (Gå till Cloudyn) för att öppna Cloudyn-registreringssidan i ett nytt fönster.
4. På utvärderingsregistreringssidan på Cloudyn-portalen anger du namnet på ditt företag och väljer sedan **Azure Enterprise Enrollment Administrator**.  
5. Ange API-nyckeln för Enterprise Portal-registreringen. Om du inte har nyckeln tillgänglig kan du klicka på länken [Enterprise Portal](https://ea.azure.com) och göra så här:  
    ![Klistra in din API-nyckel på fliken Fakturering](./media/quick-register-ea/trial-reg.png)
   1. Logga in på webbplatsen för Azure Enterprise och klicka på **Rapporter**, klicka på **API-åtkomstnyckel** och kopiera sedan den primära nyckeln.  
    ![Exempel på en API-nyckel för EA på EA-portalen](./media/quick-register-ea/ea-key.png)
   3. Gå tillbaka till registreringssidan och klistra in API-nyckeln.
6. Godkänn användningsvillkoren och validera sedan nyckeln. Klicka på **Nästa** för att tillåta att Cloudyn samlar in Azure-resursdata. Data som samlas in omfattar information om användning, prestanda, fakturering och taggar från dina prenumerationer.  
    ![Exempel på en lyckad validering av en API-nyckel för EA](./media/quick-register-ea/ea-key-validated.png)
7. Under **Invite other stakeholders** (Bjud in andra intressenter) kan du lägga till användare genom att ange deras e-postadresser. Klicka på **Nästa** när du är klar. Beroende på storleken på Azure-registreringen kan det ta upp till 24 timmar innan all faktureringsinformation läggs till i Cloudyn.
8. Klicka på **Go to Cloudyn** (Gå till Cloudyn) för att öppna Cloudyn-portalen. Nu ska din registrerade EA-kontoinformation visas på sidan **Cloud Accounts Management** (Hantering av molnkonton).

Om du vill se en självstudievideo om registrering av Enterprise-avtalet kan du titta på [How to Find Your EA Enrollment ID and API Key for use in Cloudyn](https://youtu.be/u_phLs_udig) (Hitta ditt EA-registrerings-ID och din API-nyckel som används i Cloudyn).

[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du din Azure Enterprise-avtalsinformation för att göra en registrering med Cloudyn. Du loggade också in på Cloudyn-portalen och började visa kostnadsdata. Om du vill veta mer om Cloudyn kan du fortsätta till självstudien för Cloudyn.

> [!div class="nextstepaction"]
> [Granska användning och kostnader](tutorial-review-usage.md)
