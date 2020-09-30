---
title: 'Snabb start: skapa en Unity Android-app'
description: I den här snabbstarten lär du dig att skapa en Android-app med Unity med hjälp av Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 09/29/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c84ebfe9752b8594e97c7c72d706ee25c4852f6e
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91538168"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Snabb start: skapa en enhets-Android-app med avstånds ankare för Azure

Den här snabb starten beskriver hur du skapar en Unity Android-app med hjälp av [Azure spatiala ankare](../overview.md). Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en ARCore Android-app som skapats med Unity och som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Förbereda Unity-bygginställningar
> * Konfigurera konto-ID och kontonyckel för Spatial Anchors
> * Exportera Android Studio-projektet
> * Distribuera och köra på en Android-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har följande så att du kan utföra den här snabbstarten:

- En Windows-eller macOS-dator med <a href="https://unity3d.com/get-unity/download" target="_blank">unity 2019,4 (LTS)</a>, inklusive **stöd för Android-build** med **Android SDK & NDK-verktyg** och **openjdk** -moduler.
  - Om du kör på Windows, behöver du även <a href="https://git-scm.com/download/win" target="_blank">git för Windows</a> och <a href="https://git-lfs.github.com/">git LF</a>.
  - Om du kör på macOS ska du Hämta git installerat via HomeBrew. Ange följande kommando i en enskild rad i terminalen: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . Kör sedan `brew install git` och `brew install git-lfs` .
- En <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">utvecklaraktiverad</a> och <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-kapabel</a> Android-enhet.
  - Ytterligare enhets driv rutiner kan krävas för att datorn ska kunna kommunicera med din Android-enhet. Mer ytterligare information och instruktioner finns [här](https://developer.android.com/studio/run/device.html).

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Ladda ned och öppna exempel projektet Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-android-studio-project"></a>Exportera Android Studio-projektet

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Välj enheten i **Kör enhet** och välj sedan **skapa och kör**. Du uppmanas att spara en `.apk` fil som du kan välja namn för.

I appen väljer du **BasicDemo** med pilarna och trycker sedan på **Go!** knappen för att köra demonstrationen. Följ anvisningarna för att placera och återkalla en fäst punkt.

![Skärm bild 1 ](./media/get-started-unity-android/screenshot-1.jpg)
 ![ skärmdump 2, ](./media/get-started-unity-android/screenshot-2.jpg)
 ![ skärm bild 3](./media/get-started-unity-android/screenshot-3.jpg)

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

## <a name="troubleshooting"></a>Felsökning

### <a name="rendering-issues"></a>Åter givnings problem

Om du kör appen, om du inte ser kameran som bakgrund (t. ex. en tom, blå eller annan struktur), måste du förmodligen importera till gångar i enhets gruppen. Stoppa appen. Från den översta menyn i Unity väljer du **Assets -> Reimport all** (Tillgångar -> Återimportera alla). Kör sedan appen igen.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudie: dela spatiala ankare mellan enheter](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Gör så här: Konfigurera Azure spatiala ankare i ett Unity-projekt](../how-tos/setup-unity-project.md)
