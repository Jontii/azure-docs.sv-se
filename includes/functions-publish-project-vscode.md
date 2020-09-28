---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: f4e58f4f510450db13ae13d3beecba4d55e766bf
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91408599"
---
## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

I det här avsnittet ska du skapa en Function-app och relaterade resurser i din Azure-prenumeration och sedan distribuera din kod.

> [!IMPORTANT]
> Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure.


1. Välj Azure-ikonen i aktivitets fältet och välj sedan knappen **distribuera till Function-appen** i avsnittet **Azure:** functions.

    ![Publicera ditt projekt till Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Ange följande information i prompten:

    - **Välj mapp**: Välj en mapp från din arbets yta eller bläddra till en mapp som innehåller din Function-app. Du ser inte det här om du redan har en giltig Function-app öppen.

    - **Välj prenumeration**: Välj den prenumeration som ska användas. Du ser inte det här om du bara har en prenumeration.

    - **Välj Funktionsapp i Azure**: Välj `- Create new Function App` . (Välj inte `Advanced` alternativet, som inte beskrivs i den här artikeln.)
      
    - **Ange ett globalt unikt namn för Function-appen**: Ange ett namn som är giltigt i en URL-sökväg. Namnet du skriver verifieras för att säkerställa att det är unikt i Azure Functions.
    
    ::: zone pivot="programming-language-python"
    - **Välj en körning**: Välj den version av python som du har kört lokalt. Du kan `python --version` kontrol lera din version med hjälp av kommandot.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    - **Välj en körnings miljö**: Välj den version av Node.js som du har kört lokalt. Du kan `node --version` kontrol lera din version med hjälp av kommandot.
    ::: zone-end

    - **Välj en plats för nya resurser**: om du vill ha bättre prestanda väljer du en [region](https://azure.microsoft.com/regions/) nära dig. 
    
1.  När det är slutfört skapas följande Azure-resurser i din prenumeration med hjälp av namn baserat på ditt funktions program namn:
    
    - En resurs grupp, som är en logisk behållare för relaterade resurser.
    - Ett standard Azure Storage-konto som upprätthåller tillstånd och annan information om dina projekt.
    - En förbruknings plan som definierar den underliggande värden för din server lös Function-app. 
    - En Function-app som tillhandahåller miljön för att köra funktions koden. Med en Function-app kan du gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser inom samma värd plan.
    - En Application Insights instans som är ansluten till Function-appen, som spårar användningen av din server lös funktion.

    Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. 
    
1. Välj **Visa utdata** i det här meddelandet för att Visa skapande-och distributions resultaten, inklusive de Azure-resurser som du har skapat. Om du saknar meddelandet väljer du klock ikonen i det nedre högra hörnet för att se den igen.

    ![Skapa fullständig avisering](media/functions-publish-project-vscode/function-create-notifications.png)
