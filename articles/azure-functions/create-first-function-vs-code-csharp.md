---
title: Skapa en C#-funktion med Visual Studio Code-Azure Functions
description: Lär dig hur du skapar en C#-funktion och sedan publicerar det lokala projektet till Server lös värd i Azure Functions med hjälp av Azure Functions tillägget i Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 444dff214a70a47ced127a3ca2533c20ea2a779a
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93425138"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>Snabb start: skapa en C#-funktion i Azure med Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

I den här artikeln använder du Visual Studio Code för att skapa en C#-baserad funktion i C#-klassen som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

Det finns också en [CLI-baserad version](create-first-function-cli-csharp.md) av den här artikeln.

## <a name="configure-your-environment"></a>Konfigurera din miljö

Innan du börjar bör du kontrol lera att du har följande krav på plats:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Node.js](https://nodejs.org/)som krävs av Windows för NPM. Endast [aktiva LTS-och underhålls LTS-versioner](https://nodejs.org/about/releases/). Använd `node --version` kommandot för att kontrol lera din version.
    Krävs inte för lokal utveckling på macOS och Linux.

+ [Visual Studio Code](https://code.visualstudio.com/) på en av de [plattformar som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) för Visual Studio Code.  

+ [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code.

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Skapa ditt lokala projekt

I det här avsnittet använder du Visual Studio Code för att skapa ett lokalt Azure Functions projekt i C#. Senare i den här artikeln ska du publicera funktions koden till Azure.

1. Välj Azure-ikonen i aktivitets fältet och välj sedan ikonen **Skapa nytt projekt** i avsnittet **Azure: Functions** .

    ![Välj Skapa ett nytt projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Välj en katalog plats för projekt arbets ytan och välj **Välj**.

    > [!NOTE]
    > De här stegen har utformats för att slutföras utanför en arbets yta. Välj i det här fallet inte en projektmapp som ingår i en arbetsyta.

1. Ange följande information i prompten:

    + **Välj ett språk för ditt funktions projekt** : Välj `C#` .

    + **Välj en mall för projektets första funktion** : Välj `HTTP trigger` .

    + **Ange ett funktions namn** : typ `HttpExample` .

    + **Ange ett namn område** : typ `My.Functions` .

    + **Autentiseringsnivå** : Välj `Anonymous` , vilket gör att vem som helst kan anropa funktions slut punkten. Mer information om autentiseringsnivå finns i [Authorization Keys](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Välj hur du vill öppna projektet** : Välj `Add to workspace` .

1. Med hjälp av den här informationen genererar Visual Studio Code ett Azure Functions-projekt med en HTTP-utlösare. Du kan visa de lokala projektfilerna i Utforskaren. Mer information om filer som skapas finns i [genererade projektfiler](functions-develop-vs-code.md#generated-project-files).

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

När du har kontrollerat att funktionen fungerar korrekt på den lokala datorn är det dags att använda Visual Studio Code för att publicera projektet direkt till Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Nästa steg

Du har nu använt Visual Studio Code för att skapa en funktionsapp med en enkel HTTP-utlöst funktion. I nästa artikel expanderar du den funktionen genom att lägga till en utgående bindning. Den här bindningen skriver strängen från HTTP-begäran till ett meddelande i en Azure Queue Storage-kö. 

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
