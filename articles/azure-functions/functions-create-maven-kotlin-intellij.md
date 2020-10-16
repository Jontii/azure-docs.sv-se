---
title: Skapa en Azure-funktion med Kotlin och IntelliJ
description: Lär dig hur du skapar och publicerar en enkel HTTP-utlöst, Server lös app på Azure med Kotlin och IntelliJ.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: 09dd868dc9e05241943899654d7c8bb427a8f268
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104843"
---
# <a name="quickstart-create-your-first-http-triggered-function-with-kotlin-and-intellij"></a>Snabb start: skapa din första HTTP-utlöst funktion med Kotlin och IntelliJ

Den här artikeln visar hur du skapar ett [Server](https://azure.microsoft.com/overview/serverless-computing/) lös funktions projekt med INTELLIJ-idé och Apache Maven. Det visar också hur du lokalt felsöker din funktions kod i Integrated Development Environment (IDE) och sedan distribuerar funktions projektet till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

Om du vill utveckla en funktion med Kotlin och IntelliJ installerar du följande program vara:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK), version 8
- [Apache maven](https://maven.apache.org), version 3,0 eller senare
- [INTELLIJ idé](https://www.jetbrains.com/idea/download), community-eller Ultimate-versioner med maven
- [Azure CLI](/cli/azure)
- [Version 2. x](functions-run-local.md#v2) av Azure Functions Core tools. Det ger en lokal utvecklings miljö för att skriva, köra och felsöka Azure Functions.

> [!IMPORTANT]
> Den JAVA_HOME miljövariabeln måste anges till installations platsen för JDK för att slutföra stegen i den här artikeln.

## <a name="create-a-functions-project"></a>Skapa ett Functions-projekt

1. I IntelliJ idé väljer du **Skapa nytt projekt**.  
1. I fönstret **nytt projekt** väljer du **maven** i det vänstra fönstret.
1. Markera kryss rutan **skapa från archetype** och välj sedan **Lägg till archetype** för [Azure-Functions-Kotlin-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. I fönstret **Lägg till archetype** fyller du i fälten enligt följande:
    - _Multiport: com_. Microsoft. Azure
    - _ArtifactId_: Azure-Functions-Kotlin-archetype
    - _Version_: Använd den senaste versionen från [den centrala databasen](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype) 
     ![ skapa ett Maven-projekt från archetype i IntelliJ-idén](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Välj **OK**och välj sedan **Nästa**.
1. Ange information om det aktuella projektet och välj **Slutför**.

Maven skapar projektfilerna i en ny mapp med samma namn som _ArtifactId_ -värdet. Projektets genererade kod är en enkel [http-utlöst](./functions-bindings-http-webhook.md) funktion som upprepar texten i den Utlös ande http-begäran.

## <a name="run-functions-locally-in-the-ide"></a>Köra funktioner lokalt i IDE

> [!NOTE]
> Kontrol lera att du har installerat [Azure Functions Core tools, version 2](functions-run-local.md#v2), för att köra och felsöka funktioner lokalt.

1. Importera ändringar manuellt eller aktivera [automatisk import](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Öppna verktygsfältet **Maven-projekt** .
1. Expandera **livs cykel**och öppna sedan **paket**. Lösningen har skapats och paketerats i en nyligen skapad mål katalog.
1. Expandera **plugin**-program  >  **Azure-Functions** och öppna **Azure-Functions: kör** för att starta Azure Functions lokala körnings miljön.  
  ![Maven-verktygsfält för Azure Functions](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Stäng dialog rutan Kör när du är klar med att testa din funktion. Endast en funktions värd kan vara aktiv och köras lokalt i taget.

## <a name="debug-the-function-in-intellij"></a>Felsöka funktionen i IntelliJ

1. Starta funktions värden i fel söknings läge genom att lägga till **-DenableDebug** som argument när du kör din funktion. Du kan antingen ändra konfigurationen i [maven-målen](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) eller köra följande kommando i ett terminalfönster:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Det här kommandot gör att funktions värden öppnar en fel söknings port vid 5005.

1. På menyn **Kör** väljer du **Redigera konfigurationer**.
1. Välj **(+)** om du vill lägga till en **fjärr anslutning**.
1. Fyll i fälten _namn_ och _Inställningar_ och välj sedan **OK** för att spara konfigurationen.
1. Efter installationen väljer du **felsök < Fjärrkonfigurationens namn >** eller trycker på Shift + F9 på tangent bordet för att starta fel sökningen.

   ![Fel söknings funktioner i IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Stoppa fel söknings programmet och den process som körs när du är klar. Endast en funktions värd kan vara aktiv och köras lokalt i taget.

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

1. Innan du kan distribuera din funktion till Azure måste du [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Distribuera din kod till en ny funktion med hjälp av `azure-functions:deploy` maven-målet. Du kan också välja alternativet **Azure-Functions: Deploy** i fönstret maven projects.

   ```
   mvn azure-functions:deploy
   ```

1. Hitta URL: en för din funktion i Azure CLI-utdata när funktionen har distribuerats.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat din första Kotlin-funktion till Azure kan du läsa mer i [Java Functions Developer-Guide](functions-reference-java.md) för mer information om hur du utvecklar Java-och Kotlin-funktioner.
- Lägg till ytterligare funktioner med olika utlösare i projektet med `azure-functions:add` maven-målet.