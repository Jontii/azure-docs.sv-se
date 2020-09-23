---
title: Köra Jupyter Notebooks på arbetsytan
titleSuffix: Azure Machine Learning
description: Lär dig hur du kör en Jupyter Notebook utan att lämna arbets ytan i Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 06/27/2020
ms.openlocfilehash: b310f195477f4cba83ef30c6fb5ea2fd24c4df9a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897384"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Köra Jupyter Notebooks på arbetsytan


Lär dig hur du kör dina Jupyter-anteckningsböcker direkt i din arbets yta i Azure Machine Learning Studio. Även om du kan starta [Jupyter](https://jupyter.org/) eller [JupyterLab](https://jupyterlab.readthedocs.io)kan du även redigera och köra dina antecknings böcker utan att lämna arbets ytan.

Se hur du kan:

* Skapa Jupyter-anteckningsböcker i din arbets yta
* Köra ett experiment från en bärbar dator
* Ändra Notebook-miljön
* Hitta information om de beräknings instanser som används för att köra dina antecknings böcker

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://aka.ms/AMLFree) innan du börjar.
* En Machine Learning-arbetsyta. Se [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a> Skapa antecknings böcker

I arbets ytan Azure Machine Learning skapar du en ny Jupyter-anteckningsbok och börjar arbeta. Den nyligen skapade antecknings boken lagras i standard lagrings ytan. Den här antecknings boken kan delas med alla som har åtkomst till arbets ytan. 

Så här skapar du en ny antecknings bok: 

1. Öppna din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com).
1. På den vänstra sidan väljer du **antecknings böcker**. 
1. Välj ikonen  **Skapa ny fil** ovanför listan **användarfiler** i avsnittet **Mina filer** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Skapa en ny fil":::

1. Ge filen ett namn. 
1. För Jupyter Notebook filer väljer du **Notebook** som filtyp.
1. Välj en fil katalog.
1. Välj **Skapa**.

Du kan också skapa textfiler.  Välj **text** som filtyp och Lägg till tillägget i namnet (till exempel myfile.py eller myfile.txt)  

Du kan också ladda upp mappar och filer, inklusive antecknings böcker, med verktygen överst på sidan antecknings böcker.  Antecknings böcker och de flesta text fil typer visas i avsnittet för förhands granskning.  Ingen förhands granskning är tillgänglig för de flesta andra filtyper.

> [!IMPORTANT]
> Innehåll i antecknings böcker och skript kan potentiellt läsa data från dina sessioner och komma åt data utan din organisation i Azure.  Läs bara in filer från betrodda källor. Mer information finns i [metod tips för säker kod](concept-secure-code-best-practice.md#azure-ml-studio-notebooks).

### <a name="clone-samples"></a>Klona exempel

Din arbets yta innehåller en **exempel** -mapp med antecknings böcker utformade för att hjälpa dig att utforska SDK och fungerar som exempel för dina egna Machine Learning-projekt.  Du kan klona de här antecknings böckerna till din egen mapp på arbets ytans lagrings behållare.  

Ett exempel finns i [Självstudier: skapa ditt första ml experiment](tutorial-1st-experiment-sdk-setup.md#azure).

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> Använd filer från git och version mina filer

Du kan komma åt alla git-åtgärder med hjälp av ett terminalfönster. Alla git-filer och mappar kommer att lagras i fil systemet för arbets ytan.

> [!NOTE]
> Lägg till dina filer och mappar var som helst under mappen **~/CloudFiles/Code/Users** så att de visas i alla dina Jupyter-miljöer.

För att få åtkomst till terminalen:

1. Öppna din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com).
1. På den vänstra sidan väljer du **antecknings böcker**.
1. Välj en antecknings bok som finns i avsnittet **användarfiler** på den vänstra sidan.  Om du inte har några antecknings böcker där måste du först [skapa en antecknings bok](#create)
1. Välj ett **beräknings** mål eller skapa ett nytt och vänta tills det körs.
1. Välj ikonen **Öppna Terminal** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Öppna Terminal":::

1. Om du inte ser ikonen väljer du **...** till höger om beräknings målet och väljer sedan **Öppna Terminal**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Öppna Terminal från...":::


Lär dig mer om [att klona git-databaser till fil systemet för arbets ytan](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).


### <a name="share-notebooks-and-other-files"></a>Dela antecknings böcker och andra filer

Kopiera och klistra in URL: en för att dela en antecknings bok eller fil.  Endast andra användare av arbets ytan har åtkomst till den här URL: en.  Läs mer om hur [du beviljar åtkomst till din arbets yta](how-to-assign-roles.md).

## <a name="edit-a-notebook"></a>Redigera en bärbar dator

Om du vill redigera en antecknings bok öppnar du en antecknings bok i avsnittet **användarfiler** i din arbets yta. Klicka på cellen som du vill redigera. 

Du kan redigera antecknings boken utan att ansluta till en beräknings instans.  När du vill köra cellerna i antecknings boken väljer eller skapar du en beräknings instans.  Om du väljer en stoppad beräknings instans kommer den automatiskt att starta när du kör den första cellen.

När en beräknings instans körs kan du också använda kod komplettering, som drivs av [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense), i valfri python-anteckningsbok.

Du kan också starta Jupyter eller JupyterLab från antecknings bokens verktygsfält.  Azure Machine Learning tillhandahåller inte uppdateringar och korrigerar buggar från Jupyter eller JupyterLab eftersom de är produkter med öppen källkod utanför gränserna för Microsoft Support.

### <a name="use-intellisense"></a>Använd IntelliSense

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) är ett stöd för kod komplettering som innehåller ett antal funktioner: list medlemmar, parameter information, snabb information och fullständig ord. Dessa funktioner hjälper dig att lära dig mer om den kod som du använder, håller reda på de parametrar som du skriver och lägger till anrop till egenskaper och metoder med bara några få tangenttryckningar.  

När du skriver kod använder du Ctrl + blank steg för att utlösa IntelliSense.

### <a name="clean-your-notebook-preview"></a>Rensa din bärbara dator (för hands version)

> [!IMPORTANT]
> Funktionen samla in finns för närvarande i offentlig för hands version.
> För hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I samband med att du skapar en antecknings bok slutar du normalt med celler som du använde för data utforskning eller fel sökning. Med funktionen *samla in* kan du skapa en ren bärbar dator utan dessa främmande celler.

1. Kör alla dina Notebook-celler.
1. Välj den cell som innehåller den kod som du vill att den nya bärbara datorn ska köra. Till exempel den kod som skickar ett experiment eller kanske koden som registrerar en modell.
1. Välj den **insamlings** ikon som visas i cell verktygsfältet.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Skärm bild: Välj ikonen samla in":::
1. Ange namnet på den nya "insamlade" notebook-anteckningsboken.  

Den nya antecknings boken innehåller bara kod celler, med alla celler som krävs för att skapa samma resultat som den cell som du har valt för att samla in.

### <a name="save-and-checkpoint-a-notebook"></a>Spara och Checkpoint en bärbar dator

Azure Machine Learning skapar en kontroll punkts fil när du skapar en *ipynb*-   fil.

I verktygsfältet Notebook väljer du menyn och sedan ** &gt; Spara och kontrol kontroll punkt** för att manuellt spara antecknings boken och lägger till en kontroll punkts fil som är associerad med antecknings boken.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Skärm bild av verktyget Spara i anteckningsbok-verktygsfältet":::

Alla antecknings böcker sparas automatiskt var 30: e sekund.Spara automatiskt uppdateringar endast den ursprungliga *ipynb*   -filen, inte kontroll punkts filen.
 
Välj **kontroll punkter** i Notebook-menyn för att skapa en namngiven kontroll punkt och återställa antecknings boken till en sparad kontroll punkt.


### <a name="useful-keyboard-shortcuts"></a>Användbara kortkommandon

|Tangentbord  |Action  |
|---------|---------|
|SKIFT + RETUR     |  Köra en cell       |
|CTRL + blank steg | Aktivera IntelliSense |
|Ctrl + M (Windows)     |  Aktivera/Inaktivera fliken svällning i antecknings boken.       |
|CTRL + SHIFT + M (Mac & Linux)     |    Aktivera/Inaktivera fliken svällning i antecknings boken.     |
|TABB (när TABB-trap är aktiverat) | Lägg till ett "\t"-Character (indrag)
|TABB (när TABB-trap är inaktive rad) | Ändra fokus till nästa fokus objekt (knappen Ta bort cell, knappen Kör osv.)

## <a name="delete-a-notebook"></a>Ta bort en notebook-fil

Du *kan inte* ta bort **exempel** antecknings böckerna.  Dessa antecknings böcker ingår i Studio och uppdateras varje gången ett nytt SDK publiceras.  

Du *kan* ta bort antecknings böcker för **användar filer** på något av följande sätt:

* I Studio väljer du **...** i slutet av en mapp eller fil.  Se till att använda en webbläsare som stöds (Microsoft Edge, Chrome eller Firefox).
* I valfri antecknings boks verktygsfält väljer du [**Öppna Terminal**](#terminal)  för att få åtkomst till terminalfönstret för beräknings instansen.
* I antingen Jupyter eller JupyterLab med sina verktyg.

## <a name="run-an-experiment"></a>Köra ett experiment

Om du vill köra ett experiment från en antecknings bok ansluter du först till en [beräknings instans](concept-compute-instance.md)som körs. Om du inte har en beräknings instans använder du följande steg för att skapa ett: 

1. Välj **+** i antecknings bokens verktygsfält. 
2. Namnge beräkningen och välj en storlek för den **virtuella datorn**. 
3. Välj **Skapa**.
4. Beräknings instansen är ansluten till antecknings boken automatiskt och du kan nu köra cellerna.

Endast du kan se och använda de beräknings instanser som du skapar.  Dina **användarfiler** lagras separat från den virtuella datorn och delas mellan alla beräknings instanser i arbets ytan.

### <a name="view-logs-and-output"></a>Visa loggar och utdata

Använd [Notebook-widgetar](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true) för att visa förloppet för körningen och loggarna. En widget är asynkron och innehåller uppdateringar tills inlärningen är klar. Azure Machine Learning-widgetar stöds också i Jupyter och JupterLab.

## <a name="change-the-notebook-environment"></a>Ändra Notebook-miljön

I verktygsfältet Notebook kan du ändra den miljö som antecknings boken körs på.  

Dessa åtgärder ändrar inte antecknings läget eller värdena för några variabler i antecknings boken:

|Action  |Resultat  |
|---------|---------| --------|
|Stoppa kärnan     |  Stoppar eventuell cell som körs. Om du kör en cell startas kerneln om automatiskt. |
|Navigera till en annan arbets yta-sektion     |     Celler som körs stoppas. |

Dessa åtgärder återställer antecknings bokens tillstånd och återställer alla variabler i antecknings boken.

|Action  |Resultat  |
|---------|---------| --------|
| Ändra kernel | Notebook använder ny kernel |
| Ändra beräkning    |     Antecknings boken använder automatiskt den nya beräkningen. |
| Återställ beräkning | Startar igen när du försöker köra en cell |
| Stoppa beräkning     |    Inga celler kommer att köras  |
| Öppna antecknings boken i Jupyter eller JupyterLab     |    Antecknings boken öppnas på en ny flik.  |

### <a name="add-new-kernels"></a>Lägg till nya kärnor

Antecknings boken hittar automatiskt alla Jupyter-kernels som är installerade på den anslutna beräknings instansen.  Så här lägger du till en kernel till beräknings instansen:

1. Välj [**öppna terminalen**](#terminal) i antecknings bokens verktygsfält.
1. Använd terminalfönstret för att skapa en ny miljö.  Koden nedan skapar till exempel `newenv` :
    ```shell
    conda create --name newenv
    ```
1. Aktivera miljön.  Till exempel när du har skapat `newenv` :

    ```shell
    conda activate newenv
    ```
1. Installera pip-och ipykernel-paketet i den nya miljön och skapa en kernel för det Conda-avsnittet

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Alla [tillgängliga Jupyter-kernels](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) kan installeras.

### <a name="status-indicators"></a>Status indikatorer

En indikator bredvid List rutan **Compute** visar dess status.  Statusen visas också i själva List rutan.  

|Färg |Beräknings status |
|---------|---------| 
| Green | Beräkning som körs |
| Red |Det gick inte att beräkna | 
| Svarta | Beräkning stoppad |
|  Ljusblå |Beräkning skapa, starta, starta om, konfigurera |
|  Mörkgrå |Ta bort beräkning, stoppa |

En indikator bredvid List rutan **kernel** visar dess status.

|Färg |Kernel-status |
|---------|---------|
|  Green |Kernel-ansluten, inaktiv, upptagen|
|  Mörkgrå |Kernel inte ansluten |

## <a name="find-compute-details"></a>Hitta beräknings information 

Hitta information om dina beräknings instanser på **beräknings** sidan i [Studio](https://ml.azure.com).

## <a name="next-steps"></a>Nästa steg

* [Kör ditt första experiment](tutorial-1st-experiment-sdk-train.md)
* [Säkerhetskopiera fil lagringen med ögonblicks bilder](../storage/files/storage-snapshots-files.md)
