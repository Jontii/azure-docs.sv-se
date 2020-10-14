---
title: Anslut till Azure Analysis Services med Power BI | Microsoft Docs
description: Lär dig hur du ansluter till en Azure Analysis Services-server med hjälp av Power BI. När du är ansluten kan användarna utforska modell data.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bb1781394e0091d8358570c2eaa07ca97297a6ad
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92013788"
---
# <a name="connect-with-power-bi"></a>Anslut med Power BI

När du har skapat en server i Azure och distribuerat en tabell modell till den, är användare i din organisation redo att ansluta och börja utforska data. 

> [!TIP]
> Se till att använda den senaste versionen av [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Ansluta i Power BI Desktop

1. I Power BI Desktop klickar du på **Hämta data**  >  **Azure**  >  **Azure Analysis Services Database**.

2. I **Server**anger du Server namnet. Se till att ta med den fullständiga URL: en. till exempel asazure://westcentralus.asazure.windows.net/advworks.

3. I **databasen**, om du känner till namnet på den tabell modell databas eller det perspektiv som du vill ansluta till, klistrar du in den här. Annars kan du lämna det här fältet tomt och välja en databas eller ett perspektiv senare.

4. Välj ett anslutnings alternativ och tryck sedan på **Anslut**. 

    Båda alternativen för att **ansluta Live** och **Importera** stöds. Vi rekommenderar dock att du använder Live-anslutningar eftersom import läget har vissa begränsningar. främst kan Server prestanda påverkas under importen. Om modellen ska uppdateras i Power BI-tjänst gäller även inställningen **Tillåt åtkomst från Power BI** endast när du väljer **Anslut Live**.

5. Ange dina inloggnings uppgifter om du uppmanas att göra det. 

   > [!NOTE]
   > Eng ång slö sen ord (eng ång slö sen ord) stöds inte. 

6. I **Navigator**expanderar du-servern och väljer sedan den modell eller det perspektiv som du vill ansluta till och klickar sedan på **Anslut**. Klicka på en modell eller ett perspektiv för att visa alla objekt för vyn.

    Modellen öppnas i Power BI Desktop med en tom rapport i rapportvyn. I listan Fält visas alla modellobjekt som inte är dolda. Anslutningsstatus visas i det nedre högra hörnet.

## <a name="connect-in-power-bi-service"></a>Anslut i Power BI (tjänst)

1. Skapa en Power BI Desktop-fil som har en Live-anslutning till din modell på servern.
2. I [Power BI](https://powerbi.microsoft.com)klickar du på **Hämta**  >  **datafiler**och letar upp och väljer din. pbix-fil.

## <a name="see-also"></a>Se även
[Anslut till Azure Analysis Services](analysis-services-connect.md)   
[Klientbibliotek](/analysis-services/client-libraries?view=azure-analysis-services-current)