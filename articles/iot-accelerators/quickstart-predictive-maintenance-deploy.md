---
title: Prova en molnbaserad IoT-lösning för förutsägande underhåll – Azure | Microsoft Docs
description: I den här snabbstarten ska du distribuera Azure IoT-lösningsacceleratorn för förutsägande underhåll och logga in till och använda instrumentpanelen för lösningen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 7266f110069155e2a9f7804d53c6e1088768ec8d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91541857"
---
# <a name="quickstart-try-a-cloud-based-solution-to-run-a-predictive-maintenance-analysis-on-my-connected-devices"></a>Snabbstart: Prova en molnbaserad lösning för att analysera behovet av förutsägande underhåll på anslutna enheter

I den här snabbstarten får du lära dig att distribuera Azure IoT-lösningsacceleratorn för förutsägande underhåll och simulera ett molnbaserat förutsägande underhåll. När du har distribuerat lösningsacceleratorn kan du analysera behovet av förebyggande underhåll utifrån data om en simulerad flygplansmotor på sidan **Instrumentpanel** i lösningen. Du kan använda den här lösningsacceleratorn som utgångspunkt för en egen implementering eller som utbildningsverktyg.

I den här simuleringen är Fabrikam ett regionalt flygbolag som fokuserar på bra kundupplevelser till konkurrenskraftiga priser. En orsak till flygförseningar är underhållsproblem, och flygplansmotorernas underhåll är särskilt krävande. Fabrikam måste till vilket pris som helst förhindra motorfel under flygningar och inspekterar därför regelbundet sina motorer och schemalägger underhåll med utgångspunkt i en plan. Flygplansmotorer slits dock inte alltid likadant. En del onödigt underhåll utförs på motorer. Dessutom kan det uppstå problem som gör att planet blir stående tills underhåll har utförts. Dessa problem bli extra kostsamma om ett flygplan finns på en plats där rätt tekniker eller reservdelar inte är tillgängliga.

Motorerna i Fabrikams flygplan är utrustade med sensorer som övervakar motortillståndet under flygning. Fabrikam har flera års drifts- och feldata för motorer från dessa sensorer. Fabrikams datavetare har använt dessa data för att utveckla en modell för att förutsäga återstående driftstid (RUL) för en flygplansmotor. Modellen använder en korrelation mellan data från fyra av motorsensorerna och motorförslitningar som med tiden leder till motorfel. Fabrikam fortsätter att utföra regelbundna säkerhetsinspektioner men nu kan man använda modeller för att beräkna varje motors återstående livslängd efter varje flygning. Fabrikam kan nu förutsäga framtida felpunkter och planera för underhåll för att minimera flygplanens marktid. Processen minskar driftkostnaderna och säkerställer passagerarnas och personalens säkerhet.

Du behöver en aktiv Azure-prenumeration för att kunna utföra den här snabbstarten.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="deploy-the-solution"></a>Distribuera lösningen

När du distribuerar lösningsacceleratorn till Azure-prenumerationen måste du ange några konfigurationsalternativ.

Gå till [Microsoft Azure IoT-lösningsacceleratorer](https://www.azureiotsolutions.com) och logga in med autentiseringsuppgifterna för ditt Azure-konto.

Klicka på panelen **Förebyggande underhåll**. På sidan **Förebyggande underhåll** klickar du på **Try Now** (Testa nu):

![Testa nu](./media/quickstart-predictive-maintenance-deploy/predictivemaintenance.png)

På sidan **Create Predictive Maintenance solution** (Skapa lösning för förutsägande underhåll) anger du ett unikt **lösningsnamn** för utvecklingsacceleratorn för förutsägande underhåll. I den här snabbstarten använder vi **MyPredictiveMaintenance**.

Välj den **prenumeration** och **region** du vill använda för att distribuera lösningsacceleratorn. Normalt väljer du regionen närmast dig. I den här snabbstarten använder vi **Visual Studio Enterprise** och **USA, östra**. Du måste vara [global administratör eller användare](iot-accelerators-permissions.md) i prenumerationen.

Klicka på **Skapa** för att inleda distributionen. Processen tar minst fem minuter att köra:

![Information om lösningen för förutsägande underhåll](./media/quickstart-predictive-maintenance-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Logga in till lösningen

När distributionen till Azure-prenumerationen är klar visas en grön bockmarkering och **Redo** på lösningspanelen. Nu kan du logga in på instrumentpanelen för lösningsacceleratorn för förutsägande underhåll.

Klicka på den nya lösningsacceleratorn för förutsägande underhåll på sidan **Etablerade lösningar**.

![Skärm bild som visar sidan "etablerade lösningar" med lösnings Accelerator för förebyggande underhåll markerat.](./media/quickstart-predictive-maintenance-deploy/solution.png)

 Du kan visa information om lösningsacceleratorn på sidan som visas. Välj **Go to your Solution accelerator** (Gå till din lösningsaccelerator) för att visa lösningsacceleratorn för förutsägande underhåll:

![Lösningens panel](./media/quickstart-predictive-maintenance-deploy/solutionpaneldetails.png)

Acceptera behörighetsförfrågan genom att klicka på **Acceptera**. Nu visas instrumentpanelen för lösningen för förutsägande underhåll i webbläsaren:

![Instrumentpanel för lösningen](./media/quickstart-predictive-maintenance-deploy/solutiondashboard.png)

Starta simuleringen genom att klicka på **Starta simulering**. Instrumentpanelen fylls med sensorhistorik, RUL-värden, cykler och RUL-historik:

![Simuleringen körs](./media/quickstart-predictive-maintenance-deploy/simulationrunning.png)

Om RUL-värdet är mindre än 160 (ett godtyckligt tröskelvärde som valts som exempel) visas en varningssymbol på lösningsportalen bredvid RUL-värdet. Dessutom markeras flygplansmotorn i gult på lösningsportalen. Lägg märke till att RUL-värdena har en nedåtgående trend generellt, men med många upp- och nedgångar. Detta mönster beror på de olika cykellängderna och modellens precision.

![Simuleringsvarning](./media/quickstart-predictive-maintenance-deploy/simulationwarning.png)

Den fullständiga simuleringen tar 35 minuter för att slutföra 148 cykler. RUL-tröskelvärdet på 160 nås för första gången vid cirka 5 minuter och båda motorerna når tröskelvärdet ungefär vid 8 minuter.

Simuleringen kör igenom den fullständiga datauppsättningen i 148 cykler och bestämmer den slutliga återstående användbara livslängden och cykelvärdena.

Du kan stoppa simuleringen när du vill, men om du klickar på **Starta simulering** spelas simuleringen upp igen från början av datauppsättningen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill utforska ytterligare låter du lösningsacceleratorn för förutsägande underhåll förbli distribuerad.

Om du inte längre behöver Solution Accelerator tar du bort den från sidan [etablerade lösningar](https://www.azureiotsolutions.com/Accelerators#dashboard) genom att markera den och sedan klicka på **ta bort lösning**:

![Ta bort lösningen](media/quickstart-predictive-maintenance-deploy/deletesolution.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat lösningsacceleratorn för förutsägande underhåll och kört en simulering.

Fortsätt till nästa artikel om du vill lära dig mer om lösningsacceleratorn och de simulerade flygplansmotorerna.

> [!div class="nextstepaction"]
> [Översikt över lösningsacceleratorn Förutsägande underhåll](iot-accelerators-predictive-walkthrough.md)
