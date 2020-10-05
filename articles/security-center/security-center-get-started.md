---
title: Uppgradera till Azure Defender – Azure Security Center
description: Den här snabb starten visar hur du uppgraderar till Security Center Azure Defender för ytterligare säkerhet.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: b583e3d86ba193a92080f2da9cc0b16d07c5a993
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91447168"
---
# <a name="quickstart-setting-up-azure-security-center"></a>Snabb start: Konfigurera Azure Security Center

Azure Security Center erbjuder enhetlig säkerhetshantering och skydd mot hot i olika hybridmolnarbetsbelastningar. De kostnads fria funktionerna ger begränsad säkerhet för dina Azure-resurser, och gör att Azure Defender utökar dessa funktioner till lokala och andra moln. Azure Defender hjälper dig att hitta och åtgärda säkerhets problem, använda åtkomst-och program kontroller för att blockera skadlig aktivitet, identifiera hot med analys och intelligens och svara snabbt vid angrepp. Du kan testa Azure Defender utan kostnad. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/).

I den här artikeln uppgraderar du till Azure Defender för ytterligare säkerhet och installerar Log Analytics-agenten på dina datorer för att övervaka säkerhets problem och hot.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Om du inte har någon prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

Om du vill aktivera Azure Defender för en prenumeration måste du ha tilldelats rollen som prenumerations ägare, prenumerations deltagare eller säkerhets administratör.


## <a name="open-security-center-for-the-first-time"></a>Öppna Security Center för första gången

1. Logga in på [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

1. Från portalens meny väljer du **Security Center**. 

    Översikts sidan för Security Center öppnas.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Översikts instrument panel för Security Center" lightbox="./media/security-center-get-started/overview.png":::

**Security Center – Översikt** ger en enhetlig överblick över säkerhetstillståndet för dina hybridarbetsbelastningar, vilket gör det lättare att identifiera och utvärdera säkerheten för arbetsbelastningarna samt identifiera och minimera risker. Security Center automatiskt utan kostnad, aktiverar alla dina Azure-prenumerationer som inte tidigare har registrerats av dig eller någon annan prenumerations användare.

Du kan visa och filtrera listan över prenumerationer genom att välja meny alternativet **prenumerationer** . Security Center kommer att justera visningen så att den återspeglar säkerhets position för de valda prenumerationerna. 

Inom några minuter efter att du har startat Security Center för första gången kanske du ser:

- **Rekommendationer** för hur du kan förbättra säkerheten för dina anslutna resurser.
- En förteckning över dina resurser som nu utvärderas av Security Center, tillsammans med säkerhets position för var och en.

Om du vill dra full nytta av Security Center måste du slutföra stegen nedan för att aktivera Azure Defender och installera Log Analytics-agenten.


## <a name="enable-azure-defender"></a>Aktivera Azure Defender

I syfte att Security Center snabb starter och självstudier måste du aktivera Azure Defender. En kostnads fri 30-dagars utvärderings version är tillgänglig. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/). 

1. Välj **komma igång**från Security Center marginal List.

    :::image type="content" source="./media/security-center-get-started/get-started-upgrade-tab.png" alt-text="Översikts instrument panel för Security Center"::: 

    På fliken **Uppgradera** visas de prenumerationer och arbets ytor som är berättigade till onboarding.

1. Från listan **Välj arbets ytor för att aktivera Azure Defender på** väljer du de arbets ytor som ska uppgraderas.
   - Om du väljer prenumerationer och arbets ytor som inte är tillgängliga för utvärderings versionen kommer nästa steg att uppgradera dem och avgifterna börjar.
   - Om du väljer en arbets yta som är berättigad till en kostnads fri utvärderings version påbörjar nästa steg en utvärderings version.
1. Välj **uppgradering** för att aktivera Azure Defender.

## <a name="enable-automatic-data-collection"></a>Aktivera automatisk data insamling
Security Center samlar in data från dina datorer för att övervaka säkerhets problem och hot. Data samlas in med hjälp av Log Analytics agent, som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från datorn och kopierar data till din arbets yta för analys. Som standard skapar Security Center en ny arbetsyta till dig.

När automatisk etablering har Aktiver ATS installerar Security Center Log Analytics agent på alla datorer som stöds och eventuella nya som skapas. Automatisk försörjning rekommenderas starkt.

Så här aktiverar du automatisk etablering av Log Analytics agent:

1. Från Security Center menyn väljer du **pris & inställningar**.
1. Välj relevant prenumeration.
1. På sidan **data insamling** ställer du in **Automatisk etablering** till **på**.
1. Välj **Spara**.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Översikts instrument panel för Security Center":::

>[!TIP]
> Om en arbets yta behöver tillhandahållas kan Agent installationen ta upp till 25 minuter.

Med agenten distribuerad till dina datorer kan Security Center tillhandahålla ytterligare rekommendationer relaterade till system uppdaterings status, OS-säkerhetskonfigurationer, Endpoint Protection, samt skapa ytterligare säkerhets aviseringar.

>[!NOTE]
> Om du ställer in automatisk etablering till **av** tas inte Log Analytics agenten bort från virtuella Azure-datorer där agenten redan har etablerats. Inaktivering av automatisk etablering begränsar säkerhetsövervakningen för dina resurser.



## <a name="next-steps"></a>Nästa steg
I den här snabb starten aktiverade du Azure Defender och etablerade Log Analytics agent för enhetlig säkerhets hantering och skydd mot hot i dina hybrid moln arbets belastningar. Om du vill lära dig mer om att använda Security Center fortsätter du till snabbstarten för publicering av Windows-datorer som är lokala och i andra moln.

> [!div class="nextstepaction"]
> [Snabb start: publicera datorer som inte är Azure-datorer](quickstart-onboard-machines.md)

Vill du optimera och Spara på dina moln utgifter?

> [!div class="nextstepaction"]
> [Börja analysera kostnaderna med Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
