---
title: Så här identifierar och löser du problem i Azure våren Cloud
description: Lär dig att själv diagnostisera och lösa problem i Azure våren-molnet.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9d572545f589475dc2b460db90329fe8b0a838c2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904294"
---
# <a name="how-to-self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>Så här identifierar och löser du problem i Azure våren Cloud

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Azure våren Cloud Diagnostics är en interaktiv upplevelse som hjälper dig att felsöka din app. Ingen konfiguration krävs. När du hittar problem pekar Azure våren Cloud Diagnostics upp vad som är fel och vägleder dig till information som hjälper dig att felsöka och lösa problemet.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här kursen behöver du:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* En distribuerad Azure våren Cloud Service-instans. Följ vår [snabb start för att distribuera en app via Azure CLI](spring-cloud-quickstart.md) för att komma igång.
* Minst ett program som redan har skapats i din tjänst instans.

## <a name="navigate-to-the-diagnostics-page"></a>Gå till sidan diagnostik
1. Logga in på Azure Portal.
2. Gå till **översikts** sidan för Azure våren-molnet.
3. Öppna **diagnostisera och lös problem** på menyn på vänster sida av sidan.

 ![Diagnostisera, lös dialog](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

 ## <a name="search-logged-issues"></a>Sök efter loggade problem
För att hitta ett problem kan du antingen söka genom att skriva ett nyckelord eller klicka på lösnings grupp för att utforska alla i den kategorin.

 ![Sökningsproblem](media/spring-cloud-diagnose/search-detectors.png)

Val av **konfigurations serverns hälso kontroll**, **konfigurations serverns hälso status**eller **uppdaterings historiken för konfigurations servern** visar olika resultat.

![Problem alternativ](media/spring-cloud-diagnose/detectors-options.png)

Hitta din mål detektor och klicka på den för att köra. En sammanfattning av diagnostik visas när du har kört detektorn. Du kan välja **Visa fullständig rapport** för att kontrol lera diagnostikinformation eller klicka på knappen Visa knapp på **panelen** för att gå tillbaka till detektor listan.

 ![Sammanfattning av diagnostik](media/spring-cloud-diagnose/summary-diagnostics.png)

På sidan diagnostikinformation kan du ändra det diagnostiska tidsintervallet med kontrollanten i det övre högra hörnet. Om du vill se fler mått eller loggar växlar du varje diagnostik. Det kan finnas en fördröjning på 15 minuter för mått och loggar.

 ![Diagnostikinformation](media/spring-cloud-diagnose/diagnostics-details.png)

Vissa resultat innehåller relaterad dokumentation.

 ![Relaterad information](media/spring-cloud-diagnose/related-details.png)

## <a name="next-steps"></a>Nästa steg
* [Övervaka Spring Cloud-resurser med aviseringar och åtgärdsgrupper](spring-cloud-tutorial-alerts-action-groups.md)
* [Säkerhetskontroller för Azure Spring Cloud-tjänsten](spring-cloud-concept-security-controls.md)